---
layout: post
title:  "Android Pracelable数据"
date:   2017-10-26
desc: "Android Pracelable数据"
keywords: "android,Pracelable数据"
categories: [Android]
tags: [Android, Pracelable]
---

	public class ScanInfo implements Parcelable {

		private String moduleId;
		private String mainId;
		private String value;
		private int length;

		public ScanInfo(Parcel source) {
			moduleId = source.readString();
			mainId = source.readString();
			value = source.readString();
			length = source.readInt();
		}

		@Override
		public int describeContents() {
			return 0;
		}

		@Override
		public void writeToParcel(Parcel dest, int flags) {
			dest.writeString(moduleId);
			dest.writeString(mainId);
			dest.writeString(value);
			dest.writeInt(length);
		}

		public static final Creator<ScanInfo> CREATOR = new Creator<ScanInfo>() {

			/**
			* 供外部类反序列化本类数组使用
			*/
			@Override
			public ScanInfo[] newArray(int size) {
				return new ScanInfo[size];
			}

			/**
			* 从Parcel中读取数据
			*/
			@Override
			public ScanInfo createFromParcel(Parcel source) {
				return new ScanInfo(source);
			}
			};
	}