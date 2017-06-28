# Exam2
contendprovide


ContentReader.java
package www.wulian.com.contentprovider;

import android.content.ContentResolver;
import android.database.Cursor;
import android.net.Uri;
import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;
import android.util.Log;
import android.view.View;
import android.widget.Toast;

import java.util.ArrayList;
import java.util.List;

public class ContentReader extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
    }
    public void btn_read(View view){
        Uri uri=Uri.parse("content://sms/");
        ContentResolver resolver=getContentResolver();
        Cursor cursor=resolver.query(uri,new String[]{"address","date","type","body"},null,null,null);
        List<SmsInfo> smsInfos=new ArrayList<SmsInfo>();
        Log.d("_hello","_love");
        Toast.makeText(this,"短信有"+cursor.getCount()+"条",Toast.LENGTH_SHORT).show();
        while (cursor.moveToNext()){
            String address=cursor.getString(0);
            long date=cursor.getLong(1);
            int type=cursor.getInt(2);
            String body=cursor.getString(3);
            SmsInfo smsInfo=new SmsInfo(date,type,body,address);
            smsInfos.add(smsInfo);
            Log.d("_hello","_hi");
        }
        cursor.close();
  //备份
    //    SmsUtils.backUpSms(smsInfoList,this);
    }
}




ContentReader.xml

<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:paddingLeft="@dimen/activity_horizontal_margin"
    android:paddingRight="@dimen/activity_horizontal_margin"
    android:paddingTop="@dimen/activity_vertical_margin"
    android:paddingBottom="@dimen/activity_vertical_margin"
    tools:context=".ContentReader">

  <Button
      android:layout_width="wrap_content"
      android:layout_height="wrap_content"
      android:text="读取"
      android:onClick="btn_read"/>
</RelativeLayout>


SmsUtils.class

需要备份的时候要加，还没测试过名字什么的是否正确
package com.example.a01.tigongzhe.utils;

import android.content.Context;
import android.os.Environment;
import android.util.Xml;
import android.widget.Toast;

import com.example.a01.tigongzhe.adapters.SmsInfo;

import org.xmlpull.v1.XmlSerializer;

import java.io.File;
import java.io.FileOutputStream;
import java.io.IOException;
import java.util.List;

/**
 * Created by 01 on 2017/6/18.
 */

public class SmsUtils {
    public static void backUpSms(List<SmsInfo> smsInfoList,Context context) throws IOException {
        //创建序列化器serializer
        XmlSerializer serializer= Xml.newSerializer();
        //获取sd卡根目录的路径
        File file=new File(Environment.getExternalStorageDirectory(),"sms.xml");
        //获得输出流
        FileOutputStream os=new FileOutputStream(file);
        //设置编码方式
        serializer.setOutput(os,"utf-8");
        //写入xml文件标识
        serializer.startDocument("utf-8",true);
        serializer.startTag(null,"smss");
        for(SmsInfo info:smsInfoList){

            serializer.startTag(null,"sms");
            serializer.attribute(null,"id",info.getId()+"");
            serializer.startTag(null,"body");
            serializer.text(info.getBody());
            serializer.endTag(null,"body");
            serializer.startTag(null,"type");
            serializer.text(info.getType()+"");
            serializer.endTag(null,"type");
            serializer.startTag(null,"address");
            serializer.text(info.getAddress());
            serializer.endTag(null,"address");
            serializer.startTag(null,"date");
            serializer.text(info.getDate()+"");
            serializer.endTag(null,"date");
            serializer.endTag(null,"sms");
        }
        serializer.endTag(null,"smss");
        serializer.endDocument();
        Toast.makeText(context,"cg",Toast.LENGTH_LONG).show();
    }
}



SmsInfo .class
package www.wulian.com.contentprovider;

/**
 * Created by Administrator on 2017/3/13.
 */
public class SmsInfo {
    private long date;

    public int getType() {
        return type;
    }

    public void setType(int type) {
        this.type = type;
    }

    public long getDate() {
        return date;
    }

    public void setDate(long date) {
        this.date = date;
    }

    public String getBody() {
        return body;
    }

    public void setBody(String body) {
        this.body = body;
    }

    public String getAddress() {
        return address;
    }

    public void setAddress(String address) {
        this.address = address;
    }

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public int type;
    public String body;
    public String address;
    public int id;
//构造方法
    public SmsInfo(long date,int type,String body,String address){
        this.date=date;
        this.type=type;
        this.body=body;
        this.address=address;

    }
}



//注册

<uses-permission android:name="android.permission.READ_SMS"/>
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
<uses-permission android:name="android.permission.SEND_SMS"/>
