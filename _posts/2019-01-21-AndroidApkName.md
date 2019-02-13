---
layout: post
title: 修改Android打包APK名称
date: 2019-01-21
categories: code
tags: Android
---

> 

```groovy
android {
    productFlavors {
        qd {
            applicationId "xxxx"
            versionCode 43
            versionName "1.0.10"
    	}
    }
    
    applicationVariants.all { variant ->
        if (variant.buildType.name == 'release') {
            variant.outputs.all { output ->
                def apkName = ""
                variant.productFlavors.each { product ->
                    apkName = product.name + "-v" + product.versionCode + ".apk"
                }
                outputFileName = apkName
            }
        }
    }
    
    //所有Flavor添加placeholder, 备忘
    productFlavors.all { flavor ->
        manifestPlaceholders.put("app_icon", isRelease ? "@mipmap/ic_launcher_ctn" : "@mipmap/ic_launcher_ctn_test")
    }
}
```

