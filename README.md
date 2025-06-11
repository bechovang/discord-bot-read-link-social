# discord-bot-read-link-social
# Discord Bot Planning - Facebook & YouTube Link Preview

## 1. Mục tiêu và yêu cầu

### Mục tiêu chính:
- Tự động phát hiện link Facebook và YouTube trong tin nhắn Discord
- Hiển thị thumbnail, tiêu đề, và thông tin cơ bản của link
- Tạo embed message đẹp mắt và dễ theo dõi

### Yêu cầu kỹ thuật:
- Bot hoạt động 24/7
- Phản hồi nhanh (< 3 giây)
- Hỗ trợ multiple channels
- Error handling tốt

## 2. Tech Stack được đề xuất

### Backend:
- **Node.js** với Discord.js v14 (framework chính)
- **Puppeteer** hoặc **Playwright** (web scraping)
- **Express.js** (web server cho health check)
- **MongoDB** hoặc **SQLite** (lưu cache và settings)

### APIs cần sử dụng:
- **Discord Bot API** (Discord.js)
- **YouTube Data API v3** (official API)
- **Open Graph Protocol** scraper cho Facebook
- **Cheerio** (HTML parsing)

### Hosting:
- **Railway**, **Heroku**, hoặc **VPS** (24/7 uptime)
- **Ngrok** (development testing)

## 3. Kiến trúc hệ thống

```
Discord Message → Bot Listener → Link Detector → Content Scraper → Embed Builder → Discord Response
```

### Các component chính:
1. **Message Listener**: Lắng nghe tin nhắn trong channels
2. **Link Parser**: Phát hiện và validate links
3. **Content Scraper**: Lấy metadata từ các platform
4. **Embed Builder**: Tạo Discord embed messages
5. **Cache Manager**: Lưu trữ kết quả để tăng tốc độ
6. **Error Handler**: Xử lý lỗi và fallback

## 4. Chi tiết implementation

### 4.1 Setup Discord Bot
```javascript
// Cần tạo bot trên Discord Developer Portal
// Permissions cần thiết:
- Read Messages/View Channels
- Send Messages
- Embed Links
- Read Message History
```

### 4.2 Link Detection
```javascript
// Regex patterns cần thiết:
const YOUTUBE_REGEX = /(?:youtube\.com\/watch\?v=|youtu\.be\/)([a-zA-Z0-9_-]+)/
const FACEBOOK_REGEX = /(?:facebook\.com|fb\.com)\/.*\/(?:posts|videos)\/([0-9]+)/
```

### 4.3 Content Scraping Strategy

#### YouTube:
- **Option 1**: YouTube Data API (official, stable, có quota limit)
- **Option 2**: yt-dlp wrapper (unlimited nhưng có thể bị block)
- **Option 3**: Web scraping với Puppeteer (backup method)

#### Facebook:
- **Challenges**: Facebook có anti-bot mạnh
- **Solutions**: 
  - Open Graph meta tags scraping
  - Facebook Graph API (cần permissions)
  - Headless browser với user-agent rotation

### 4.4 Database Schema
```sql
-- Cache table
CREATE TABLE link_cache (
    id INTEGER PRIMARY KEY,
    url TEXT UNIQUE,
    title TEXT,
    description TEXT,
    thumbnail TEXT,
    duration TEXT,
    author TEXT,
    created_at TIMESTAMP,
    expires_at TIMESTAMP
);

-- Bot settings
CREATE TABLE bot_settings (
    guild_id TEXT,
    channel_id TEXT,
    auto_preview BOOLEAN DEFAULT true,
    created_at TIMESTAMP
);
```

## 5. Workflow chi tiết

### Step 1: Project Setup (1-2 ngày)
1. Tạo Discord Application và Bot
2. Setup Node.js project với dependencies
3. Cấu hình environment variables
4. Test basic bot connection

### Step 2: Core Features (3-4 ngày)
1. Implement message listener
2. Link detection và parsing
3. Basic embed message creation
4. Test với sample links

