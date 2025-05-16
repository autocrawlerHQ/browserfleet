# Browsergrid

> Browser automation infrastructure made simple

![image](https://github.com/user-attachments/assets/3409f88c-46db-4769-aed1-2502518b17fa)


Browsergrid provides ready-to-use browser instances for test automation, web scraping, and development workflows. Stop struggling with browser setup and focus on what matters - your code.

## Why Browsergrid?

- **Eliminate Browser Headaches**: No more fighting with browser drivers, dependencies, or version conflicts
- **Develop Faster**: Instantly provision browser environments that match your production needs
- **Reduce CI Costs**: Save compute resources with efficient browser multiplexing
- **Scale Seamlessly**: From local development to production deployment with the same API

## Quick Start

```bash
# Install the client
pip install browsergrid

# Start the server (uses Docker under the hood)
browsergrid server start

# Launch a Chrome session
browsergrid session create --browser chrome
```

Boom! Now you have a fully configured Chrome instance ready for automation.

## Key Features

- **Multi-Browser Support**: Chrome, Firefox, Chromium, and WebKit
- **Browser Multiplexing**: Connect multiple clients to one browser instance
- **Dynamic Scaling**: Add/remove browser capacity on demand
- **Webhook Triggers**: Automate workflows based on browser events
- **Full CDP Support**: Works with Puppeteer, Playwright, and custom CDP tools

## Real-World Examples

### Web Testing with Playwright

```python
from playwright.sync_api import sync_playwright
from browsergrid.client import BrowsergridClient

# Start a browser session
client = BrowsergridClient()
session = client.create_session(browser="chrome")

# Connect Playwright to the running browser
with sync_playwright() as p:
    browser = p.chromium.connect_over_cdp(session.endpoint_url)
    page = browser.new_page()
    page.goto("https://example.com")
    page.screenshot(path="screenshot.png")
    browser.close()
```

### Web Scraping

```python
from browsergrid.client import BrowsergridClient
import requests

# Start a browser with proxy support
client = BrowsergridClient()
session = client.create_session(
    browser="chrome",
    proxy="http://myproxy:8080",
    headless=True
)

# Use the browser's CDP endpoint for scraping
response = requests.post(
    f"{session.endpoint_url}/json/new",
    json={"url": "https://example.com"}
)
tab_info = response.json()
# Continue with CDP commands...
```

## Installation

### Prerequisites

- Docker (for local server)
- Python 3.8+

### Install the Client

```bash
pip install browsergrid
```

### Server Deployment Options

#### Local Development (Easiest)

```bash
# Start server with default settings
browsergrid server start

# Or with custom configuration
browsergrid server start --port 9000 --workers 5
```

#### Docker Compose

```yaml
# docker-compose.yml
version: '3'
services:
  browsergrid-server:
    image: browsergrid/server:latest
    ports:
      - "8765:8765"
    environment:
      - API_KEY=your_secret_key
      
  chrome-worker:
    image: browsergrid/worker:latest
    depends_on:
      - browsergrid-server
    environment:
      - BROWSER=chrome
      - API_KEY=your_secret_key
      - SERVER_URL=http://browsergrid-server:8765
```

#### Cloud Deployment

See our [Cloud Deployment Guide](https://docs.browsergrid.com/deployment/cloud) for step-by-step instructions for AWS, GCP, and Azure.

## Common Scenarios

### Managing Browser Sessions

```bash
# List all active sessions
browsergrid session list

# Get details for a specific session
browsergrid session info SESSION_ID

# Terminate a session
browsergrid session terminate SESSION_ID
```

### Using Work Pools for Scaling

```bash
# Create a work pool for Chrome sessions
browsergrid pool create --name "chrome-pool" --browser chrome

# Add workers to the pool
browsergrid worker add --pool "chrome-pool" --count 3

# Monitor pool status
browsergrid pool status "chrome-pool"
```

## Troubleshooting

### Common Issues

**Connection Refused**
```
Error: Could not connect to browsergrid server
```
→ Ensure the server is running: `browsergrid server status`

**Session Creation Fails**
```
Error: Failed to create session
```
→ Check for available workers: `browsergrid worker list`
→ Verify Docker is running if using local deployment

**Performance Issues**
```
Browser response is slow
```
→ Check resource utilization: `browsergrid status --resources`
→ Consider increasing worker resources: `browsergrid worker update --memory 4G`

## Need Help?

- [Documentation](https://docs.browsergrid.com)
- [GitHub Issues](https://github.com/galad-ca/browsergrid/issues)
- [Community Discord](https://discord.gg/browsergrid)

## Contributing

We welcome contributions! See our [Contributing Guide](./.github/CONTRIBUTING.md) to get started.

## License

Browsergrid is MIT licensed. See [LICENSE](./LICENSE) for details.
