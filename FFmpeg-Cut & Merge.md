# FFmpeg裁剪视频

```ffmpeg -ss 00:01:00 -t 00:00:10 -i input.mp4 -vcodec copy -acodec copy output.mp4```
```
ffmpeg
	-ss 00:01:00        //[输入选项]开始于00:01:00
	-t 00:00:10         //[输入选项]裁剪内容持续00:00:10
	-to 00:30:00		//[输入选项]裁剪至00:30:00
	-i input.mp4        //[输入文件]输入视频对象为input.mp4
	-vcodec copy        //[输出选项]选择视频编码方式（对于输出文件）：原样拷贝
	-acodec copy        //[输出选项]选择音频编码方式（对于输出文件）：原样拷贝
	output.mp4          //[输出文件]输出视频对象为output.mp4
```
* "-vcodec copy -acodec copy"等同于"-c copy"

# 针对精确时间的裁剪
尽管上述的裁剪命令已经表明了精确的裁剪时间和时长，但FFmpeg只会在视频的关键帧处执行裁剪，又因为大多视频关键帧一般相隔几秒到十几秒，因此裁剪出来会有几秒的误差，如果想执意得到精确时间，我们就必须把每秒都转换成关键帧，这即是“帧内编码”。命令如下：

```ffmpeg -i input.mp4 -q 0 -intra output-intra.mp4```

```
ffmpeg
	-i input.mp4        //[输入文件]输入视频对象为input.mp4
	-q 0                //[输出选项]即为"-qscale q"：使用固定的视频量化标度(VBR)，以<q>质量为基础的VBR，取值0.01-255。q=0为取原样(?)。此处"-q 0"为缩写（来自FFmpeg命令行的建议）。
				//[PS.]此处不建议用"-c copy"代替
	-intra              //[输出选项]使用帧内编码，特点：过程耗时且会使视频体积显著增大。
	output-intra.mp4    //[输出文件]输出视频对象为output-intra.mp4
```
至此，我们就可以使用上述的裁剪命令实现精确裁剪了。




# FFmpeg合并视频
* 提供两种方案和备用方案：
##### ①先把视频转换成相同的编码，再用copy工具合并，最后转换为原编码。    
* 转换成mpeg编码：
```
ffmpeg -i input1.mp4 -f mpeg output1.mp4
ffmpeg -i input2.mp4 -f mpeg output2.mp4
```
* 通过"copy /b"命令合并文件：

```copy /b input1.mp4+input2.mp4 merge.mp4```

* 转换成原格式

```ffmpeg -i merge.mp4 -f mp4 result.mp4```

##### ②使用FFmpeg提供的concat分离器直接操作。
* 先创建一个文本文档filelist.txt
```
file 'file1.mkv'
file 'file2.mkv'
file 'file3.mkv'
```
* 再执行concat分离器命令

```ffmpeg -f concat -i filelist.txt -c copy output.mkv```

##### *[备用(有损)]合并不同编码器的视频，使用FFmpeg提供的concat过滤器。
```
ffmpeg
	-i input1.mp4 -i input2.webm -i input3.avi
	-filter_complex '[0:0] [0:1] [1:0] [1:1] [2:0] [2:1] concat=n=3:v=1:a=1 [v] [a]' -map '[v]' -map '[a]' <编码器选项> output.mkv
```
//如你所见，上面的命令合并了三种不同格式的文件，FFmpeg concat 过滤器会重新编码它们。注意这是有损压缩。[0:0] [0:1] [1:0] [1:1] [2:0] [2:1] 分别表示第一个输入文件的视频、音频、第二个输入文件的视频、音频、第三个输入文件的视频、音频。concat=n=3:v=1:a=1 表示有三个输入文件，输出一条视频流和一条音频流。[v] [a] 就是得到的视频流和音频流的名字，注意在 bash 等 shell 中需要用引号，防止通配符扩展。