### Step 3: Content Scraping (4-5 ngày)
1. YouTube API integration
2. Facebook scraping implementation
3. Error handling và fallbacks
4. Cache mechanism

### Step 4: Advanced Features (2-3 ngày)
1. Multiple formats support
2. User preferences
3. Admin commands
4. Rate limiting

### Step 5: Testing & Deployment (2-3 ngày)
1. Comprehensive testing
2. Performance optimization
3. Deployment setup
4. Monitoring và logging

## 6. Code Structure

```
discord-preview-bot/
├── src/
│   ├── bot/
│   │   ├── index.js          # Main bot file
│   │   ├── commands/         # Slash commands
│   │   └── events/           # Event handlers
│   ├── scrapers/
│   │   ├── youtube.js        # YouTube scraper
│   │   ├── facebook.js       # Facebook scraper
│   │   └── base.js           # Base scraper class
│   ├── utils/
│   │   ├── linkParser.js     # Link detection
│   │   ├── embedBuilder.js   # Discord embeds
│   │   └── cache.js          # Cache management
│   ├── database/
│   │   ├── models/           # Database models
│   │   └── migrations/       # DB migrations
│   └── config/
│       └── index.js          # Configuration
├── package.json
├── .env.example
└── README.md
```

## 7. Environment Variables cần thiết

```bash
# Discord
DISCORD_TOKEN=your_bot_token
DISCORD_CLIENT_ID=your_client_id

# YouTube
YOUTUBE_API_KEY=your_youtube_api_key

# Database
DATABASE_URL=your_database_url

# Optional
PORT=3000
NODE_ENV=production
CACHE_TTL=3600
```

## 8. Các thử thách và giải pháp

### Thử thách 1: Facebook Anti-Bot
**Giải pháp**:
- Sử dụng rotating user agents
- Implement delays between requests
- Fallback to Open Graph tags
- Consider using Facebook Graph API

### Thử thách 2: Rate Limiting
**Giải pháp**:
- Implement caching với TTL
- Queue system cho requests
- Graceful degradation
- User notification về limits

### Thử thách 3: Performance
**Giải pháp**:
- Async/await everywhere
- Connection pooling
- Image compression
- CDN cho cached thumbnails

## 9. Monitoring và Maintenance

### Logging:
- Request/response logs
- Error tracking (Sentry recommended)
- Performance metrics
- API quota usage

### Health Checks:
- Bot uptime monitoring
- API endpoints status
- Database connectivity
- Memory usage tracking

## 10. Timeline tổng quan

| Week | Tasks | Deliverables |
|------|-------|-------------|
| Week 1 | Setup + Core Features | Working bot with basic link detection |
| Week 2 | Scraping Implementation | YouTube và Facebook preview working |
| Week 3 | Advanced Features + Testing | Production-ready bot |
| Week 4 | Deployment + Monitoring | Live bot với monitoring |

## 11. Budget ước tính (monthly)

- **Hosting**: $5-10 (Railway/Heroku)
- **YouTube API**: Free (có quota limit)
- **Database**: Free tier available
- **Monitoring**: Free tier available
- **Total**: ~$5-15/month

## 12. Getting Started Commands

```bash
# 1. Initialize project
npm init -y
npm install discord.js puppeteer express sqlite3 node-cron

# 2. Create bot structure
mkdir -p src/{bot,scrapers,utils,database,config}

# 3. Start development
npm run dev
```

Bạn có muốn tôi giúp implement phần nào cụ thể đầu tiên không?


--------------------------------------------------------------------------


# Discord Bot Planning - GCP Edition với $300 Credit

## 1. Google Cloud Architecture Overview

### Core Services sử dụng:
- **Cloud Run**: Host Discord bot (serverless, auto-scale)
- **Cloud Functions**: Xử lý webhook và background tasks
- **Firestore**: NoSQL database cho cache và settings
- **Cloud Storage**: Lưu thumbnails và media cache
- **Cloud Scheduler**: Cron jobs cho maintenance
- **Cloud Build**: CI/CD pipeline
- **YouTube Data API**: Free với GCP integration
- **Vision API**: OCR cho Facebook images (nếu cần)

