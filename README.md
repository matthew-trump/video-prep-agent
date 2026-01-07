# video-prep-agent

A FastAPI-based agentic system that automates video preparation for podcast editing: downloading videos from YouTube and X.com, converting them to mp4 with Handbrake, and organizing them for iMovie import.

## Prerequisites

Before running this project, you need to install the following dependencies using Homebrew:

### Install yt-dlp
```bash
brew install yt-dlp
```

### Install HandBrakeCLI
```bash
brew install handbrake
```

### Verify Installation
```bash
yt-dlp --version
HandBrakeCLI --version
```

## Installation

```bash
# Install Python dependencies
pip install -r requirements.txt
```

## Usage

See [PRD.md](PRD.md) for detailed API design and workflow documentation.