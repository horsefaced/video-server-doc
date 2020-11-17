# VideoServer的说明文档

## 客户端文件引用

客户端在网页上引用视频播放组件

```html
<script src="视频服务器地址包括端口/video-player.js" type="text/javascript"></script>
```

比如:

```html
<script src="http://fzcjit.320.io:58080/video-player.js" type="text/javascript"></script>
```

如果是从业务系统得到可以直接使用

```html
<script src="业务系统地址:业务系统端口/video-player.js" type="text/javascript"></script>
```



## 客户端使用

### 操作部分

1. 创建播放组件
   
   播放组件尽量不要在不同的视频源即 source 间重复利用

   播放器组件需要一个div作为容器container，示例代码为：

   ```javascript
   let player = new VideoPlayer(container, {
       server: 可选，视频服务器地址包括端口, 缺省为 window.location.origin
       source: 视频的RTSP流地址
       type:   可选，使用的视频格式，缺省为'mjpeg'
       isLive: 可选，是否是实时直播视频流，缺省为true，直播视频流在有观众的情况下，不会结束视频流推送
       error: 可选，错误事件处理回调
       bundle: 可选，用户可以在里面填写自己想要的一切信息，本信息会在事件回调中返回给用户,
       compression: 可选，返回的是压缩后的视频流，缺省为true，如果设置为false，这返回原始流，
   })
   ```

2. 播放或继续播放

   调用 play 函数, 在回放流的情况下, 相同的rtsp流地址会在上次暂停的时间点上继续播放

   ```javascript
   player.play(source);  //source为rtsp流地址
   ```

3. 销毁

   不再使用这个播放器后，调用 terminate 函数，这个函数可调可不调，推荐为了资源着想调用
   **一旦调用 terminate 函数后，这个player就不能再用**

   ```javascript
   player.terminate();
   ```

4. 暂停

   调用 pause 函数, 只在回放流情况下生效

   ```javascript
   player.pause();
   ```

5. 停止

   调用 stop 函数, 只在回放流情况下生效

   ```javascript
   player.stop();
   ```

6. 指定播放开始时间

   设定 current 属性, 只在回放流情况下生效

   ```javascript
   player.current = 5; //5为秒, 如果超过回放流长度, 回放流会直接停止并不会播放
   ```
   比如拖动进度条这个功能
   
   ```javascript
   player.current = 10; //拖动到10 秒
   player.play(source); //继续播放视频
   ```
   

### 事件部分

1. progressing

   回放流情况下, 给出当前播放的进度

   ```javascript
   player.on('progressing', (event) => {
        event.total //视频总长度, 单位为秒
        event.current //视频当前时间位置, 单位为秒
   });
   ```

2. statusChange

   播放器状态改变通知

   ```javascript
   player.on('statusChange', (status) => {
        switch (status) {
               case 'playing': 
                   //正在播放, 只在播放开始时发出
                   break;
               case 'paused':
                   //已经暂停
                   break;
               case 'stopped':
                   //已经停止
                   break;    
             };
   });
   ```

3. paused

   暂停事件, 表明暂停事件发出

4. end

   停止事件, 表明停止事件发出


## Error部分

video-player在出错后会发出error事件，可以使用on('error')进行监听，事件处理示例如下：

```javascript
player.on('error', (event) => {
    console.log(event.message); //错误消息
    console.log(event.bundle); //用户建立Player时传入的bundle, 如果建立时没有传入，这里返回{}对象
})
```

event.message 会返回错误消息，消息中的内容说明如下：

1. 'Invalid data found when processing input' 当前通道无法得到视频流
2. 'Connection refused' 视频源无法连接，请检查网络

## 示例

用户可通过 /index.html 和 /playback.html 两个示例了解具体调用
