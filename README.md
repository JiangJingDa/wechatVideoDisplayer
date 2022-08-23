## **一、实验目标**

1、掌握视频列表的切换方法；2、掌握视频自动播放方法；3、掌握视频随机颜色弹幕效果。

## 二、实验步骤

### 1.创建项目并完成相关配置

删除并修改相关文件，之后创建images文件夹用来存放播放按钮

### 2. 视图设计：

#### 2.1导航栏设计：

在app.json中进行设计，代码如下：

``` json
{
  "pages":[
    "pages/index/index"
  ],
  "window":{
    "navigationBarBackgroundColor": "#987938",
    "navigationBarTitleText": "口述校史"
  },
  "style": "v2",
  "sitemapLocation": "sitemap.json"
}

```

#### 2.2 页面设计：

主要有三个区域，区域一：视频播放器；区域二：弹幕发送区；区域三：视频列表

使用如下组件：

+ 区域一<video>组件
+ 区域二<view>组件
+ 区域二内部<input>和<button>组件
+ 区域三<view>组件
+ 区域三内部单元行<view>组件
+ 区域三单元行内每行一个<image>组件用于显示播放图标、一个<text>组件用于显示视频标题

#### 2.3 逻辑实现：

##### 2.3.1 使用for循行实现播放列表的更新

##### 2.3.2 实现点击播放视频

##### 2.3.3 实现发送弹幕的功能

index.js代码：

``` js
// index.js
// 获取应用实例
Page({
  

  /**
   * 页面的初始数据
   */
  data: {
    danmuTxt: "",
    list: [{
        id: '1001',
        title: '杨国宜先生口述校史实录',
        videoUrl: 'http://arch.ahnu.edu.cn/__local/6/CB/D1/C2DF3FC847F4CE2ABB67034C595_025F0082_ABD7AE2.mp4?e=.mp4'
      },
      {
        id: '1002',
        title: '唐成伦先生口述校史实录',
        videoUrl: 'http://arch.ahnu.edu.cn/__local/E/31/EB/2F368A265E6C842BB6A63EE5F97_425ABEDD_7167F22.mp4?e=.mp4'
      },
      {
        id: '1003',
        title: '倪光明先生口述校史实录',
        videoUrl: 'http://arch.ahnu.edu.cn/__local/9/DC/3B/35687573BA2145023FDAEBAFE67_AAD8D222_925F3FF.mp4?e=.mp4'
      },
      {
        id: '1004',
        title: '吴仪兴先生口述校史实录',
        videoUrl: 'http://arch.ahnu.edu.cn/__local/5/DA/BD/7A27865731CF2B096E90B522005_A29CB142_6525BCF.mp4?e=.mp4'
      }
    ]
  },

  /**
   * 生命周期函数--监听页面加载
   */
  onLoad: function (options) {
    this.videoCtx = wx.createVideoContext('myVideo', this)
  },

  /**
   * 生命周期函数--监听页面初次渲染完成
   */
  onReady: function () {

  },

  /**
   * 生命周期函数--监听页面显示
   */
  onShow: function () {

  },

  /**
   * 生命周期函数--监听页面隐藏
   */
  onHide: function () {

  },

  /**
   * 生命周期函数--监听页面卸载
   */
  onUnload: function () {

  },

  /**
   * 页面相关事件处理函数--监听用户下拉动作
   */
  onPullDownRefresh: function () {

  },

  /**
   * 页面上拉触底事件的处理函数
   */
  onReachBottom: function () {

  },

  /**
   * 用户点击右上角分享
   */
  onShareAppMessage: function () {

  },
  /**
   * 播放视频
   */
  playVideo: function (e) {
    //停止视频
    this.videoCtx.stop()
    //更新视频地址
    this.setData({
      src: e.currentTarget.dataset.url
    })
    //播放新的视频
    this.videoCtx.play()
  },
  /**
   * 更新弹幕内容
   */
  getDanmu: function (e) {
    this.setData({
      danmuTxt: e.detail.value
    })
  },
  /**
   * 发送弹幕
   */
  sendDanmu: function (e) {
    let text = this.data.danmuTxt;
    this.videoCtx.sendDanmu({
      text: text,
      color: getRandomColor()
    })
  }  
})
  /**
   * 随机生成颜色
   */
  function  getRandomColor() {
    let rgb=[];
    for (let i = 0; i < 3; i++) {
      let color = Math.floor(Math.random() * 256).toString(16);
      color = color.length == 1 ? '0' + color : color;
      rgb.push(color);
  }
  return "#"+rgb.join("")
  }
```

index.wxml代码：

``` html
<!--index.wxml-->
<!--区域一：视频播放器-->
<video id= "myVideo" controls enable-danmu danmu-btn="" src="{{src}}"></video>
<!--区域二：弹幕控制-->
<view class="damuArea">
    <input type="text" placeholder="请输入弹幕内容"bindinput="getDanmu" ></input>
    <button bindtap="sendDanmu" >发送弹幕</button>
</view>
<!--区域三：视频列表-->
<view class="videoList">
    <view class="videoBar" wx:for="{{list}}" wx:key="viedo{{index}}"data-url="{{item.videoUrl}}" bindtap="playVideo">
        <image src='/images/play.png'></image>
        <text>{{item.title}}</text>
    </view>
</view>
```

index.wxss代码：

``` css
/**index.wxss**/
video{
  width: 100%;
}
/*弹幕区域样式*/
.damuArea{
  display: flex;
  flex-direction: row; 
}
/*文本输入框形式*/
input{
  border: 1rpx solid #987938;
  flex-grow: 1;
  height: 75rpx;
}
/*按钮样式*/
button{
  color: white;
  background-color: #987938;
}
.videoList{
  width: 100%;
  min-height: 400rpx;
}
.videoBar{
  width: 95%;
  display: flex;
  flex-direction: row;
  border-bottom: 1rpx solid #987938;
  margin: 10rpx;
}
image{
  width: 70rpx;
  height: 70rpx;
  margin: 20rpx;
}
text{
  font-size: 45rpx;
  color: #987938;
  margin: 20rpx;
  flex-grow: 1;
}
```



## 三、程序运行结果

![image-20220820112336826](C:\Users\姜景达\AppData\Roaming\Typora\typora-user-images\image-20220820112336826.png)

![image-20220820112339776](C:\Users\姜景达\AppData\Roaming\Typora\typora-user-images\image-20220820112339776.png)

## 四、问题总结与体会

本次实验学习到了video组件，以及input组件的使用方法，加深了对逻辑层编写的印象。提升了编写CSS文件以及html文件的能力