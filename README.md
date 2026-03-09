# OpenClaw Feishu Plugin (Custom Fork)

Custom fork of [@openclaw/feishu](https://www.npmjs.com/package/@openclaw/feishu) with enhanced media format conversion support.

## Features

### Audio Conversion Support
- Automatically converts audio files (MP3, WAV, M4A, AAC, FLAC, WMA) to Opus format
- Feishu requires: Opus codec, 24kHz sample rate, mono channel, 24kbps bitrate
- Sends converted audio as playable voice messages (not file attachments)

### Video Conversion Support
- Automatically converts video files (MOV, AVI, MKV, WebM, WMV, FLV) to MP4 format
- Feishu requires: H.264 video codec, AAC audio codec, MP4 container
- Sends converted videos as playable media messages

## Requirements

- **ffmpeg** and **ffprobe** must be installed on the server
- Node.js >= 22

### Installing ffmpeg

**Ubuntu/Debian:**
```bash
sudo apt update
sudo apt install ffmpeg
```

**CentOS/RHEL:**
```bash
sudo yum install epel-release
sudo yum install ffmpeg ffmpeg-devel
```

**macOS:**
```bash
brew install ffmpeg
```

## Installation

### From GitHub (Recommended)

```bash
cd ~/.openclaw/extensions
git clone https://github.com/DrayChou/openclaw-extensions-feishu.git feishu-custom
cd feishu-custom
npm install
```

### Update OpenClaw Config

Add the local plugin path to your `~/.openclaw/openclaw.json`:

```json
{
  "plugins": {
    "load": {
      "paths": ["/path/to/.openclaw/extensions/feishu-custom"]
    },
    "entries": {
      "feishu": {
        "enabled": false
      },
      "feishu-custom": {
        "enabled": true
      }
    }
  }
}
```

## Supported Formats

### Audio
| Format | Extension | Conversion |
|--------|-----------|------------|
| Opus/Ogg | .opus, .ogg | None (native) |
| MP3 | .mp3 | Convert to Opus |
| WAV | .wav | Convert to Opus |
| M4A | .m4a | Convert to Opus |
| AAC | .aac | Convert to Opus |
| FLAC | .flac | Convert to Opus |
| WMA | .wma | Convert to Opus |

### Video
| Format | Extension | Conversion |
|--------|-----------|------------|
| MP4 | .mp4, .m4v | None (native) |
| MOV | .mov | Convert to MP4 |
| AVI | .avi | Convert to MP4 |
| MKV | .mkv | Convert to MP4 |
| WebM | .webm | Convert to MP4 |
| WMV | .wmv | Convert to MP4 |
| FLV | .flv | Convert to MP4 |

## How It Works

1. When sending media, the plugin checks the file extension
2. For audio files:
   - If already Opus/Ogg: upload directly as audio
   - Otherwise: convert to Opus using ffmpeg, then upload
3. For video files:
   - If already MP4: upload directly as media
   - Otherwise: convert to H.264/AAC/MP4 using ffmpeg, then upload
4. Temp files are automatically cleaned up after processing

## Technical Details

### Audio Conversion Parameters
```
ffmpeg -i input.mp3 \
  -c:a libopus \
  -b:a 24k \
  -ar 24000 \
  -ac 1 \
  output.opus
```

### Video Conversion Parameters
```
ffmpeg -i input.mkv \
  -c:v libx264 \
  -preset fast \
  -crf 23 \
  -c:a aac \
  -b:a 128k \
  -movflags +faststart \
  output.mp4
```

## Differences from Official Plugin

| Feature | Official | This Fork |
|---------|----------|-----------|
| MP3 audio | Sent as file | Converted to Opus voice message |
| Other audio formats | Sent as file | Converted to Opus voice message |
| Non-MP4 video | Sent as file | Converted to MP4 media message |
| ffmpeg dependency | Not required | Required |

## License

MIT

## Credits

- Original plugin: [OpenClaw](https://github.com/openclaw/openclaw)
- Maintained by: @m1heng (official), @DrayChou (this fork)
