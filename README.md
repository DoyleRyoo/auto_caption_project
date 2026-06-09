# Auto Caption Project

A small Python CLI for creating text subtitles from a YouTube video.

The app downloads a video's audio as MP3 with `yt-dlp`, transcribes it with OpenAI Whisper, and writes the result to `output/subtitles/result.txt`.

## Features

- Downloads audio from a single YouTube URL.
- Converts the audio to MP3.
- Transcribes audio with the OpenAI `whisper-1` model.
- Saves the transcription as a UTF-8 text file.
- Includes an alternate chunk-aware script for large audio files.

## Project Structure

```text
.
├── main.py                  # Main CLI workflow
├── main2.py                 # Alternate workflow with chunk support
├── youtube_audio.py         # YouTube audio download helper
├── whisper_stt.py           # OpenAI Whisper transcription helper
├── input/audio/             # Downloaded audio files
└── output/subtitles/        # Generated transcription output
```

## Requirements

- Python 3.12 or compatible Python 3 version
- `ffmpeg`
- `yt-dlp`
- OpenAI API key

Python packages:

```bash
pip install openai python-dotenv yt-dlp
```

`yt-dlp` uses `ffmpeg` to extract and convert audio. Install it separately if it is not already available on your system.

## Setup

1. Create and activate a virtual environment:

```bash
python -m venv .venv
source .venv/bin/activate
```

2. Install dependencies:

```bash
pip install openai python-dotenv yt-dlp
```

3. Create a `.env` file in the project root:

```env
OPENAI_API_KEY=your_openai_api_key_here
```

4. Make sure `ffmpeg` is installed and available from the terminal:

```bash
ffmpeg -version
```

## Usage

Run the main script:

```bash
python main.py
```

When prompted, paste a YouTube URL:

```text
유튜브 URL을 입력하세요: https://www.youtube.com/watch?v=...
```

The script will:

1. Download the audio to `input/audio/audio.mp3`.
2. Send the MP3 file to OpenAI Whisper.
3. Save the transcription to `output/subtitles/result.txt`.

## Large Audio Files

`main2.py` supports transcribing pre-split audio chunks.

If `input/audio/chunks/` exists and contains files named like `part_*.mp3`, the script transcribes those files in sorted order and joins their text output:

```text
input/audio/chunks/
├── part_001.mp3
├── part_002.mp3
└── part_003.mp3
```

Run it with:

```bash
python main2.py
```

If no chunk directory exists, `main2.py` transcribes the downloaded `input/audio/audio.mp3` file directly.

## Output

Generated subtitle text is written to:

```text
output/subtitles/result.txt
```

`main.py` also formats sentence endings by inserting line breaks after `. ` and `? `.

## Troubleshooting

- `OPENAI_API_KEY가 설정되지 않았습니다`: Check that `.env` exists and contains `OPENAI_API_KEY`.
- `yt-dlp 실행 실패입니다`: Check that the YouTube URL is valid and that `ffmpeg` is installed.
- `mp3 파일이 생성되지 않았습니다`: Confirm that `yt-dlp` and `ffmpeg` are both available from your shell.
- Whisper upload errors: the audio file may be too large; split it into `input/audio/chunks/part_*.mp3` files and run `main2.py`.

## Notes

- The downloader uses `--no-playlist`, so only one video is processed from the provided URL.
- Existing `input/audio/audio.mp3` may be overwritten by the next download.
- `.env` and `.venv/` are ignored by git.
