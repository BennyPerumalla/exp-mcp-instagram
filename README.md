# Instagram Automation Service - Installation Guide

This guide will walk you through the process of installing and setting up the Instagram Automation Service with all its dependencies.

## Prerequisites

- Python 3.8 or higher
- pip (Python package manager)
- Git (optional, for cloning the repository)
- System administrator privileges (for systemd service installation)

## Installation Steps

### 1. Clone or Download the Repository

```bash
git clone https://github.com/yourusername/instagram-automation.git
cd instagram-automation
```

Or download and extract the ZIP file from the repository.

### 2. Create a Virtual Environment (Recommended)

```bash
python -m venv venv
```

Activate the virtual environment:

**On Windows:**
```bash
venv\Scripts\activate
```

**On macOS/Linux:**
```bash
source venv/bin/activate
```

### 3. Install Dependencies

#### Option 1: Using pip with requirements.txt

```bash
pip install -r requirements.txt
```

#### Option 2: Using setup.py

```bash
pip install -e .
```

### 4. Platform-Specific Setup for Contacts Integration

#### macOS

```bash
pip install contacts-mac
```

#### Windows

```bash
pip install contacts-win
```

#### Linux

```bash
# Install system dependencies
sudo apt-get install evolution-data-server-dev

# Install Python package
pip install contacts-linux
```

### 5. Configuration

1. Copy the example configuration file:

```bash
cp config_example.py config.py
```

2. Edit the configuration file with your Instagram credentials:

```bash
nano config.py  # or use any text editor
```

Update the following fields:
```python
CONFIG = {
    "instagram": {
        "username": "your_instagram_username",
        "password": "your_instagram_password",
        # ... other settings
    },
    # ... rest of config
}
```

### 6. System Integration (Optional)

#### Using systemd (Linux)

1. Edit the systemd service files to match your installation path:

```bash
nano systemd_service/instagram-automation.service
```

2. Update the `ExecStart` line with your actual Python path and script location.

3. Install the systemd services:

```bash
sudo cp systemd_service/instagram-automation.service /etc/systemd/system/
sudo cp systemd_service/instagram-scheduler.timer /etc/systemd/system/
sudo cp systemd_service/instagram-scheduler.service /etc/systemd/system/
sudo systemctl daemon-reload
```

4. Enable and start the services:

```bash
sudo systemctl enable instagram-automation.service
sudo systemctl start instagram-automation.service
sudo systemctl enable instagram-scheduler.timer
sudo systemctl start instagram-scheduler.timer
```

#### Using cron (Alternative for all platforms)

Add a cron job to run the scheduler:

```bash
crontab -e
```

Add the following line to run the scheduler every 15 minutes:

```
*/15 * * * * /path/to/venv/bin/python /path/to/instagram_automation/scheduler.py
```

### 7. Running the Service Manually

To run the service without system integration:

```bash
python -m instagram_automation.main --username your_instagram_username --password your_instagram_password
```

## Verification

1. Check if the service is running:

```bash
# If using systemd
systemctl status instagram-automation.service

# If running manually, check for the process
ps aux | grep instagram_automation
```

2. Test the API with a simple request:

```bash
curl -X POST -H "Content-Type: application/json" -d '{"jsonrpc":"2.0","method":"get_liked_reels","params":{"limit":5},"id":1}' http://localhost:8000/jsonrpc
```

## Troubleshooting

### Common Issues

1. **Authentication Errors**
   - Check your Instagram credentials
   - Instagram may require additional verification
   - Try logging in manually first on a browser

2. **Rate Limiting**
   - Adjust the delay parameters in the configuration
   - Reduce the number of messages sent per hour

3. **Missing Dependencies**
   - Run `pip install -r requirements.txt` again
   - Check for platform-specific requirements

4. **Service Not Starting**
   - Check the logs: `journalctl -u instagram-automation.service`
   - Verify paths in the service file

### Logs

Check the service logs for detailed error information:

```bash
# If using systemd
journalctl -u instagram-automation.service -f

# Otherwise check the log file
cat instagram_automation.log
```

## Security Considerations

- Store your Instagram credentials securely
- Consider using environment variables for sensitive information
- Use a dedicated Instagram account for automation

## Updates

To update the service to the latest version:

```bash
git pull
pip install -r requirements.txt
systemctl restart instagram-automation.service  # if using systemd
```

## Support

For issues or questions, please open an issue on the GitHub repository or contact the maintainer.
