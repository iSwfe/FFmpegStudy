# Summary of FFmpeg Options
### 一些关于FFmpeg工具使用参数的总结
```
语法格式：
ffmpeg 
	[输入选项]	eg. -ss 00:01:00 -t 00:00:30
	[输入文件]	eg. -i input.mp4
	[输出选项]	eg. -vcodec h264 -acodec aac
	[输出文件]	eg. output.mp4
```

* 常见的[视频/音频]编解码器：
```
-vcodec 
	h264	//avc/h264/mpeg4视频编码
	hevc	//h265视频编码
-acodec
	aac	//aac音频编码
	ac3	//ac3音频编码
```

* [输入选项]
```
-ss 00:01:00	//对于输入文件截取的开始时间
-t 00:30:00	//对于输入文件截取的持续时间
-f concat	//使用concat分离器，帧内编码要用到
```

* [输出选项]
```
-vcodec codec	//对于输出文件视频流使用的编码器
-acodec codec	//对于输出文件音频流使用的编码器
-f fmt		//强制使用格式
-q 0 -intra	//不损失质量以帧内编码
```

* Want More?
```
ffmpeg
	-codecs		//available codecs
	-decoders	//available decoders
	-encoders	//available encoders
	-formats	//available formats
```
