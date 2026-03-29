## **Guide: Setting Up the Docker Auto-Update Cron Job**

### **Phase 1: Create the Shell Script**

We use a script instead of putting the command directly in the cron table because it’s easier to debug and allows for multiple commands (pull, up, prune) to run in sequence.

1. **Open a terminal** via SSH to 172.16.10.2.  
2. **Create the file**:

```
nano ~/update-docker.sh
```

   

3. **Paste this code**:

```
#!/bin/bash
# Script to update Portainer and the Semaphore Stack

# List your folder paths here
PROJECTS=("/mnt/controller/docker/portainer" "/mnt/controller/docker/stack")

echo "--- Update Started: $(date) ---"

for DIR in "${PROJECTS[@]}"; do
    if [ -d "$DIR" ]; then
        echo "Processing $DIR..."
        cd "$DIR" || continue
        /usr/bin/docker compose pull
        /usr/bin/docker compose up -d
    else
        echo "Error: Directory $DIR not found."
    fi
done

echo "Cleaning up old images..."
/usr/bin/docker image prune -af
echo "--- Update Finished: $(date) ---"
```

4. **Save and Exit**: Press Ctrl+O, Enter, then Ctrl+X.  
5. **Make it Executable**:

```
chmod +x ~/update-docker.sh
```

---

### **Phase 2: Testing (The "Dry Run")**

Before scheduling it, run it manually to ensure there are no syntax errors.

* **Run the script**: sudo ./update-docker.sh  
* **What to expect**: Your SSH session should stay active, but if you are looking at the Semaphore Web UI, it will disconnect temporarily when the stack container restarts. This is expected.

---

### **Phase 3: Scheduling with Cron**

We will schedule this for **Saturdays at 11:00 UTC**.

1. **Open the Crontab**:

```
crontab -e
```

*(If asked, choose 1 for nano).*

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

### **Phase 4: Verification & Troubleshooting**

After the first Saturday passes, you can check if it worked by looking at the log file we created:

* **View the log**: cat \~/docker\_update.log  
* **Check Server Time**: To ensure "11:00" aligns with your expectations, type date \-u in your terminal to see the current time in UTC.

\[\!IMPORTANT\]  
**Skeptical Double-Check**: Open your docker-compose.yaml file inside /mnt/controller/docker/stack. Ensure your Semaphore service has restart: always. If it doesn't, and the update script stops the container but fails to restart it, your automation will stay offline until you manually start it