### Estimated Monthly Cost (sau khi hết credit):
- Cloud Run: ~$5-15
- Firestore: ~$1-5  
- Cloud Storage: ~$1-3
- Other services: ~$2-5
- **Total**: ~$10-30/month (rất hợp lý)

## 2. Updated Tech Stack - GCP Native

### Backend Services:
```
Discord Bot (Cloud Run) ←→ Firestore Database
     ↓                           ↑
Cloud Functions ←→ Cloud Storage (thumbnails)
     ↓                           ↑
Cloud Scheduler ←→ YouTube Data API (GCP)
```

### Development Stack:
- **Node.js 18+** với Discord.js v14
- **@google-cloud/firestore** (database)
- **@google-cloud/storage** (file storage)
- **@google-cloud/functions-framework** (cloud functions)
- **Puppeteer** cho Facebook scraping
- **Docker** containers cho Cloud Run

## 3. GCP Project Setup

### 3.1 Initial GCP Setup (30 phút)
```bash
# 1. Tạo new GCP project
gcloud projects create discord-preview-bot-$(date +%s)
gcloud config set project discord-preview-bot-$(date +%s)

# 2. Enable required APIs
gcloud services enable run.googleapis.com
gcloud services enable cloudfunctions.googleapis.com
gcloud services enable firestore.googleapis.com
gcloud services enable storage.googleapis.com
gcloud services enable youtube.googleapis.com
gcloud services enable cloudbuild.googleapis.com
gcloud services enable cloudscheduler.googleapis.com

# 3. Create service account
gcloud iam service-accounts create discord-bot-service \
    --display-name="Discord Bot Service Account"
```

### 3.2 Firestore Database Setup
```bash
# Initialize Firestore in native mode
gcloud firestore databases create --region=asia-southeast1
```

### 3.3 Cloud Storage Buckets
```bash
# Bucket cho thumbnails cache
gsutil mb -l asia-southeast1 gs://discord-bot-thumbnails-$(date +%s)

# Bucket cho temporary files
gsutil mb -l asia-southeast1 gs://discord-bot-temp-$(date +%s)
```

## 4. Cloud Run Main Bot Service

### 4.1 Dockerfile
```dockerfile
FROM node:18-alpine

WORKDIR /app
COPY package*.json ./
RUN npm ci --only=production

COPY . .
EXPOSE 8080

CMD ["npm", "start"]
```

### 4.2 Cloud Run Configuration
```yaml
# cloudbuild.yaml
steps:
  - name: 'gcr.io/cloud-builders/docker'
    args: ['build', '-t', 'gcr.io/$PROJECT_ID/discord-bot', '.']
  - name: 'gcr.io/cloud-builders/docker'
    args: ['push', 'gcr.io/$PROJECT_ID/discord-bot']
  - name: 'gcr.io/cloud-builders/gcloud'
    args:
      - 'run'
      - 'deploy'
      - 'discord-bot'
      - '--image'
      - 'gcr.io/$PROJECT_ID/discord-bot'
      - '--region'
      - 'asia-southeast1'
      - '--platform'
      - 'managed'
      - '--allow-unauthenticated'
      - '--memory'
      - '1Gi'
      - '--cpu'
      - '1'
      - '--max-instances'
      - '10'
      - '--concurrency'
      - '80'
```

## 5. Firestore Database Schema

### Collections Structure:
```javascript
// /link_cache/{hash}
{
  url: "https://youtube.com/watch?v=...",
  title: "Video Title",
  description: "Video description...",
  thumbnail: "gs://bucket/thumb.jpg",
  duration: "PT4M13S",
  author: "Channel Name",
  platform: "youtube",
  cached_at: Timestamp,
  expires_at: Timestamp,
  view_count: 1000000,
  like_count: 50000
}

// /guild_settings/{guild_id}
{
  auto_preview: true,
  allowed_channels: ["channel1", "channel2"],
  prefix: "!",
  premium: false,
  created_at: Timestamp
}

// /usage_stats/{date}
{
  requests_count: 1500,
  cache_hits: 800,
  cache_misses: 700,
  errors: 5,
  platforms: {
    youtube: 900,
    facebook: 600
  }
}
```

