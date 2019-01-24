---
layout: post
title:  "mysql递归查询子节点，及java后台访问存储过程"
date:   2018-09-25
desc: "mysql递归查询子节点，及java后台访问存储过程"
keywords: "java web"
categories: [javaweb]
tags: [java web, java访问存储过程]
---


# mysql递归查询子节点，及java后台访问存储过程

### MySQL实现树状所有子节点查询。

**业务描述**：一个产成品是由很多模块组成，每个模块又由许多其他小模块组成，小模块又由... 最小模块才是由基本的元器件组成，现在要列出生产1000台该产品所需购买的所有基本元器件清单。这个过程因为不知道嵌套次数，故只能用函数递归。


在MySQL中如果是有限的层次，我们事先知道树的深度，那我们就可以直接通过left join 来实现，只是代码会繁琐一点，实现功能是没有问题的。但很多时候我们无法控制树的深度。

mysql存储过程。主要用到了**递归方式**。while,group_concat(),find_in_set()

		CREATE DEFINER=`ww`@`%` PROCEDURE `pro_orderplan_getbomdetail`(in orgbomid varchar(255), bomnum int(10))
		BEGIN
			
		DECLARE FItemIDs varchar(4000) default '';   
		DECLARE FInterIDs varchar(4000) default '';   
		DECLARE res varchar(1000) default '';   
		
		set FInterIDs =(select GROUP_CONCAT(FInterID) from ws_k3_bom where FBOMNumber = orgbomid);
		
		WHILE FInterIDs is not null  do   
				SET res = concat(res,',',FInterIDs);
		    SET FItemIDs =(SELECT GROUP_CONCAT(FItemID) FROM ws_k3_bomchild WHERE FIND_IN_SET(FInterID,FInterIDs));   
		    IF FItemIDs is not null THEN    
		        SET FInterIDs = (select GROUP_CONCAT(FInterID) from ws_k3_bom where FIND_IN_SET(FItemID,FItemIDs));  
		    END IF;   
		END WHILE;
		
		select a.*,b.FFixLeadTime,IFNULL(c.FQty_Stock_total,'0.0') as FQty_Stock_total,IFNULL(c.FQty_PO_total,'0.0') as FQty_PO_total,
		IFNULL(c.FQty_PR_total,'0.0') as FQty_PR_total,IFNULL(c.FQty_Need_total,'0.0') as FQty_Need_total,IFNULL(c.FQty_Remain_total,'0.0') as FQty_Remain_total from 
		(select a.*,(a.FQty*bomnum) as usesnums  from 
		(select FInterID,FItemID,FNumber,FName,SUM(FQty) as FQty,FModel,CONCAT(FItemID,'0') as td, '' as type FROM ws_k3_bomchild where FIND_IN_SET(FInterID,res) GROUP BY FInterID, FItemID
		union
		select a.FInterID,b.FSubsItemID,c.FNumber,c.FName,a.FQty,c.FModel,CONCAT(a.FItemID,b.FPriorityID) as td, '替代' as type from 
		(select FInterID,FItemID,FNumber,FName,SUM(FQty) as FQty,FModel FROM ws_k3_bomchild where FIND_IN_SET(FInterID,res) GROUP BY FInterID, FItemID) a
		LEFT JOIN ws_k3_subsitem b on a.FItemID = b.FItemID and (a.FInterID = b.FApplicableBOM or b.FApplicableBOM = 0) 
		LEFT JOIN ws_k3_icitem c on b.FSubsItemID = c.fitemid where b.FSubsItemID is not NULL GROUP BY b.FSubsItemID ) a
		ORDER BY a.td) a
		LEFT JOIN ws_k3_icitem b on a.FItemID = b.FItemID 
		LEFT JOIN ws_orderplan_MRP_Material_Need_Info c on a.FNumber = c.FNumber ORDER BY a.td;
		
		END

### java后台访问存储过程
数据连接方式是spring提供的JdbcTemplate。
java后台从存储过程中获取数据的写法如下：

	public String gridDataProdetil(String params){
			String sqlTemp = "";
			try{
				JSONObject jsonObj = JSONObject.fromObject(params);
				final String FBOMNumber = jsonObj.get("FBOMNumber").toString();
				final String FQty = jsonObj.get("FQty").toString();
					String result = (String) jdbcTemplate.execute(  
							 new CallableStatementCreator(){   
						        public  CallableStatement createCallableStatement(Connection con)  throws  SQLException {  
				                    String storedProc = "{call pro_orderplan_getbomdetail(?,?)}";// 调用的存储过程  
				                    CallableStatement cs = con.prepareCall(storedProc);  
				                    cs.setString(1, FBOMNumber);// 设置输入参数的值  
				                    cs.setString(2, FQty);// 设置输入参数的值  
				                    return cs;
							    }
			                 },new CallableStatementCallback(){   
			    		        public Object doInCallableStatement(CallableStatement cs)  throws  SQLException,DataAccessException {   
				                    List<String> list = new ArrayList(); 
				                    JSONObject object = null;
				        			JSONObject allResult = new JSONObject();
				        			JSONArray array = new JSONArray();
				                    boolean exist = cs.execute();  
				                    
				                    ResultSet resultSet = cs.getResultSet();  
				                    if (exist) {  
				                        while (resultSet.next()) {  
					                        object = new JSONObject();
				            				object.put("FInterID", resultSet.getString(1));
				            				object.put("FItemID", resultSet.getString(2));
				            				object.put("FNumber", resultSet.getString(3));
				            				object.put("FName", resultSet.getString(4));
				            				object.put("FQty", Double.parseDouble(resultSet.getString(5) == "" ? "0" : resultSet.getString(5)));
				            				object.put("FModel", resultSet.getString(6));
				            				object.put("td", resultSet.getString(7));
				            				object.put("FQty_PR_total", resultSet.getDouble(8));
				            				array.add(object);
				                        }  
				                    }  
				                    allResult.put("rows", "");
				            		allResult.put("values", array);
				            		return allResult.toString();
			    		         }
				          }); 
					return result;
				
			}catch(Exception e){
				e.printStackTrace();
				throw new RuntimeException("运行时出错！");
			}
		}
