---
layout: post
title:  "iReport&jasper打印Java实现"
date:   2017-10-25
desc: "iReport&jasper打印Java实现"
keywords: "java,iReport，jasper"
categories: [Java]
tags: [java, iReport,jasper]
---

##IReport与class关联

* 工具--> 选项 --> ireport--> classPath--> 选择相应的classes文件夹

* 项目 --> EditQuery--> javaBeanDataSource --> 输入class名选择相应的字段

---

##Jasper打印--java

#### 所需jar(必须)

* jasperreports-5.0.4.jar
* groovy-all-2.0.1.jar
* commons-logging.jar
* commons-digester.jar
* commons-beanutils.jar  
* commons-collections-3.2.jar

#### java 调用

* 获取数据源

		JRDataSource dataSource = new JRBeanCollectionDataSource(list);

* 加载报表模版

		JasperReport jasperReport = (JasperReport) JRLoader.loadObject(file);

* 获取打印对象

		JasperPrint print=JasperFillManager.fillReport(jasperReport,null, dataSource);

* 打印

		JasperPrintManager.printReport(print, false);
		
* 具体代码如下：

	 	public void pressData(List list) throws FileNotFoundException {
	        try {
	            JRDataSource dataSource = new JRBeanCollectionDataSource(list);     
				String basePath = FileUtil.getBasePath();
	            String filePath = basePath + "\\iReporter\\report1.jasper";
	            File file = new File(filePath);
	            if (file.exists()) {
	                JasperReport jasperReport = (JasperReport) JRLoader.loadObject(file);
	                JasperPrint jasperPrint = JasperFillManager.fillReport(jasperReport, null, dataSource);
	                jasperPrint.setOrientation(OrientationEnum.PORTRAIT);//设置打印的方向
					boolean isFinish = JasperPrintManager.printReport(jasperPrint, false);
			        if(isFinish){
				       System.out.println("打印成功");
				    }
				} else {
				     throw new FileNotFoundException("文件不存在");
				}
	         } catch (JRException ex) {
	            if (ex.getCause() != null) {
	                if ("java.awt.print.PrinterAbortException".equals(ex.getCause().toString())) {
	                    System.err.println("用户取消打印");
	                }
	            } else {
	                Logger.getLogger(Service.class.getName()).log(Level.SEVERE, null, ex);
	            }
	        }
   		}