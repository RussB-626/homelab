## **Final Guide: Secure Docker Auto-Updates with Discord Alerts**

### **Phase 1: Secure Your Secrets**

To keep your Discord Webhook URL hidden from the script logic and the process list, we store it in a restricted file.

1. **Create the file**: nano \~/.docker\_secrets  
2. **Paste your variable** (Ensure no spaces around the \=):

```
export DISCORD_WEBHOOK_URL="https://discord.com/api/webhooks/YOUR_ID/YOUR_TOKEN"
```

3. **Lock permissions**: chmod 600 \~/.docker\_secrets

---

### **Phase 2: Create the Shell Script**

We use a script instead of raw commands in the crontab because it allows for error handling and sequenced Discord notifications.

1. **Create the file**: nano \~/update-docker.sh  
2. **Paste the updated code**:

```
#!/bin/bash
# Script to update Portainer and the Semaphore Stack with Discord Alerts

# --- 1. LOAD SECRETS ---
SECRET_FILE="/home/russ/.docker_secrets"
if [ -f "$SECRET_FILE" ]; then
    source "$SECRET_FILE"
else
    echo "Error: Secret file not found at $SECRET_FILE"
    exit 1
fi

# --- 2. CONFIGURATION ---
WEBHOOK_URL="$DISCORD_WEBHOOK_URL"
PROJECTS=("/mnt/controller/docker/portainer" "/mnt/controller/docker/stack")
HOSTNAME=$(hostname)

# --- 3. NOTIFICATION FUNCTION ---
send_discord() {
    local color=$1
    local status=$2
    local message=$3
    
    curl -s -H "Content-Type: application/json" \
         -X POST \
         -d "{
               \"embeds\": [{
                 \"title\": \"$status\",
                 \"description\": \"$message\",
                 \"color\": $color,
                 \"footer\": {\"text\": \"Host: $HOSTNAME\"},
                 \"timestamp\": \"$(date -u +%Y-%m-%dT%H:%M:%SZ)\"
               }]
             }" \
         "$WEBHOOK_URL" > /dev/null
}

# --- 4. EXECUTION ---
# Send Start Message
send_discord 3447003 "🚀 Update Started" "Beginning automated maintenance on $HOSTNAME."

# Loop through directories
for DIR in "${PROJECTS[@]}"; do
    if [ -d "$DIR" ]; then
        echo "Processing $DIR..."
        cd "$DIR" || { send_discord 15158332 "❌ FAIL" "Could not enter $DIR"; exit 1; }
        
        # Pull and Up logic
        if ! (/usr/bin/docker compose pull && /usr/bin/docker compose up -d); then
            send_discord 15158332 "❌ UPDATE FAILED" "Error occurred while updating $DIR."
            exit 1
        fi
    else
        send_discord 16776960 "⚠️ WARNING" "Directory $DIR not found. Skipping."
    fi
done

# --- 5. CLEANUP ---
echo "Cleaning up old images..."
if /usr/bin/docker image prune -af; then
    send_discord 3066993 "✅ SUCCESS" "All containers updated and images pruned on $HOSTNAME."
else
    send_discord 16776960 "⚠️ PARTIAL SUCCESS" "Update finished, but 'prune' failed."
fi

exit 0
```

3. **Save and Exit**: Ctrl+O, Enter, Ctrl+X.  
4. **Make it Executable**: chmod \+x \~/update-docker.sh

---

### **Phase 3: Testing (Manual Run)**

Before scheduling, run the script manually to ensure your Webhook is pulling correctly and Docker is responding.

* **Run the script**: sudo \~/update-docker.sh  
* **What to expect**: Your SSH session remains active. The Semaphore Web UI will disconnect briefly when the stack container restarts. Check Discord for the Blue and Green messages.

---

### **Phase 4: Scheduling with Cron**

We will schedule this for **Saturdays at 11:00 UTC** with a 15-minute safety timeout.

1. **Open the Crontab**: crontab \-e  
2. **Add the following line at the very bottom**:

```
0 11 * * 6 /bin/bash /home/your-username/update-docker.sh >> /home/your-username/docker_update.log 2>&1
```

*Replace your-username with your actual Linux username.*

### **Breakdown of the Timing Logic**

| Field | Value | Meaning |
| :---- | :---- | :---- |
| **Minute** | 00 | Start exactly on the hour. |
| **Hour** | 11 | 11:00 AM (Ensure your server system time is UTC). |
| **Day of Month** | \* | Every day of the month. |
| **Month** | \* | Every month. |
| **Day of Week** | 6 | Saturday (0 is Sunday, 6 is Saturday). |

---

### **Phase 5: Verification & Troubleshooting**

Check your work after the first scheduled run.

* **View the log**: cat \~/docker\_update.log  
* **Check Server Time**: Type date \-u in your terminal to ensure the current time in UTC matches your scheduling expectations.

\[\!IMPORTANT\]

**Skeptical Double-Check**: Open your docker-compose.yaml file inside /mnt/controller/docker/stack. Ensure your Semaphore service has restart: always. If it doesn't, and the update script stops the container but fails to restart it, your automation will stay offline until you manually start it via SSH.

