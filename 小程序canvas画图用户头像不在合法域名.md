#  小程序canvas画图用户头像不在合法域名

canvas 里面的图片必须是本地图片不能使用网络图片，因此我们需要用到微信的一个API `wx.downloadFile` 将网络图片转化成本地图片，以供canvas画图时候使用，其中也明确提出需要将要下载图片的路径地址中 https 格式的域名在小程序后台进行安全域名配置。本以为万事具备，但是万万没想到正式发版以后好多用户都无法生成自己的分享图，真的是让自己严重怀疑是否写错了，可是我怎么测试都没有问题，最后几经周折发现是因为有一部分从第三方引入的用户头像是 `https://thirdwx.qlogo.cn` 开头的路径，时间长不使用这个功能了这就当是自己业务能力不足造成的，这个锅我背了，赶紧配置安全域名进行解决。本以为这回总没事了吧，谁成想，千算万算怎么也没算到，微信玩了一手更狠的，还有一大部分用户头像是以 `http://thirdwx.qlogo.cn` 开头的路径，报错信息如下，微信自己发布提供的http格式路径让存着，然后又在这里自己卡着不放报着错，真乃神操作。

```javascript
wx.downloadFile({
	url: shareInfo.userphoto.replace('http://thirdwx.qlogo.cn', 'https://wx.qlogo.cn'),
	success: (res)=>{
		var avatarUrl = res.tempFilePath
	}
})
```

解决思路，通过 `replace` 方法将头像路径中的 `http://thirdwx.qlogo.cn` 替换成 `https://wx.qlogo.cn` ，头像还是原来的头像，但是可以下载成本地图片再也不会报错了，canvas 可以正常画图了，用户可以正常进行分享图生成了，总算是解决掉了这个问题。

