---
title: Android—调用系统相机拍照
---

<p align="right">作者：不完美</p>

*本文实现的效果是：点击一个button，调用系统相机进行拍照，并储存在picture根目录下*

## 一、页面布局

页面布局比较简单，只有一个button，代码如下：

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:layout_marginLeft="10dp"
    android:orientation="vertical">

    <Button
        android:id="@+id/btnPhoto"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="拍照" />
</LinearLayout>
```



## 二、增加权限

在AndroidManifest.xml中增加**调用相机**和**存储数据**的权限，代码如下：

```xml
<uses-permission android:name="android.permission.CAMERA"/>
<uses-feature android:name="android.hardware.camera"/>
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
<uses-permission android:name="android.permission.RECORD_AUDIO"/>
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"/>
```



## 三、设置照片存储路径

```java
  private File createImageFile() throws IOException{
        String timeStamp=new SimpleDateFormat("yyyyMMdd_HHmmss").format(new Date());
        Log.i(timeStamp,"保存路径");
        String imageFileName="JPEG_"+timeStamp+"_";
        File storageDir = Environment.getExternalStoragePublicDirectory(
                Environment.DIRECTORY_PICTURES);
        File image = File.createTempFile(
                imageFileName,  /* prefix */
                ".jpg",         /* suffix */
                storageDir      /* directory */
        );

        // Save a file: path for use with ACTION_VIEW intents
        mCurrentPhotoPath = "file:" + image.getAbsolutePath();
        return image;
    }
```

## 四、启动系统相机拍照并存储

```java
 private void dispatchTakePictureIntent() {
        //隐式intent，不指定要启动哪个活动，而是通过配置一些相关信息，如action，category等信息，然后交给系统分析并找出合适的活动去启动
        Intent takePictureIntent = new Intent(MediaStore.ACTION_IMAGE_CAPTURE);
        // Ensure that there's a camera activity to handle the intent
        //resolveActivity 该方法接受一个包管理器对象作为参数，通过查找该包管理器，返回能够处理该intent的activity的component对象，没有找到能处理该intent的组件则返回null
        if (takePictureIntent.resolveActivity(getPackageManager()) != null) {
            // Create the File where the photo should go
            File photoFile = null;
            try {
                photoFile = createImageFile();
            } catch (IOException ex) {
                // Error occurred while creating the File

            }
            // Continue only if the File was successfully created
            if (photoFile != null) {
                Uri photoUri=FileProvider.getUriForFile(this,"com.example.z.test.fileProvider",photoFile);
               // Log.d(photoUri.toString(),"保存路径");
                Log.i(photoUri.toString(),"保存路径");
               // Uri photoUri= FileProvider.getUriForFile(this,getPackageName()+".provider",photoFile);
                takePictureIntent.putExtra(MediaStore.EXTRA_OUTPUT,
                        photoUri);
                startActivityForResult(takePictureIntent, REQUEST_TAKE_PHOTO);
            }
        }
    }
```

## 五、遇到的问题

*Android 7.0禁止应用程序向外部公布file://的 URI，尝试传递 file://的 URI会触发 FileUriException。应用程序之间共享数据，应该发送 content://的 URI，且授予URI临时访问权限。解决方法配置FileProvider。*

### 1、在AnadroidMainfest.xml中配置provider

```xml
<provider
            android:name="android.support.v4.content.FileProvider"
            android:authorities="com.example.z.test.fileProvider"
            android:exported="false"
            android:grantUriPermissions="true"
            tools:replace="android:authorities">
            <meta-data
                android:name="android.support.FILE_PROVIDER_PATHS"
                android:resource="@xml/provider_paths"
                tools:replace="android:resource"/>
        </provider>
```

### 2、增加provider_paths.xml

在res文件夹下新建xml文件夹，在xml文件夹下增加provider_paths.xml，provider_paths.xml文件内容如下：

```xml

