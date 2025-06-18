---
title: Java使用ffmpeg
date: 2025-06-18
updated: 2025-06-18
url: /archives/java-ffmpeg
categories:
tags:
---


# ☕ Java 使用 FFmpeg 实战手册

如果你已经安装了 FFmpeg，可以直接通过 Java 的 `ProcessBuilder` 或 `Runtime.exec()` 来执行 FFmpeg 命令，实现音视频的转换、截图、压缩、录制等功能，无需引入任何第三方依赖。

---

## ✅ 前提条件

- 系统中已安装 FFmpeg 并配置在环境变量中（`ffmpeg` 可在终端执行）
- Java 版本 8+

---

## 🎵 1. Java 调用 FFmpeg 转换音频格式（如 pcm 24000 → pcm 16000）
        ProcessBuilder builder = new ProcessBuilder(
                "ffmpeg",
                "-loglevel", "error",        // 安静输出
                "-f", "f32le",
                "-ar", "24000",
                "-ac", "1",
                "-i", "pipe:0",              // 从 stdin 读入
                "-f", "s16le",
                "-ar", "16000",
                "-ac", "1",
                "pipe:1"                     // 输出到 stdout
        );
        Process process = builder.start();

        // 写入原始 PCM f32le 数据
        try (OutputStream stdin = process.getOutputStream()) {
            stdin.write(bytes);
            stdin.flush();
        }

        // 读取输出的 PCM s16le 数据
        ByteArrayOutputStream outputBuffer = new ByteArrayOutputStream();
        try (InputStream stdout = process.getInputStream()) {
            byte[] buffer = new byte[4096];
            int len;
            while ((len = stdout.read(buffer)) != -1) {
                outputBuffer.write(buffer, 0, len);
            }
        }

        // 等待 FFmpeg 执行完成
        int exitCode = process.waitFor();
        if (exitCode != 0) {
            throw new RuntimeException("FFmpeg process exited with code " + exitCode);
        }



## 📦 常用 FFmpeg 参数速查

| 参数 | 含义 |
|------|------|
| `-i input` | 输入文件 |
| `-f fmt` | 指定格式（如 `f32le`, `s16le`, `mp4` 等） |
| `-ar rate` | 音频采样率 |
| `-ac channels` | 声道数（1 = mono, 2 = stereo） |
| `-b:a` | 音频比特率（如 128k） |
| `-vf` | 视频滤镜，如 `fps=1` 截图 |
| `-t seconds` | 限制录制/输出时长 |
| `pipe:0 / pipe:1` | 读取 stdin / 写入 stdout |

---

## 🧪 建议工具与调试方式

- `ffmpeg -h` 查看帮助
- 使用 `builder.redirectErrorStream(true)` 合并错误输出便于调试
- 在本地命令行先试通 FFmpeg 命令，再用 Java 调用

---

## 📘 结语

Java 通过 `ProcessBuilder` 调用 FFmpeg 提供了轻量、灵活的音视频处理方案。适用于音频格式转换、实时推流、帧截取、录屏等多种场景，无需引入复杂依赖。如果需要更复杂的流式处理，可进一步使用 JavaCV 或 FFmpeg CLI 封装库。


## 🎬 第二章：使用 JavaCV 操作 FFmpeg（适合复杂音视频任务 ffmpeg 转音频有杂音原因未知 目前运用最多的是视频转码）

[JavaCV](https://github.com/bytedeco/javacv) 是对 FFmpeg、OpenCV 等原生库的 Java 封装，适合进行复杂的视频帧处理、实时音频流处理等任务。

### 🧰 添加依赖（Maven）

```xml
<dependency>
    <groupId>org.bytedeco</groupId>
    <artifactId>javacv-platform</artifactId>
    <version>1.5.10</version> <!-- 可替换为最新版本 -->
</dependency>
```

---

### 🎧 示例：将 PCM 音频流转为 MP3 文件

```java
import org.bytedeco.javacv.*;
import org.bytedeco.ffmpeg.global.avcodec;

public class PcmToMp3 {
    public static void main(String[] args) throws Exception {
        try (FFmpegFrameGrabber grabber = new FFmpegFrameGrabber("input.pcm");
             FFmpegFrameRecorder recorder = new FFmpegFrameRecorder("output.mp3", 1)) {

            grabber.setFormat("f32le"); // 原始 PCM 格式
            grabber.setSampleRate(24000);
            grabber.setAudioChannels(1);
            grabber.setAudioBitrate(128000);
            grabber.start();

            recorder.setFormat("mp3");
            recorder.setAudioCodec(avcodec.AV_CODEC_ID_MP3);
            recorder.setSampleRate(24000);
            recorder.setAudioChannels(1);
            recorder.start();

            Frame frame;
            while ((frame = grabber.grabFrame()) != null) {
                recorder.record(frame);
            }

            recorder.stop();
            grabber.stop();
        }
    }
}
```

---

## 📌 使用建议

- JavaCV 非常适合需要对帧进行逐帧处理的场景
- 不适用于简单的音频格式转换（用命令行更轻量）
- 可配合 Java NIO 管道、Netty 实现音视频实时处理

---

## 📚 更多资源

- JavaCV 文档：https://bytedeco.org/javacv/
- 示例项目：https://github.com/bytedeco/javacv-examples
- JavaCV 支持的 FFmpeg API 封装参考：`org.bytedeco.ffmpeg.*`

---
