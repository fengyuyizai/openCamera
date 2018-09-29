## 能够使用浏览器打开手机端摄像头


#####能够前后摄像头切换，能够截取当前视频流图像

#####兼容各大主流浏览器，

####主要使用的api：
``` javascript

// 获取视频流
navigator.mediaDevices.getUserMedia(constrains).then(success).catch(error);

// 获取设备摄像信息
navigator.mediaDevices.enumerateDevices().then(gotDevices).then(getStream).catch(handleError);

```

之前在本机上测试Chrome出现问题，问题在于没有使用https作为测试链接，如果使用http的话，则项目不能打开摄像头，这可能与chrome的明文加密有关系

如果使用http，代码会报
` NotSupportedError Only secure origins are allowed (see: https://goo.gl/Y0ZkNV) `
但是这个错开始并没有报，开始我直接运行获取视频流代码，项目的代码仿佛停止运行一般，相应位置的console.log也没有输出，这个错误也没有报
后来经过调试，获取视频流的代码放在点击事件中，错误才出来。。

#####切换摄像头代码：

```javascript
// 多选框更改事件
videoSelect.onchange = getStream;

// 获取设备音频输出设备与摄像设备，这里我只用到了摄像设备
function gotDevices(deviceInfos) {
    console.log('deviceInfos')
	console.log('deviceInfos:', deviceInfos);
	for (let i = 0; i !== deviceInfos.length; i++) {
		let deviceInfo = deviceInfos[i];
		var option = document.createElement('option');
		// console.log(deviceInfo)
		if (deviceInfo.kind === 'videoinput') {  // audiooutput  , videoinput
			option.value = deviceInfo.deviceId;
	    	option.text = deviceInfo.label || 'camera ' + (videoSelect.length + 1);
	      	videoSelect.appendChild(option);
	    }
	}
}
```

#####兼容浏览器:

```javascript

//访问用户媒体设备的兼容方法
function getUserMedia(constrains,success,error){
    if(navigator.mediaDevices.getUserMedia){
        //最新标准API
        navigator.mediaDevices.getUserMedia(constrains).then(success).catch(error);
    } else if (navigator.webkitGetUserMedia){
        //webkit内核浏览器
        navigator.webkitGetUserMedia(constrains).then(success).catch(error);
    } else if (navigator.mozGetUserMedia){
        //Firefox浏览器
        navagator.mozGetUserMedia(constrains).then(success).catch(error);
    } else if (navigator.getUserMedia){
        //旧版API
        navigator.getUserMedia(constrains).then(success).catch(error);
    }
}

```


#####获取视频流成功回调：
```javascript

function getStream(){

	if (window.stream) {
		window.stream.getTracks().forEach(function(track) {
			track.stop();
		})
	}

	if (navigator.mediaDevices.getUserMedia || navigator.getUserMedia || navigator.webkitGetUserMedia || navigator.mozGetUserMedia){
	    //调用用户媒体设备，访问摄像头
	    const constraints = {
	        audio: true, 
	        video: {
	            width: { ideal: 1280 },
	            height: { ideal: 720 },
	            frameRate: { 
	                ideal: 10,
	                max: 15
	            },
	            deviceId: {exact: videoSelect.value}
	        }
	    };
	    console.log('获取视频流');
	    getUserMedia(constraints,success,error);
	} else {
	    alert("你的浏览器不支持访问用户媒体设备");
	}
}

```

#####截取视频流作为图片：

```javascript

//注册拍照按钮的单击事件
document.getElementById("capture").addEventListener("click",function(){
    //绘制画面
    console.log('点击事件');
    context.drawImage(video,0,0,480,320);
});

```






