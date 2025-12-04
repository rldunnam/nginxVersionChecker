# NGINX Version Checker

Automatically monitor the NGINX download page for new stable releases and send instant notifications via Email, Slack, and/or Microsoft Teams when a new version is detected.

## Features

- 🔔 **Multi-Channel Notifications** - Email, Slack, and Microsoft Teams support
- 🔄 **Automatic Version Tracking** - Remembers last version and only alerts on changes
- 🛡️ **Retry Logic** - Automatic retry with exponential backoff for network failures
- 🧪 **Dry Run Mode** - Test configuration without sending notifications
- ⚙️ **Flexible Configuration** - Environment variables, .env file, or command-line arguments
- 📊 **Detailed Logging** - Comprehensive logging with verbose mode for troubleshooting
- 🚀 **Zero Dependencies** - Only requires Python 3.7+ and the `requests` library

## Table of Contents

- [Prerequisites](#prerequisites)
- [Installation](#installation)
- [Configuration](#configuration)
- [Usage](#usage)
- [Notification Setup](#notification-setup)
- [Scheduling](#scheduling)
- [Troubleshooting](#troubleshooting)
- [Examples](#examples)

## Prerequisites

- Python 3.7 or higher
- `requests` library
- (Optional) `python-dotenv` for .env file support

### Install Dependencies

```bash
pip install requests

# Optional: For .env file support
pip install python-dotenv
```

## Installation

1. **Download the script:**
   ```bash
   curl -O https://your-repo/nginxVersionChecker.py
   chmod +x nginxVersionChecker.py
   ```

2. **Create configuration file:**
   ```bash
   cp .env.example .env
   # Edit .env with your settings
   ```

3. **Secure the configuration file:**
   ```bash
   chmod 600 .env
   ```

4. **Test the setup:**
   ```bash
   ./nginxVersionChecker.py --dry-run --enable-email --enable-slack --enable-teams
   ```

## Configuration

### Configuration Priority

The script uses configuration in this priority order (highest to lowest):

1. **Command-line arguments** (e.g., `--enable-email`)
2. **Environment variables** (e.g., `export ENABLE_EMAIL=true`)
3. **.env file** (loads automatically if present)
4. **Default values**

### Quick Start Configurations

#### Email Only
```bash
# .env file
ENABLE_EMAIL=true
EMAIL_USERNAME=monitor@example.com
EMAIL_PASSWORD=your_password
EMAIL_FROM=monitor@example.com
EMAIL_TO=team@example.com
```

#### Slack Only
```bash
# .env file
ENABLE_SLACK=true
SLACK_WEBHOOK_URL=https://hooks.slack.com/services/YOUR/WEBHOOK/URL
```

#### Microsoft Teams Only
```bash
# .env file
ENABLE_TEAMS=true
TEAMS_WEBHOOK_URL=https://your-webhook-url
```

#### All Channels
```bash
# .env file
ENABLE_EMAIL=true
ENABLE_SLACK=true
ENABLE_TEAMS=true

# Email settings
EMAIL_USERNAME=monitor@example.com
EMAIL_PASSWORD=your_password
EMAIL_FROM=monitor@example.com
EMAIL_TO=team@example.com

# Slack settings
SLACK_WEBHOOK_URL=https://hooks.slack.com/services/YOUR/WEBHOOK/URL

# Teams settings
TEAMS_WEBHOOK_URL=https://your-webhook-url
```

### Configuration Parameters

| Parameter | Required | Default | Description |
|-----------|----------|---------|-------------|
| `ENABLE_EMAIL` | No | `false` | Enable email notifications |
| `ENABLE_SLACK` | No | `false` | Enable Slack notifications |
| `ENABLE_TEAMS` | No | `false` | Enable Microsoft Teams notifications |
| `SMTP_SERVER` | If email enabled | `smtp.office365.com` | SMTP server hostname |
| `SMTP_PORT` | If email enabled | `587` | SMTP server port |
| `EMAIL_USERNAME` | If email enabled | - | Email account username |
| `EMAIL_PASSWORD` | If email enabled | - | Email account password |
| `EMAIL_FROM` | If email enabled | - | From email address |
| `EMAIL_TO` | If email enabled | - | To email address(es) |
| `SLACK_WEBHOOK_URL` | If Slack enabled | - | Slack incoming webhook URL |
| `TEAMS_WEBHOOK_URL` | If Teams enabled | - | Teams webhook or Power Automate URL |
| `VERSION_FILE` | No | `nginx_last_version.txt` | File to store version history |
| `DRY_RUN` | No | `false` | Test mode without sending notifications |

## Usage

### Command-Line Options

```
Usage: nginxVersionChecker.py [OPTIONS]

Notification Control:
  --enable-email          Enable email notifications
  --enable-slack          Enable Slack notifications
  --enable-teams          Enable Microsoft Teams notifications
  --disable-email         Explicitly disable email
  --disable-slack         Explicitly disable Slack
  --disable-teams         Explicitly disable Teams

Email Configuration:
  --smtp-server HOST      SMTP server hostname
  --smtp-port PORT        SMTP server port
  --email-username USER   Email account username
  --email-password PASS   Email account password
  --email-from ADDR       From email address
  --email-to ADDR         To email address

Slack Configuration:
  --slack-webhook URL     Slack webhook URL

Teams Configuration:
  --teams-webhook URL     Teams webhook URL

General Options:
  --version-file PATH     Version tracking file path
  --dry-run               Test without sending notifications
  --verbose               Enable verbose logging
  --help                  Show help message
```

### Basic Usage Examples

```bash
# Enable email notifications (using .env for credentials)
./nginxVersionChecker.py --enable-email

# Enable all notification channels
./nginxVersionChecker.py --enable-email --enable-slack --enable-teams

# Test configuration without sending notifications
./nginxVersionChecker.py --dry-run --enable-email --enable-teams

# Override email recipient via command line
./nginxVersionChecker.py --enable-email --email-to admin@example.com

# Verbose mode for troubleshooting
./nginxVersionChecker.py --enable-email --verbose
```

## Notification Setup

### Email Setup

#### Microsoft 365 / Outlook.com

1. Use your Microsoft 365 credentials
2. If using MFA, create an **App Password**:
   - Go to [Microsoft Account Security](https://account.microsoft.com/security)
   - Security → More security options → App passwords
   - Create app password for "NGINX Monitor"
   - Use this password in `EMAIL_PASSWORD`

**Configuration:**
```bash
SMTP_SERVER=smtp.office365.com
SMTP_PORT=587
EMAIL_USERNAME=your-email@example.com
EMAIL_PASSWORD=your-app-password
```

#### Gmail

1. Enable 2-Step Verification on your Google Account
2. Generate an **App Password**:
   - Go to [Google Account](https://myaccount.google.com/)
   - Security → 2-Step Verification → App passwords
   - Select "Mail" and "Other (Custom name)"
   - Enter "NGINX Monitor"
   - Copy the 16-character password

**Configuration:**
```bash
SMTP_SERVER=smtp.gmail.com
SMTP_PORT=587
EMAIL_USERNAME=your-email@gmail.com
EMAIL_PASSWORD=your-16-char-app-password
```

### Slack Setup

1. **Create Incoming Webhook:**
   - Go to [Slack API Apps](https://api.slack.com/apps)
   - Click "Create New App" → "From scratch"
   - Name it "NGINX Monitor" and select your workspace
   - Click "Incoming Webhooks" → Enable
   - Click "Add New Webhook to Workspace"
   - Select the channel for notifications
   - Copy the webhook URL

2. **Configure:**
   ```bash
   SLACK_WEBHOOK_URL=https://hooks.slack.com/services/T00000000/B00000000/XXXXXXXXXXXXXXXXXXXX
   ```

### Microsoft Teams Setup

You have **two options** for Teams integration:

#### Option 1: Power Automate Flow (Recommended)

This method gives you more control over message formatting and is more reliable.

1. **Create a new Flow:**
   - Go to [Power Automate](https://make.powerautomate.com/)
   - Create → Automated cloud flow
   - Skip the trigger selection

2. **Add HTTP trigger:**
   - Search for "When a HTTP request is received"
   - Add this trigger (don't configure yet)

3. **Add Parse JSON action:**
   - Add action → "Parse JSON"
   - Content: `triggerBody()`
   - Schema:
   ```json
   {
       "type": "object",
       "properties": {
           "title": {"type": "string"},
           "version": {"type": "string"},
           "status": {"type": "string"},
           "description": {"type": "string"},
           "url": {"type": "string"},
           "emoji": {"type": "string"}
       }
   }
   ```

4. **Add Teams action:**
   - Add action → "Post message in a chat or channel"
   - Post as: Flow bot
   - Post in: Channel
   - Team: [Select your team]
   - Channel: [Select your channel]
   - Message (use dynamic content):
   ```
   @{triggerBody()?['emoji']} **@{triggerBody()?['title']}**
   
   **Version:** @{triggerBody()?['version']}
   **Status:** @{triggerBody()?['status']}
   
   @{triggerBody()?['description']}
   
   [View Download Page](@{triggerBody()?['url']})
   ```

5. **Save and get URL:**
   - Save the flow
   - Open the HTTP trigger
   - Copy the "HTTP POST URL"
   - Use this as your `TEAMS_WEBHOOK_URL`

#### Option 2: Traditional Incoming Webhook

1. **Add Incoming Webhook Connector:**
   - Go to your Teams channel
   - Click "..." → "Connectors"
   - Search for "Incoming Webhook"
   - Click "Configure"
   - Name it "NGINX Monitor"
   - (Optional) Upload an icon
   - Click "Create"
   - Copy the webhook URL

2. **Configure:**
   ```bash
   TEAMS_WEBHOOK_URL=https://your-org.webhook.office.com/webhookb2/...
   ```

**Note:** The script automatically detects which method you're using and sends the appropriate payload format.

## Scheduling

### Cron (Linux/macOS)

Check for updates every 6 hours:

```bash
# Edit crontab
crontab -e

# Add this line (runs at 00:00, 06:00, 12:00, 18:00)
0 */6 * * * cd /path/to/script && /usr/bin/python3 nginxVersionChecker.py >> /var/log/nginx-checker.log 2>&1
```

Check daily at 9 AM:
```bash
0 9 * * * cd /path/to/script && /usr/bin/python3 nginxVersionChecker.py
```

### Systemd Timer (Linux)

**Create `/etc/systemd/system/nginx-checker.service`:**
```ini
[Unit]
Description=NGINX Version Checker
After=network-online.target

[Service]
Type=oneshot
User=nginx-checker
WorkingDirectory=/opt/nginx-checker
EnvironmentFile=/opt/nginx-checker/.env
ExecStart=/usr/bin/python3 /opt/nginx-checker/nginxVersionChecker.py
StandardOutput=journal
StandardError=journal

[Install]
WantedBy=multi-user.target
```

**Create `/etc/systemd/system/nginx-checker.timer`:**
```ini
[Unit]
Description=Run NGINX Version Checker every 6 hours

[Timer]
OnBootSec=10min
OnUnitActiveSec=6h
Persistent=true

[Install]
WantedBy=timers.target
```

**Enable and start:**
```bash
sudo systemctl daemon-reload
sudo systemctl enable nginx-checker.timer
sudo systemctl start nginx-checker.timer

# Check status
sudo systemctl status nginx-checker.timer
sudo systemctl list-timers
```

### Windows Task Scheduler

1. Open Task Scheduler
2. Create Task → "NGINX Version Checker"
3. **Triggers:**
   - Daily at 9:00 AM
   - Repeat every 6 hours
4. **Actions:**
   - Program: `C:\Python39\python.exe`
   - Arguments: `C:\Scripts\nginxVersionChecker.py`
   - Start in: `C:\Scripts\`
5. **Conditions:**
   - Start only if network available
6. **Settings:**
   - Allow task to run on demand
   - Run as soon as possible after missed

### Docker

**Dockerfile:**
```dockerfile
FROM python:3.11-slim

WORKDIR /app

COPY nginxVersionChecker.py .
RUN pip install --no-cache-dir requests

# Use cron to schedule
RUN apt-get update && apt-get install -y cron
COPY crontab /etc/cron.d/nginx-checker
RUN chmod 0644 /etc/cron.d/nginx-checker
RUN crontab /etc/cron.d/nginx-checker

CMD ["cron", "-f"]
```

**crontab file:**
```
0 */6 * * * cd /app && python nginxVersionChecker.py >> /var/log/cron.log 2>&1
```

**docker-compose.yml:**
```yaml
version: '3.8'
services:
  nginx-checker:
    build: .
    environment:
      - ENABLE_EMAIL=true
      - ENABLE_TEAMS=true
      - EMAIL_USERNAME=${EMAIL_USERNAME}
      - EMAIL_PASSWORD=${EMAIL_PASSWORD}
      - EMAIL_FROM=${EMAIL_FROM}
      - EMAIL_TO=${EMAIL_TO}
      - TEAMS_WEBHOOK_URL=${TEAMS_WEBHOOK_URL}
    volumes:
      - ./data:/app/data
    restart: unless-stopped
```

### GitHub Actions

**`.github/workflows/nginx-check.yml`:**
```yaml
name: Check NGINX Version

on:
  schedule:
    - cron: '0 */6 * * *'  # Every 6 hours
  workflow_dispatch:  # Manual trigger

jobs:
  check:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      - name: Set up Python
        uses: actions/setup-python@v4
        with:
          python-version: '3.11'
      
      - name: Install dependencies
        run: pip install requests
      
      - name: Check NGINX version
        env:
          ENABLE_TEAMS: true
          TEAMS_WEBHOOK_URL: ${{ secrets.TEAMS_WEBHOOK_URL }}
        run: python nginxVersionChecker.py
      
      - name: Upload version file
        uses: actions/upload-artifact@v3
        with:
          name: version-history
          path: nginx_last_version.txt
```

## Troubleshooting

### Common Issues

#### 1. Authentication Failed (Email)

**Error:** `Email authentication failed - check EMAIL_USERNAME and EMAIL_PASSWORD`

**Solutions:**
- Verify credentials are correct
- For Microsoft 365/Gmail with MFA: Use an **App Password** instead of regular password
- Check if account requires "Less secure app access" to be enabled
- Verify `SMTP_SERVER` and `SMTP_PORT` are correct for your provider

#### 2. Slack Webhook Invalid

**Error:** `SLACK_WEBHOOK_URL appears to be invalid`

**Solutions:**
- Ensure URL starts with `https://hooks.slack.com/`
- Regenerate webhook if it was deleted or expired
- Test webhook manually:
  ```bash
  curl -X POST -H 'Content-type: application/json' \
    --data '{"text":"Test message"}' \
    YOUR_WEBHOOK_URL
  ```

#### 3. Teams Webhook Validation Failed

**Error:** `TEAMS_WEBHOOK_URL appears to be invalid`

**Solutions:**
- Ensure URL starts with `https://`
- For Power Automate: URL should contain `powerplatform.com` or `logic.azure.com`
- For traditional webhook: URL should contain `webhook.office.com`
- Verify webhook wasn't deleted or expired
- Test webhook manually:
  ```bash
  curl -X POST -H 'Content-Type: application/json' \
    -d '{"title":"Test","version":"1.0.0"}' \
    YOUR_WEBHOOK_URL
  ```

#### 4. SSL Certificate Errors

**Error:** `SSL: CERTIFICATE_VERIFY_FAILED`

**Solutions:**
- Update CA certificates: `pip install --upgrade certifi`
- Check system date/time is correct
- If behind corporate proxy, set environment variables:
  ```bash
  export HTTP_PROXY=http://proxy:port
  export HTTPS_PROXY=http://proxy:port
  ```

#### 5. Version Pattern Not Found

**Error:** `Stable version pattern not found in page content`

**Solutions:**
- NGINX website structure may have changed
- Check if you can access nginx.org directly
- Update the regex pattern in the script if needed
- Run with `--verbose` to see full page content

#### 6. No Notifications Sent

**Issue:** Script runs but no notifications are received

**Solutions:**
- Check if any notification channel is actually enabled:
  ```bash
  ./nginxVersionChecker.py --verbose
  ```
- Verify version has actually changed (delete `nginx_last_version.txt` to force notification)
- Check notification service logs/spam folders
- Run in dry-run mode to verify configuration:
  ```bash
  ./nginxVersionChecker.py --dry-run --enable-email --enable-teams
  ```

### Debug Mode

Enable verbose logging to see detailed information:

```bash
./nginxVersionChecker.py --verbose --enable-email
```

This will show:
- Configuration loaded
- HTTP requests being made
- Retry attempts
- Full error messages with stack traces
- Notification attempts

### Testing Configuration

Test without sending notifications:

```bash
# Test with dry-run
./nginxVersionChecker.py --dry-run --enable-email --enable-slack --enable-teams

# Force a notification by deleting version file
rm nginx_last_version.txt
./nginxVersionChecker.py --enable-teams
```

### Logs

Check systemd logs (if using systemd timer):
```bash
journalctl -u nginx-checker.service -f
```

Check cron logs:
```bash
# Most systems
grep CRON /var/log/syslog

# Some systems
tail -f /var/log/cron
```

## Examples

### Example 1: First-Time Setup with Email

```bash
# Create .env file
cat > .env << 'EOF'
ENABLE_EMAIL=true
SMTP_SERVER=smtp.gmail.com
SMTP_PORT=587
EMAIL_USERNAME=monitor@gmail.com
EMAIL_PASSWORD=your-app-password
EMAIL_FROM=monitor@gmail.com
EMAIL_TO=admin@example.com
EOF

# Secure the file
chmod 600 .env

# Test configuration
./nginxVersionChecker.py --dry-run

# Run for real
./nginxVersionChecker.py
```

### Example 2: Multiple Notification Channels

```bash
# .env file with all channels
cat > .env << 'EOF'
ENABLE_EMAIL=true
ENABLE_SLACK=true
ENABLE_TEAMS=true

EMAIL_USERNAME=monitor@example.com
EMAIL_PASSWORD=password123
EMAIL_FROM=monitor@example.com
EMAIL_TO=team@example.com

SLACK_WEBHOOK_URL=https://hooks.slack.com/services/YOUR/WEBHOOK/URL
TEAMS_WEBHOOK_URL=https://your-flow-url
EOF

./nginxVersionChecker.py
```

### Example 3: Override Single Value

```bash
# Use .env for most config, override one value
./nginxVersionChecker.py --email-to urgent-alerts@example.com
```

### Example 4: Command-Line Only (No .env)

```bash
./nginxVersionChecker.py \
  --enable-teams \
  --teams-webhook "https://your-webhook-url"
```

### Example 5: Monitoring Script

```bash
#!/bin/bash
# nginx-monitor.sh - Run with error handling and logging

LOG_FILE="/var/log/nginx-checker.log"
ERROR_FILE="/var/log/nginx-checker-error.log"

echo "[$(date)] Starting NGINX version check" >> "$LOG_FILE"

if /usr/local/bin/nginxVersionChecker.py >> "$LOG_FILE" 2>> "$ERROR_FILE"; then
    echo "[$(date)] Check completed successfully" >> "$LOG_FILE"
else
    EXIT_CODE=$?
    echo "[$(date)] Check failed with exit code $EXIT_CODE" >> "$ERROR_FILE"
    
    # Send alert (optional)
    if command -v mail &> /dev/null; then
        mail -s "NGINX Checker Failed" admin@example.com < "$ERROR_FILE"
    fi
fi

# Rotate logs (keep last 30 days)
find /var/log -name "nginx-checker*.log" -mtime +30 -delete
```

## Exit Codes

The script uses standard exit codes:

| Code | Meaning |
|------|---------|
| 0 | Success - No new version or notifications sent successfully |
| 1 | Error - Configuration error, network failure, or notification failure |

Use these in automation:

```bash
if ./nginxVersionChecker.py; then
    echo "Check completed successfully"
else
    echo "Check failed"
    exit 1
fi
```

## Security Best Practices

1. **Protect credentials:**
   ```bash
   chmod 600 .env
   chown nginx-checker:nginx-checker .env
   ```

2. **Never commit .env to version control:**
   ```bash
   echo ".env" >> .gitignore
   echo "nginx_last_version.txt" >> .gitignore
   ```

3. **Use app-specific passwords** for email accounts with MFA

4. **Rotate credentials regularly** (every 90 days recommended)

5. **Use dedicated service accounts** with minimal permissions

6. **For production:**
   - Use secret management systems (AWS Secrets Manager, Azure Key Vault, etc.)
   - Run as non-privileged user
   - Implement log rotation
   - Monitor script execution
   - Set up alerting for script failures

## Contributing

Contributions are welcome! Please:
1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Test thoroughly
5. Submit a pull request

## License

[Your License Here]

## Support

- **Issues:** [GitHub Issues](https://github.com/your-org/nginx-checker/issues)
- **Documentation:** [Wiki](https://github.com/your-org/nginx-checker/wiki)
- **Email:** support@your-org.com

## Changelog

### Version 2.0.0
- ✨ Added Microsoft Teams support (Power Automate + Traditional webhooks)
- ✨ Multi-channel notification support
- 🔄 Improved retry logic with exponential backoff
- 📝 Better error messages and validation
- 🧪 Dry-run mode for testing
- 📊 Verbose logging mode

### Version 1.0.0
- Initial release
- Email and Slack support
- Basic version tracking

---

**Last Updated:** December 2025