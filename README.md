# Social Listener

A social media monitoring tool that aggregates posts from multiple platforms (Reddit, Twitter, Bluesky, YouTube, Instagram) based on configured keywords and patterns. Built with FastAPI for high-performance async processing.

## What it does

- **Scans multiple social platforms** for content matching your keywords
- **AI-powered filtering** using OpenAI to identify promotion-worthy content
- **Automated email notifications** when relevant content is found
- **Real-time monitoring** with configurable intervals

## Architecture
```
┌─────────────────────────────────────────────────────────────────────────────┐
│                              SOCIAL LISTENER                                |
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  ┌─────────────────┐    ┌─────────────────┐    ┌─────────────────────────┐  │
│  │   FastAPI App   │    │   Middleware    │    │      Configuration      │  │
│  │   (main.py)     │    │  (Basic Auth)   │    │   (settings.py)         │  │
│  └─────────────────┘    └─────────────────┘    └─────────────────────────┘  │
│           │                       │                        │                │
│           ▼                       ▼                        ▼                │
│  ┌─────────────────────────────────────────────────────────────────────────┐│
│  │                              ROUTERS                                    ││
│  │  ┌─────────┐ ┌─────────┐ ┌─────────┐ ┌─────────┐ ┌─────────┐ ┌───────┐  ││
│  │  │ Reddit  │ │ Twitter │ │Bluesky  │ │ YouTube │ │Instagram│ │Aggreg.│  ││
│  │  └─────────┘ └─────────┘ └─────────┘ └─────────┘ └─────────┘ └───────┘  ││
│  └─────────────────────────────────────────────────────────────────────────┘│
│           │                       │                        │                │
│           ▼                       ▼                        ▼                │
│  ┌─────────────────────────────────────────────────────────────────────────┐│
│  │                              SERVICES                                   ││
│  │  ┌─────────────┐ ┌─────────────┐ ┌─────────────┐ ┌─────────────────────┐|| 
│  │  │ Reddit      │ │ Twitter     │ │ Bluesky     │ │ YouTube             │||
│  │  │ Service     │ │ Service     │ │ Service     │ │ Service             │||
│  │  └─────────────┘ └─────────────┘ └─────────────┘ └─────────────────────┘||
│  │                                                                         ||
│  │  ┌─────────────┐ ┌─────────────┐ ┌─────────────┐ ┌─────────────────────┐||
│  │  │ Instagram   │ │ Email       │ │ OpenAI      │ │ Matchers            │||
│  │  │ Service     │ │ Service     │ │ Service     │ │ (Question/Semantic) │||
│  │  └─────────────┘ └─────────────┘ └─────────────┘ └─────────────────────┘||
│  └─────────────────────────────────────────────────────────────────────────┘|
│           │                       │                        │                │
│           ▼                       ▼                        ▼                │
│  ┌─────────────────────────────────────────────────────────────────────────┐│
│  │                              MODELS                                     ││
│  │  ┌─────────────────────────────────────────────────────────────────────┐||
│  │  │                    SocialPost Model                                 │||
│  │  │  (platform, content, author, url, timestamp, keyword_matched, etc.) │||
│  │  └─────────────────────────────────────────────────────────────────────┘||
│  └─────────────────────────────────────────────────────────────────────────┘|
│                                                                             │
│  ┌─────────────────────────────────────────────────────────────────────────┐│
│  │                              EXTERNAL APIs                              ||
│  │  ┌─────────┐ ┌─────────┐ ┌─────────┐ ┌─────────┐ ┌─────────┐ ┌───────┐  ||
│  │  │ Reddit  │ │ Twitter │ │Bluesky  │ │ YouTube │ │Instagram│ │OpenAI │  ||
│  │  │   API   │ │   API   │ │   API   │ │   API   │ │   API   │ │  API  │  ||
│  │  └─────────┘ └─────────┘ └─────────┘ └─────────┘ └─────────┘ └───────┘  ||
│  └─────────────────────────────────────────────────────────────────────────┘|
│                                                                             │
│  ┌─────────────────────────────────────────────────────────────────────────┐│
│  │                              OUTPUT                                     ││
│  │  ┌─────────────────────────────────────────────────────────────────────┐|│
│  │  │                    Email Notifications                              │|│
│  │  │              (via Resend API)                                       │|│
│  │  └─────────────────────────────────────────────────────────────────────┘|│
│  └─────────────────────────────────────────────────────────────────────────┘│
```
## How it works

1. **API Endpoints** - FastAPI routes handle requests to scan different platforms
2. **Service Layer** - Each social platform has its own service that handles API calls and data processing
3. **Keyword Matching** - Content is filtered using configurable keywords and patterns
4. **AI Filtering** - OpenAI analyzes content to identify promotion-worthy posts
5. **Email Notifications** - Relevant content is automatically sent via email

## Quick Start

```bash
# Install dependencies
pip install -r requirements.txt

# Set up environment variables
cp env.example .env
# Edit .env with your API keys

# Run the application
uvicorn app.main:app --reload
```

## API Endpoints

- `GET /aggregate/scan` - Scan all platforms for matching content
- `GET /reddit/scan` - Scan Reddit specifically
- `GET /twitter/scan` - Scan Twitter specifically
- `GET /docs` - Interactive API documentation

## Technologies Used

- **FastAPI** - Modern, fast web framework
- **AsyncPRAW** - Reddit API client
- **Tweepy** - Twitter API client
- **Atproto** - Bluesky API client
- **Google API Client** - YouTube API
- **Instagrapi** - Instagram API
- **OpenAI** - AI content filtering
- **Resend** - Email notifications
