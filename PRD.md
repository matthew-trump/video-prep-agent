# PRD: Video Download & Conversion Agent

## Overview
A FastAPI-based agentic system that automates the weekly podcast video preparation workflow: downloading videos from YouTube and X.com, converting them to mp4 with Handbrake, and organizing them for iMovie import.

## Goals
- Eliminate manual command-line video downloading and conversion
- Standardize file naming and organization
- Support section-based YouTube downloads to minimize file sizes
- Prepare videos ready for iMovie import with consistent quality settings

## Non-Goals (for v1)
- Frontend UI (future enhancement)
- Automatic iMovie project creation/import
- Video editing or merging
- Support for platforms beyond YouTube and X.com

## Technical Stack
- **Backend**: Python 3.11+ with FastAPI
- **Tools**: yt-dlp (CLI), HandBrakeCLI
- **Structure**: Monorepo with `/backend` and `/frontend` (placeholder for future)

## API Design

### POST /api/projects/prepare

**Request Body:**
```json
{
  "project_name": "podcast-2025-01-12",
  "output_base_path": "/Users/matt/Videos/PodcastProjects",
  "handbrake_preset": {
    "format": "mp4",
    "encoder": "x264",
    "quality": 22,
    "crop": "auto"  // or "none" or "top:bottom:left:right"
  },
  "videos": [
    {
      "url": "https://youtube.com/watch?v=xxxxx",
      "sections": "00:30-02:15",  // optional, YouTube only
      "custom_name": "interview-segment"  // optional
    },
    {
      "url": "https://x.com/user/status/xxxxx",
      "custom_name": "reaction-clip"
    }
  ]
}
```

**Response:**
```json
{
  "project_id": "podcast-2025-01-12",
  "status": "completed",
  "output_directory": "/Users/matt/Videos/PodcastProjects/podcast-2025-01-12",
  "processed_videos": [
    {
      "source_url": "https://youtube.com/watch?v=xxxxx",
      "output_file": "01-interview-segment.mp4",
      "status": "success",
      "file_size_mb": 45.2
    }
  ],
  "errors": []
}
```

## Agent Workflow

1. **Validate Input**: Check URLs, paths, and Handbrake settings
2. **Create Project Directory**: `{output_base_path}/{project_name}`
3. **Download Videos**: 
   - Use yt-dlp with appropriate flags for each source
   - For YouTube with sections: use `--download-sections` flag
   - Store in `{project_dir}/downloads/`
4. **Convert with Handbrake**:
   - Use HandBrakeCLI with specified preset
   - Output to `{project_dir}/ready-for-imovie/`
   - Apply naming convention: `{index:02d}-{custom_name or sanitized_title}.mp4`
5. **Cleanup**: Remove raw downloads, keep only converted files
6. **Return Report**: Summary of what was processed

## File Naming Convention
- Format: `{sequence_number}-{name}.mp4`
- Example: `01-interview-segment.mp4`, `02-reaction-clip.mp4`
- Sequence ensures proper ordering in iMovie
- Names are sanitized (lowercase, hyphens, alphanumeric only)

## Handbrake Presets
Default preset (user can override):
```
--format mp4 
--encoder x264 
--quality 22 
--crop {auto|none|manual}
--optimize (for web)
```

## Error Handling
- Invalid URLs: Return clear error, continue with other videos
- Download failures: Retry once, then report failure
- Handbrake failures: Keep original download, report conversion error
- Missing dependencies: Check for yt-dlp and HandBrakeCLI at startup

## Development Phases

### Phase 1: Core Backend (MVP)
- FastAPI setup with single endpoint
- yt-dlp integration for YouTube and X.com
- Basic file organization
- Simple synchronous processing

### Phase 2: Handbrake Integration
- HandBrakeCLI wrapper
- Preset configuration
- File naming and organization

### Phase 3: Agent Enhancement
- Async processing for multiple videos
- Progress tracking
- Better error recovery and reporting

### Phase 4: Frontend (Future)
- React+Vite UI
- Project management
- Preset templates

## Success Criteria
- Can process a list of 5 YouTube/X videos in one API call
- Videos correctly download with specified sections
- All videos converted to consistent mp4 format
- Files organized and named for easy iMovie import
- Total time < 5 minutes for typical weekly project

## Dependencies
- yt-dlp (must be installed: `pip install yt-dlp`)
- HandBrakeCLI (must be installed via Homebrew on macOS)
- Python 3.11+
- FastAPI, Pydantic, httpx

