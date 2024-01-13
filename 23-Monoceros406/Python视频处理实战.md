---
title: Python视频处理实战
date: 2024-01-04 18:56:02
tags: Python
mathjax: true
---

# Python视频处理实战

## 批量删除环境声并高速播放

```python
from moviepy.editor import VideoFileClip
from pathlib import Path
root_path=Path(__file__).parent
origin_folder=root_path/'origin'
result_folder=root_path/"result"
result_folder.mkdir(exist_ok=True)
for origin_file in origin_folder.iterdir():
    origin=VideoFileClip(str(origin_file),audio=False)
    result=result_folder.joinpath(origin_file.name)
    origin.speedx(15).write_videofile(str(result)) #15倍速
    origin.close()
```

VideoFileClip常用参数：

* filename
* has_mask：是否添加遮罩视频 可选 布尔 默认False不添加
* audio：是否有音频 可选 布尔 默认True含有音频
* target_resolution：是否调整画面尺寸 可选 元组(height,widtj) 默认None

## 批量添加背景音乐

```python
from moviepy.editor import VideoFileClip,AudioFileClip
from pathlib import Path
root_path=Path(__file__).parent
silent_folder=root_path/"silent"
audio_folder=root_path/"audio"
mp4_folder=root_path/'mp4'
mp4_folder.mkdir(exist_ok=True)
source=zip(silent_folder.iterdir(),audio_folder.iterdir())
for silent_file,audio_file in source:
    frame_clip=VideoFileClip(str(silent_file))
    audio_clip=AudioFileClip(str(audio_file))
    duration=frame_clip.duration
    frame_clip.audio=audio_clip.set_duration(t=duration)
    mp4_file=mp4_folder/(audio_file.stem+'.mp4')
    frame_clip.write_videofile(str(mp4_file))
    frame_clip.close()
    audio_clip.close()
```

## 批量导出视频背景音乐为

### moviepy法导出为wav

```python
from moviepy.editor import VideoFileClip
from pathlib import Path
root_path=Path(__file__).parent
video_folder=root_path/'video'
wav_folder=root_path/'wav'
wav_folder.mkdir(exist_ok=True)
for video_file in video_folder.iterdir():
    video_clip=VideoFileClip(str(video_file))
    wav_clip=video_clip.audio
    wav_file=wav_folder/(video_file.stem+'.wav')
    wav_clip.write_audiofile(str(wav_file))
    video_clip.close()
    wav_clip.close()
```

### office法导出为mp3

```python
from office import video
from pathlib import Path
root_path=Path(__file__).parent
mp4_folder=root_path/'mp4'
mp3_folder=root_path/'mp3'
mp3_folder.mkdir(exist_ok=True)
for mp4_file in mp4_folder.iterdir():
    mp3_file=mp3_folder/mp4_file.stem
    video.video2mp3(path=str(mp4_file),mp3_name=str(mp3_file))
```

## 批量截屏生成字幕长图

```python
from moviepy.editor import VideoFileClip
from PIL import Image
from pathlib import Path
root_path=Path(__file__).parent
mp4_file=root_path/'运动.mp4'
pic_file=root_path/'运动.jpg'
mp4_clip=VideoFileClip(str(mp4_file))
width,height=mp4_clip.size
during=mp4_clip.duration
frame_list=[]
for i in range(1,int(during),6):
    frame=mp4_clip.get_frame(t=i)
    frame_list.append(frame)
mp4_clip.close()
frame_list=frame_list[::-1]
height+=(len(frame_list)-1)*100
img_result=Image.new('RGB',(width,height))
for i,frame in enumerate(frame_list):
    img=Image.fromarray(frame)
    top=(len(frame_list)-i-1)*100
    img_result.paste(im=img,box=(0,top))
    img.close()
img_result.save(pic_file)
img_result.close()
```

## 批量视频剪辑

### 指定时间间隔进行视频分割

```python
from moviepy.editor import VideoFileClip
from pathlib import Path
root_path=Path(__file__).parent
long_file=root_path/'运动.mp4'
split_folder=root_path/'split'
split_folder.mkdir(exist_ok=True)
def split_mp4(clip_file,time_list,folder):
    clip=VideoFileClip(str(clip_file))
    duration=clip.duration
    for i,start in enumerate(time_list):
        if i==len(time_list)-1:
            end=int(dutation)
        else:
            end=time_list[i+1]
        sub_clip=clip.subclip(start,end)
        mp4_file=clip_file.stem+str(i)+'.mp4'
        sub_file=folder/mp4_file
        sub_clip.write_videofile(str(sub_file))
    clip.close()
    sub_clip.close()
time_list=['0:0:0','0:0:10','0:0:20']
split_mp4(long_file,time_list,split_folder)
```

### 多段视频合成一段

```python
from moviepy.editor import VideoFileClip,concatenate_videoclips
from pathlib import Path
root_path=Path(__file__).parent
split_folder=root_path/'split'
merge_folder=root_path/'merge'
merge_folder.mkdir(exist_ok=True)
for sub_folder in split_folder.iterdir():
    clips=[VideoFileClip(filename=str(file)) for file in sub_folder.iterdir()]
    merge_clip=concatenate_videoclips(clips=clips)
    merge_file=sub_folder.name+'.mp4'
    merge_path=merge_folder/merge_file
    merge_clip.write_videofile(str(merge_path))
    merge_clip.close()
    for clip in clips:
        clip.close()
```

当视频尺寸不一样时，解决方法有俩：

法一：第8行改为：

```python
clips=[VideoFileClip(filename=str(file),target_resolution=(1080,1920)) for file in sub_folder.iterdir()]
```

法二：第9行改为：

```python
merge_clip=concatenate_videoclips(clips=clips,method='compose')
```

### 多段视频合成四分屏

```python
from moviepy.editor import VideoFileClip,clips_array,VideoClip
from PIL import Image
import numpy
from pathlib import Path
root_path=Path(__file__).parent
split_folder=root_path/'split'
stack_folder=root_path/'stack'
stack_folder.mkdir(exist_ok=True)
def make_frame(t):
    arr=numpy.zeros((720,1280))
    img=Image.fromarray(arr).convert('RGB')
    return numpy.array(img)
for sub_folder in split_folder.iterdir():
    clips=[VideoFileClip(str(file)) for file in sub_folder.iterdit()]
    if len(clips)%2>0:
        clip=VideoClip(make_frame,duration=1)
        clips.append(clip)
    stack_list=numpy.array(clips).reshape(-1,2).tolist() #reshape转为若干行x2列
    stack_clip=clips_array(stack_list)
    stack_file=sub_folder.name+'.mp4'
    stack_path=stack_folder.joinpath(stack_file)
    stack_clip.write_videofile(str(stack_path))
    stack_clip.close()
    for clip in clips:
        clip.close()
```

