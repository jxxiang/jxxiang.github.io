---
title：Android录音AudioRecord和MediaRecorder
---

## AudioRecord

主要是实现边录边播（AudioRecord+AudioTrack）以及对音频的实时处理（如会说话的汤姆猫、语音）

优点：语音的实时处理，可以用代码实现各种音频的封装

缺点：输出是PCM语音数据，如果保存成音频文件，是不能够被播放器播放的，所以必须先写代码实现数据编码以及压缩

使用AudioRecord类录音，并实现WAV格式封装。录音20s，输出的音频文件大概为3.5M左右

## MediaRecorder

已经集成了录音、编码、压缩等，支持少量的录音音频格式，大概有.aac（API = 16） .amr .3gp

优点：大部分以及集成，直接调用相关接口即可，代码量小

缺点：无法实时处理音频；输出的音频格式不是很多，例如没有输出mp3格式文件

使用MediaRecorder类录音，输出amr格式文件。录音20s，输出的音频文件大概为33K