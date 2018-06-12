---
layout: post
title: React Native 踩坑笔记
date: 2016-12-15
categories: code
tags: React-Native
---

> React Native开发中遇到的一些问题

- 解决 Could not get BatchedBridge, make sure your bundle is packaged correctly 

  `adb reverse tcp:8081 tcp:8081 `

- 新建 assets文件夹 

  `react-native bundle --platform android --dev false --entry-file index.android.js --bundle-output android/app/src/main/assets/index.android.bundle --sourcemap-output android/app/src/main/assets/index.android.map --assets-dest android/app/src/main/res/`

- ListView滑动到底部 `onEndReached`   `onEndReachedThreshold`

- 在render()里可以使用IIFE, 基本格式举例 

  `((str) => {log(str)})("param")`

- 强制刷新, 从this.state或者this.props之外取值, 调用 this.forceUpdate(), 但是不推荐

- 实现首页多界面缓存可用Navagator, 用jumpTo()方法

- 出现这个错误 "This is either due to a require error during..." 运行 `react-native start` 查看

- return component时, 如果只有一个`<*** />`, return不加括号, 如果是 `<***>...</***>`, return加括号, return()

- `import RCTDeviceEventEmitter from 'RCTDeviceEventEmitter'`

- ListView一行显示多项 `contentContainerStyle = { {flexDirection: 'row', flexWrap: 'wrap', alignItems: 'center', }}` , alignItems 必须要加

- vscode 使用typings提示 

  `sudo npm i typings -g `

  `sudo npm i typescript -g `

  每个项目 

  `typings i dt~react-native `

  `typings i dt~react`

- 在没有ReactContext的情况下传值用阻塞队列 ArrayBlockingQueue，满了插、空了取都会阻塞

- `UIManager.measureLayoutRelativeToParent(findNodeHandle(ref), (e) => { }, (x, y, w, h) => { })`

  `UIManager.measureLayout(findNodeHandle(ref), findNodeHandle(root), (e) => { }, (x, y, w, h) => { }) `

- 不能关闭界面 

   ```
   config: { 
     ...Navigator.SceneConfigs.PushFromRight, 
     gestures: { pop: false } 
   }
   ```

- 

- ```
  static defaultProps = { 
       title: '标题' 
  }
  static propTypes = { 
      title: PropTypes.string.isRequired, 
  } 
  ```

  