## 6. Cloud Functions cho Background Tasks

### 6.1 YouTube Data Fetcher
```javascript
// functions/youtube-fetcher/index.js
const {onRequest} = require("firebase-functions/v2/https");
const {initializeApp} = require("firebase-admin/app");
const {getFirestore} = require("firebase-admin/firestore");

initializeApp();

exports.fetchYouTubeData = onRequest(async (req, res) => {
  const {videoId} = req.body;
  
  try {
    // Fetch from YouTube API
    const videoData = await fetchYouTubeVideo(videoId);
    
    // Save to Firestore
    const db = getFirestore();
    await db.collection('link_cache').doc(videoId).set(videoData);
    
    res.json({success: true, data: videoData});
  } catch (error) {
    res.status(500).json({error: error.message});
  }
});
```

### 6.2 Facebook Scraper Function
```javascript
// functions/facebook-scraper/index.js
const puppeteer = require('puppeteer');

exports.scrapeFacebook = onRequest(async (req, res) => {
  const {url} = req.body;
  
  const browser = await puppeteer.launch({
    args: ['--no-sandbox', '--disable-setuid-sandbox']
  });
  
  try {
    const page = await browser.newPage();
    await page.setUserAgent('Mozilla/5.0...');
    await page.goto(url, {waitUntil: 'networkidle0'});
    
    const data = await page.evaluate(() => {
      // Extract Open Graph data
      const title = document.querySelector('meta[property="og:title"]')?.content;
      const description = document.querySelector('meta[property="og:description"]')?.content;
      const image = document.querySelector('meta[property="og:image"]')?.content;
      
      return {title, description, image};
    });
    
    res.json({success: true, data});
  } catch (error) {
    res.status(500).json({error: error.message});
  } finally {
    await browser.close();
  }
});
```

## 7. Main Bot Code Structure

```
discord-bot-gcp/
├── src/
│   ├── index.js              # Main Cloud Run entry point
│   ├── bot/
│   │   ├── client.js         # Discord client setup
│   │   ├── events/           # Event handlers
│   │   └── commands/         # Slash commands
│   ├── services/
│   │   ├── firestore.js      # Database service
│   │   ├── storage.js        # Cloud Storage service
│   │   ├── youtube.js        # YouTube API service
│   │   └── scraper.js        # Web scraping service
│   ├── utils/
│   │   ├── linkParser.js     # URL parsing
│   │   ├── embedBuilder.js   # Discord embeds
│   │   └── cache.js          # Cache management
│   └── config/
│       └── gcp.js            # GCP configuration
├── functions/
│   ├── youtube-fetcher/      # Cloud Function
│   └── facebook-scraper/     # Cloud Function
├── Dockerfile
├── cloudbuild.yaml
├── package.json
└── .env.example
```

## 8. Environment Variables cho GCP

```bash
# Discord
DISCORD_TOKEN=your_bot_token
DISCORD_CLIENT_ID=your_client_id

# GCP
GOOGLE_CLOUD_PROJECT=your-project-id
FIRESTORE_DATABASE_ID=(default)
STORAGE_BUCKET_THUMBNAILS=your-thumbnails-bucket
STORAGE_BUCKET_TEMP=your-temp-bucket

# YouTube (GCP integrated)
YOUTUBE_API_KEY=your_gcp_youtube_key

# Cloud Functions URLs
YOUTUBE_FUNCTION_URL=https://region-project.cloudfunctions.net/fetchYouTubeData
FACEBOOK_FUNCTION_URL=https://region-project.cloudfunctions.net/scrapeFacebook

# Performance
MAX_CONCURRENT_REQUESTS=50
CACHE_TTL_SECONDS=3600
```

## 9. Monitoring với GCP

