This document describes the setup of a containerized email server using MailHog on the corporate jumpbox server.
## Overview
MailHog is used to simulate enterprise email delivery for:
- Phishing attack scenarios
- Email exfiltration testing
    
- Debugging scripts that send emails
    
- Observing SMTP traffic inside Wazuh/SIEM
    

It runs on Docker inside the corporate jumpbox:

`project-x-corp-svr` (Ubuntu Desktop 22.04).

MailHog provides:

- SMTP server on port 1025
    
- Web inbox on port 8025
    
- REST API for automation
    

## Setup MailHog Using Docker Compose

On `project-x-corp-svr`:

1. Create directory:
    
    ```
    cd /home
    sudo mkdir mailhog && cd mailhog
    ```
    
1. Create docker-compose. Yml:
    
    ```
    sudo nano docker-compose.yml
    ```
    
2. Add:
    
    ```
    version: "3"
    services:
      mailhog:
        image: mailhog/mailhog
        container_name: mailhog
        ports:
          - "1025:1025"
          - "8025:8025"
    ```
    
3. Start service:
    
    ```
    sudo docker compose up -d
    ```
    
4. Access MailHog UI:
    
    ```
    http://localhost:8025
    ```
    
    Or
    
    ```
    http://10.0.0.8:8025
    ```
    

## Sending Test Email via Python

Create script:

```
sudo nano test_message.py
```

Paste:

```
import smtplib
from email.message import EmailMessage

msg = EmailMessage()
msg.set_content("This is a test email from Ubuntu VM.")
msg["Subject"] = "Hello World from MailHog!"
msg["From"] = "corpserver@example.com"
msg["To"] = "user@example.com"

with smtplib.SMTP("localhost", 1025) as server:
    server.send_message(msg)
```

Run:

```
sudo chmod +x test_message.py
sudo python3 test_message.py
```

Email appears in MailHog UI.

## Email Poller Script (Linux Workstation)

Used on `project-x-linux-client` to simulate real-time inbox checking.

1. Install dependencies:
    
    ```
    sudo apt install curl jq
    ```
    
2. Create poller:
    
    ```
    sudo nano email_poller.sh
    ```
    
3. Paste:
    

```
#!/bin/bash

MAILHOG_IP="10.0.0.8"
TO_EMAIL="janed"
POLL_INTERVAL=30

SEEN_IDS_FILE="/tmp/mailhog_seen_ids_janed.txt"
touch "$SEEN_IDS_FILE"

while true; do
  curl -s http://$MAILHOG_IP:8025/api/v2/messages |
  jq -c '.items[]' | while read -r msg; do
    TO=$(echo "$msg" | jq -r '.To[].Mailbox')
    ID=$(echo "$msg" | jq -r '.ID')

    if [[ "$TO" == "$TO_EMAIL" && ! $(grep -Fx "$ID" "$SEEN_IDS_FILE") ]]; then
      SUBJECT=$(echo "$msg" | jq -r '.Content.Headers.Subject[0]')
      BODY=$(echo "$msg" | jq -r '.Content.Body')

      echo -e "\nNew Email:"
      echo "Subject: $SUBJECT"
      echo "From: $(echo "$msg" | jq -r '.Content.Headers.From[0]')"
      echo -e "Message:\n$BODY"
      echo "-----------------------------------"

      echo "$ID" >> "$SEEN_IDS_FILE"
    fi
  done

  sleep "$POLL_INTERVAL"
done
```

4. Make executable and run:
    
    ```
    chmod +x email_poller.sh
    sudo ./email_poller.sh &
    ```
    

Stop:

```
pkill -f email_poller
```

The email server is now fully operational and ready for phishing simulation, attack detection, and SIEM correlation in later modules.

---

If you want, I can:

- Rewrite these into more high-level summaries
    
- Produce README-ready versions
    
- Split each into separate folders in a GitHub structure
    
- Generate architecture diagrams for each component
    

Just tell me what style you want your GitHub repo to follow.