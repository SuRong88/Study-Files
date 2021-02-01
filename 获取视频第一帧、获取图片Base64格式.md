#  获取视频第一帧、获取图片Base64格式

## 获取视频第一帧

##  获取图片base64

```javascript
function getImgBase64(url) {
	return new Promise(function(resolve, reject) {
		let Img = new Image();
		Img.setAttribute('crossOrigin', 'anonymous'); //处理跨域
		let dataURL = '';
		Img.src = url;
		Img.onload = function() { //确保图片完整获取
			const canvas = document.createElement("canvas"); //创建canvas元素
			const width = Img.width; //canvas的尺寸和图片一样
			const height = Img.height;
			canvas.width = width;
			canvas.height = height;
			canvas.getContext("2d").drawImage(Img, 0, 0, width, height); //绘制canvas
			dataURL = canvas.toDataURL('image/jpeg'); //转换为base64
			resolve(dataURL);
		};
	});
}

//test
let imgUrl = "https://wx.qlogo.cn/mmhead/Xxm13xujfTCFEVZOzaHJqgIX8MpocIrfzHJukfsMJkE/0"
getImgBase64(imgUrl).then(res => {
	console.log(res)
})
```

