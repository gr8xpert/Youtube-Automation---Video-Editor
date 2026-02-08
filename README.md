# YouTube Automation - Video Editor

Automated video generation workflow for n8n that creates videos from images/clips with voiceover, subtitles, and background music.

## Features

- **Automated Video Creation** - Combine images/video clips with voiceover audio
- **Smart Scene Detection** - Automatically syncs visuals to voiceover timing
- **Ken Burns Effect** - Smooth zoom animations on images
- **Custom Subtitles** - Full styling control (font, size, color, position, background)
- **Custom Fonts** - Load any TTF/OTF font from URL
- **Multiple Aspect Ratios** - 16:9 (YouTube), 9:16 (TikTok/Reels), 1:1 (Instagram)
- **Preview Mode** - Fast low-quality renders for quick review
- **Auto-Retry** - Failed jobs automatically retry up to 3 times
- **Google Sheets Integration** - Manage video queue via spreadsheet

## Versions

| Version | File | Description |
|---------|------|-------------|
| V3 | `Automated Video - Perfect Sync v3.json` | Original with Airtable integration |
| V4 | `Automated Video - Perfect Sync v4.json` | Google Sheets + Custom Fonts + ASS Subtitles |
| V5 | `Automated Video - Perfect Sync v5.json` | Preview Mode + Aspect Ratios + Auto-Retry |

## Requirements

- [n8n](https://n8n.io/) (self-hosted or cloud)
- Google Cloud account (for Sheets & Drive APIs)
- OpenAI API key (for Whisper transcription)
- FFmpeg (included in n8n Docker image)

## Setup

### 1. Import Workflow

1. Download the desired version JSON file
2. In n8n, go to **Workflows** > **Import from File**
3. Select the JSON file

### 2. Configure Credentials

Replace these placeholders in the workflow:

| Placeholder | Description |
|-------------|-------------|
| `YOUR_OPENAI_API_KEY_HERE` | OpenAI API key for Whisper |
| `YOUR_GOOGLE_API_KEY` | Google Cloud API key |
| `YOUR_GOOGLE_SHEETS_ID` | Your Google Sheets document ID |
| `YOUR_SHEETS_CREDENTIAL_ID` | n8n Google Sheets credential ID |
| `YOUR_DRIVE_CREDENTIAL_ID` | n8n Google Drive credential ID |

### 3. Create Google Sheet

Create a Google Sheet with these columns:

| Column | Type | Description |
|--------|------|-------------|
| Name | Text | Video name (used for output filename) |
| Status | Text | Pending, Processing, Complete, Failed, Retry |
| Images Folder ID | Text | Google Drive folder ID with images/clips |
| Voiceover File ID | Text | Google Drive file ID of voiceover audio |
| Subtitle Enable | Checkbox | Enable/disable subtitles |
| Subtitle Font | Text | Font family name (e.g., "Arial", "Allura") |
| Subtitle Font URL | Text | URL to TTF/OTF font file (optional) |
| Subtitle Font Size | Number | Font size in pixels (default: 36) |
| Subtitle Font Color | Text | Hex color (e.g., #FFFFFF) |
| Subtitle Background Color | Text | Hex color (e.g., #000000) |
| Subtitle Position | Text | top, center, bottom |
| Subtitle Outline | Number | Outline width (0 for none) |
| Subtitle Width | Number | Width as percentage (default: 90) |
| Output URL | Text | Auto-filled with final video URL |

### V5 Additional Columns

| Column | Type | Description |
|--------|------|-------------|
| Preview | Checkbox | Enable preview mode (fast, low quality) |
| Aspect Ratio | Text | 16:9, 9:16, or 1:1 |
| Retry Count | Number | Auto-updated on failures |
| Error Message | Text | Auto-updated with error details |

## Usage

1. Add a row to your Google Sheet with:
   - Name for the video
   - Status = "Pending"
   - Images Folder ID (Google Drive folder with numbered images: 1.jpg, 2.jpg, etc.)
   - Voiceover File ID (Google Drive file ID of MP3/WAV audio)
   - Subtitle settings as desired

2. Run the workflow manually or set up a scheduled trigger

3. The workflow will:
   - Download media files
   - Detect scene timings from voiceover
   - Generate video clips with Ken Burns effect
   - Add subtitles with your styling
   - Concatenate all clips
   - Upload final video to Google Drive
   - Update the sheet with output URL

## Quality Settings

### Full Quality (Default)
- Resolution: 1920x1080 (16:9), 1080x1920 (9:16), 1080x1080 (1:1)
- Preset: medium
- CRF: 20
- FPS: 30
- Audio: 192kbps

### Preview Mode
- Resolution: 640x360 (16:9), 360x640 (9:16), 360x360 (1:1)
- Preset: ultrafast
- CRF: 35
- FPS: 24
- Audio: 96kbps

## Restoring Previous Versions

```bash
# Clone the repo
git clone https://github.com/gr8xpert/Youtube-Automation---Video-Editor.git
cd Youtube-Automation---Video-Editor

# Checkout specific version
git checkout v3  # Original Airtable version
git checkout v4  # Google Sheets + Custom Fonts
git checkout v5  # Latest with all features

# Return to latest
git checkout main
```

## Troubleshooting

### Subtitles not showing custom font
- Ensure the font URL is accessible (try opening in browser)
- Check that "Subtitle Font" column matches the font's family name
- The workflow uses `fc-query` to detect the correct font family name

### Video generation fails
- Check that Images Folder ID contains files named numerically (1.jpg, 2.jpg, etc.)
- Ensure the number of images matches the number of scenes in voiceover
- Verify voiceover file is a valid audio format (MP3, WAV)

### Auto-retry not working (V5)
- Ensure "Retry Count" column exists in your sheet
- Check that the Error Trigger node is connected
- Maximum retries is set to 3 by default

## License

MIT License

## Credits

Built with [n8n](https://n8n.io/) workflow automation.

Co-Authored-By: Claude Opus 4.5 <noreply@anthropic.com>
