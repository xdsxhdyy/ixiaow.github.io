---
layout: post
title:  "EditText增强-TextInputLayout"
date:   2017-10-27
desc: "EditText增强-TextInputLayout"
keywords: "android,EditText, TextInputLayout"
categories: [Android]
tags: [Android, EditText]
---

### XML文件

#### TextInputLayout + EditText

    <android.support.design.widget.TextInputLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        app:counterEnabled="true"
        app:counterMaxLength="11"
        app:counterOverflowTextAppearance="@style/count_over_text"
        app:counterTextAppearance="@style/count_text">

        <EditText
            android:id="@+id/et_phone"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:hint="请输入手机号"
            android:maxLength="11"/>
    </android.support.design.widget.TextInputLayout>

#### TextInputLayout + TextInputEditText (推荐)

    <android.support.design.widget.TextInputLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content">

        <android.support.design.widget.TextInputEditText
            android:id="@+id/et_password"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:hint="请输入密码"
            android:inputType="textPassword"
            android:maxLength="11"/>
    </android.support.design.widget.TextInputLayout>

### JAVA

    mBtnLogin.setOnClickListener(new View.OnClickListener() {
        @Override
        public void onClick(View v) {
            String phone = mETPhone.getText().toString();
            if (!phone.startsWith("1")) {
    // 下边提示
                    TextInputLayout.setError("手机号码无效");
                //右边图标提示
                mETPhone.setError("手机号无效");
            }
        }
    });