### 9.1 Cloud Monitoring Setup
```javascript
// Add to main bot code
const {Monitoring} = require('@google-cloud/monitoring');
const monitoring = new Monitoring.MetricServiceClient();

// Custom metrics
async function recordMetric(metricType, value) {
  const request = {
    name: monitoring.projectPath(process.env.GOOGLE_CLOUD_PROJECT),
    timeSeries: [{
      metric: {type: `custom.googleapis.com/${metricType}`},
      points: [{
        interval: {endTime: {seconds: Date.now() / 1000}},
        value: {doubleValue: value}
      }]
    }]
  };
  
  await monitoring.createTimeSeries(request);
}
```

### 9.2 Logging
```javascript
const {Logging} = require('@google-cloud/logging');
const logging = new Logging();
const log = logging.log('discord-bot');

// Structured logging
function logInfo(message, metadata = {}) {
  const entry = log.entry({
    severity: 'INFO',
    timestamp: new Date(),
    labels: {service: 'discord-bot'}
  }, {message, ...metadata});
  
  log.write(entry);
}
```

## 10. Cost Optimization Strategies

### Free Tier Utilization:
- **Cloud Run**: 2 million requests/month free
- **Firestore**: 1GB storage + 50K reads/day free
- **Cloud Functions**: 2 million invocations/month free
- **Cloud Storage**: 5GB free
- **YouTube API**: 10,000 units/day free

### Smart Caching:
- Cache YouTube data 24h (ít thay đổi)
- Cache Facebook data 6h (thay đổi nhiều hơn)
- Compress thumbnails trước khi lưu
- Use Cloud CDN cho static assets

## 11. Deployment Commands

```bash
# 1. Setup GCP CLI
gcloud auth login
gcloud config set project YOUR_PROJECT_ID

# 2. Deploy Cloud Functions
cd functions/youtube-fetcher
gcloud functions deploy fetchYouTubeData --runtime nodejs18 --trigger-http

cd ../facebook-scraper  
gcloud functions deploy scrapeFacebook --runtime nodejs18 --trigger-http --memory 1GB

# 3. Deploy main bot to Cloud Run
cd ../../
gcloud builds submit --config cloudbuild.yaml

# 4. Setup Cloud Scheduler (optional)
gcloud scheduler jobs create http cache-cleanup \
    --schedule="0 2 * * *" \
    --uri="https://your-bot-url/cleanup" \
    --http-method=POST
```

## 12. Advanced GCP Features

### 12.1 Auto-scaling Configuration
```yaml
# cloud-run-service.yaml
apiVersion: serving.knative.dev/v1
kind: Service
metadata:
  annotations:
    run.googleapis.com/ingress: all
    run.googleapis.com/cpu-throttling: "false"
spec:
  template:
    metadata:
      annotations:
        autoscaling.knative.dev/minScale: "1"
        autoscaling.knative.dev/maxScale: "100"
        run.googleapis.com/memory: "1Gi"
        run.googleapis.com/cpu: "1"
```

### 12.2 Health Checks
```javascript
// Health check endpoint
app.get('/health', async (req, res) => {
  try {
    // Check Firestore connection
    await db.collection('health').doc('test').get();
    
    // Check Discord connection
    const ping = client.ws.ping;
    
    res.json({
      status: 'healthy',
      timestamp: new Date().toISOString(),
      discord_ping: ping,
      uptime: process.uptime()
    });
  } catch (error) {
    res.status(500).json({
      status: 'unhealthy',
      error: error.message
    });
  }
});
```

## 13. Timeline với GCP (Nhanh hơn)

| Week | GCP Tasks | Deliverables |
|------|-----------|-------------|
| Week 1 | GCP setup + Basic Cloud Run bot | Bot running on GCP |
| Week 2 | Firestore + Cloud Functions | Full scraping system |
| Week 3 | Monitoring + Optimization | Production ready |
| Week 4 | Advanced features + CI/CD | Fully automated |

**Lợi ích GCP:**
- Tận dụng $300 credit (có thể chạy free 6-12 tháng)
- Auto-scaling không cần lo về traffic
- Integrated monitoring và logging
- YouTube API free tier generous
- Serverless = không cần maintain server

Bạn muốn bắt đầu setup GCP project ngay không? Tôi có thể hướng dẫn step-by-step!
