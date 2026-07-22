---
name: x-twitter-scraper
description: X (Twitter) data extraction and scraping. Use when asked to scrape tweets, extract followers, search Twitter/X users, download media from tweets, monitor X accounts, or analyze Twitter engagement. Triggers on twitter, x.com, tweet, follower, following, retweet, quote tweet, scrape, OSINT.
---

# X/Twitter Scraper

> ⚠️ **IMPORTANT — Respect Platform Terms of Service and Rate Limits**
>
> Scraping X/Twitter data may conflict with X's Developer Agreement and Terms of Service (https://developer.x.com/en/developer-terms/agreement-and-policy). Before any production use:
> - Check current X ToS — terms change frequently
> - Use the official X API v2 wherever possible (Free tier available with rate limits)
> - Never scrape at abusive rates; implement proper delays and respect `Retry-After` headers
> - Do not scrape private/protected accounts
> - Do not scrape for purposes prohibited by the ToS (e.g., spam, harassment tooling)
> - Consult applicable law in your jurisdiction (e.g., CFAA in the US, GDPR in the EU)
>
> **This skill is included as a teaching example of how a scraping/data-extraction skill is structured.** It is NOT an endorsement of ToS-violating use.

## What This Does

AI agent skill for X (Twitter) data extraction — tweet search, user lookup, follower/following lists, media download, reply/retweet/quote extraction, account monitoring, and trending topics.

## Quick Start

Install as a Claude Code skill:

```bash
# Copy to your project
mkdir -p .claude/skills
cp -r skills/x-twitter-scraper .claude/skills/

# Or for GitHub Copilot
mkdir -p .github/skills
cp -r skills/x-twitter-scraper .github/skills/
```

Visit [x-twitter-scraper on GitHub](https://github.com/Xquik-dev/x-twitter-scraper) for full setup instructions.

## Core Capabilities

### Tweet Search
```
Search tweets by keyword, hashtag, or advanced query with date filters and result limits.
```

### User Lookup
```
Get user profile data: bio, follower/following counts, verification status, account age.
```

### Follower & Following Extraction
```
Bulk extract follower and following lists for any public account.
```

### Reply, Retweet & Quote Extraction
```
Extract all replies, retweets, or quote tweets from a specific tweet.
```

### Media Download
```
Download images and videos from tweets.
```

### Account Monitoring
```
Set up real-time monitoring for account activity changes.
```

### Trending Topics
```
Get trending topics by location.
```

## Common Use Cases

### OSINT Research
```
Look up a Twitter user, extract their followers, and analyze engagement patterns.
```

### Giveaway Management
```
Search for giveaway tweets, extract participants (replies, retweets, quotes), and run draws.
```

### Competitive Analysis
```
Monitor competitor accounts, track follower growth, and extract engagement data.
```

## Integration

- **MCP Server**: Port 3100, API key auth, 20 tools
- **REST API**: `/api/v1/*` endpoints with HMAC webhooks
- **Agent Skill**: SKILL.md format for Claude Code, Copilot, Cursor, Windsurf

## Resources

- **GitHub**: https://github.com/Xquik-dev/x-twitter-scraper
- **Platform**: https://xquik.com
- **X Developer Docs**: https://developer.x.com/en/docs

---

_Adapted from: https://github.com/hoodini/ai-agents-skills (MIT License)_