```

## 六、代码清单

目录结构如下图所示：

![Markdown](http://i2.bvimg.com/662648/530b49ecab6e26cf.png)



### 1、AndroidManifest.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    package="com.example.z.test">

    <uses-permission android:name="android.permission.CAMERA"/>
    <uses-feature android:name="android.hardware.camera"/>
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
    <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"/>


    <application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/AppTheme">
        <activity android:name=".MainActivity">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />

                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
        <provider
            android:name="android.support.v4.content.FileProvider"
            android:authorities="com.example.z.test.fileProvider"
            android:exported="false"
            android:grantUriPermissions="true"
            tools:replace="android:authorities">
            <meta-data
                android:name="android.support.FILE_PROVIDER_PATHS"
                android:resource="@xml/provider_paths"
                tools:replace="android:resource"/>
        </provider>
    </application>

</manifest>
```

### 2、MainActivity.java

```java
package com.example.z.test;


import android.net.Uri;
import android.os.Environment;
import android.provider.MediaStore;
import android.support.v4.content.FileProvider;
import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;
import android.util.Log;
import android.view.View;
import android.widget.Button;
import android.widget.Toast;
import  android.content.Intent;

import java.io.File;
import java.io.IOException;
import java.text.SimpleDateFormat;
import java.util.Date;

public class MainActivity extends AppCompatActivity implements View.OnClickListener {
    private Button btnPhoto;
    static final int REQUEST_TAKE_PHOTO = 1;
    String mCurrentPhotoPath;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        initView();
    }

    private void initView(){
        btnPhoto=findViewById(R.id.btnPhoto);
        btnPhoto.setOnClickListener(this);
    }

    @Override
    public void onClick(View v){
        switch (v.getId()){
            case R.id.btnPhoto:
              //Toast.makeText(MainActivity.this,"btnPhoto",Toast.LENGTH_SHORT).show();
                dispatchTakePictureIntent();
                break;
            default:
                break;
        }
    }

    private File createImageFile() throws IOException{
        String timeStamp=new SimpleDateFormat("yyyyMMdd_HHmmss").format(new Date());
        Log.i(timeStamp,"保存路径");
        String imageFileName="JPEG_"+timeStamp+"_";
        File storageDir = Environment.getExternalStoragePublicDirectory(
                Environment.DIRECTORY_PICTURES);
        File image = File.createTempFile(
                imageFileName,  /* prefix */
                ".jpg",         /* suffix */
                storageDir      /* directory */
        );

        // Save a file: path for use with ACTION_VIEW intents
        mCurrentPhotoPath = "file:" + image.getAbsolutePath();
        return image;
    }


    private void dispatchTakePictureIntent() {
        //隐式intent，不指定要启动哪个活动，而是通过配置一些相关信息，如action，category等信息，然后交给系统分析并找出合适的活动去启动
        Intent takePictureIntent = new Intent(MediaStore.ACTION_IMAGE_CAPTURE);
        // Ensure that there's a camera activity to handle the intent
        //resolveActivity 该方法接受一个包管理器对象作为参数，通过查找该包管理器，返回能够处理该intent的activity的component对象，没有找到能处理该intent的组件则返回null
        if (takePictureIntent.resolveActivity(getPackageManager()) != null) {
            // Create the File where the photo should go
            File photoFile = null;
            try {
                photoFile = createImageFile();
            } catch (IOException ex) {
                // Error occurred while creating the File

            }
            // Continue only if the File was successfully created
            if (photoFile != null) {
                Uri photoUri=FileProvider.getUriForFile(this,"com.example.z.test.fileProvider",photoFile);
               // Log.d(photoUri.toString(),"保存路径");
                Log.i(photoUri.toString(),"保存路径");
               // Uri photoUri= FileProvider.getUriForFile(this,getPackageName()+".provider",photoFile);
                takePictureIntent.putExtra(MediaStore.EXTRA_OUTPUT,
                        photoUri);
                startActivityForResult(takePictureIntent, REQUEST_TAKE_PHOTO);
            }
        }
    }
}

```

### 3、activity_main.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:layout_marginLeft="10dp"
    android:orientation="vertical">

    <Button
        android:id="@+id/btnPhoto"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="拍照" />
</LinearLayout>


```

### 4、provider_paths.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<paths xmlns:android="http://schemas.android.com/apk/res/android">
    <external-path name="external_files" path="."/>
</paths>
```

