# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## System Overview

This is a Blueprint Prompting System that enables Gemini AI to execute actions through GitHub Pages, triggering Make.com webhooks and returning actual response data. The system uses static HTML files deployed to GitHub Pages as an interface layer between Gemini and Make.com workflows.

## Architecture

```
User → Gemini → GitHub Pages (execute.html) → Make.com Webhook → Response → Gemini → User
```

## Development Commands

### Testing the System
```bash
# Test the execute.html endpoint directly
curl "https://smeltsermake.github.io/gemini-blueprint-pages/execute.html?tool=test&json=%7B%22message%22%3A%22hello%22%7D"

# Deploy changes to GitHub Pages (auto-deploys on push to main)
git push origin main
```

### File Structure
- `index.html` - System instructions page that Gemini reads for configuration
- `execute.html` - **Primary execution endpoint** - handles tool execution via Make.com webhook
- `PROJECT.md` - Current project status and implementation details
- Legacy files: `make.html`, `test.html`, `slack.html` (historical, not actively used)

## System Architecture

### Core Data Flow
1. User requests action via `/test` command
2. Gemini converts request to JSON, URL-encodes it
3. Gemini browses to `execute.html?tool=test&json=[ENCODED_JSON]`  
4. JavaScript in execute.html makes GET request to Make.com webhook
5. Make.com processes request and returns response
6. Response is displayed in `message-for-gemini` code block
7. Gemini extracts and returns response to user

### Key Integration Points
- **GitHub Pages**: https://smeltsermake.github.io/gemini-blueprint-pages/
- **Make.com Webhook**: https://hook.us1.make.com/3nzwopn5fq6ql6fulyv293728c2bxwde
- **Response Format**: JSON wrapped in `message-for-gemini` code blocks

## Implementation Notes

### execute.html Implementation Details
The core execution file (`execute.html`) uses JavaScript to:
- Extract URL parameters containing tool and JSON data
- Make asynchronous fetch requests to Make.com webhook
- Handle CORS issues with image beacon fallback
- Display responses in Gemini-parseable format (`message-for-gemini` blocks)
- Show loading states while waiting for webhook responses

### Error Handling Strategy
- **Network/CORS failures**: Falls back to image beacon requests
- **Response formatting**: Attempts JSON parsing, falls back to raw text
- **Loading states**: Shows spinner until webhook responds
- **HTTP errors**: Displays error status and response text

### URL Encoding Requirements  
All JSON payloads must be URL-encoded before passing to execute.html. Complex nested objects and special characters require proper encoding to ensure data integrity through the URL parameter system.

## Current Status
✅ **OPERATIONAL** - System is fully functional and deployed
- GitHub Pages hosting active
- Make.com webhook responding  
- Response extraction working for Gemini
- Error handling and fallback mechanisms in place