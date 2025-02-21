# pihole-blocklists
# 🚀 Pi-hole Automated Blocklist Updater  

This repository automates the process of fetching, merging, and deduplicating multiple blocklists for **Pi-hole**.  
It ensures that Pi-hole always has an up-to-date and optimized blocklist without duplicates.  

---

## 📌 How It Works  

1. **GitHub Actions** automatically runs every 24 hours to:  
   - Fetch multiple blocklists from different sources.  
   - Merge them into a single, clean list.  
   - Remove duplicates and format it for Pi-hole.  
   - Store the final blocklist in the repository.  

2. **Pi-hole** fetches the cleaned list every 24 hours to update its **Gravity database**.  

---

## ⚙️ Setup Guide  

### 1️⃣ Add Your Blocklists  
1. Open `lists.txt` in this repository.  
2. Add the URLs of blocklists you want to use, one per line:  

   ```txt
   https://some-blocklist-url.com/list.txt
   https://another-source.com/ads.txt
   https://raw.githubusercontent.com/someuser/blocklist/main/list.txt
   ```

3. Save and commit the changes.  

---

### 2️⃣ Configure GitHub Actions  
The repository includes a GitHub Actions workflow (`.github/workflows/update-blocklist.yml`) that:  

- Runs every 24 hours at **midnight UTC**.  
- Fetches all blocklists.  
- Merges and deduplicates them.  
- Saves the final blocklist in `merged_lists/final_blocklist.txt`.  

📌 **You can also manually trigger the workflow from the "Actions" tab.**  

---

### 3️⃣ Get the Final Blocklist URL  
Once the workflow runs, your cleaned blocklist will be available at:  

```
https://raw.githubusercontent.com/YOUR-USERNAME/pihole-blocklists/main/merged_lists/final_blocklist.txt
```

---

### 4️⃣ Add the Blocklist to Pi-hole  
On your Raspberry Pi, run the following commands:  

```sh
pihole -a adlist add https://raw.githubusercontent.com/YOUR-USERNAME/pihole-blocklists/main/merged_lists/final_blocklist.txt
pihole -g  # Update Gravity
```

---

### 5️⃣ Automate Pi-hole Updates  
To ensure Pi-hole fetches the latest blocklist daily, set up a **cron job**:  

```sh
crontab -e
```

Add this line at the bottom:  

```
0 3 * * * pihole -g
```

📌 **This updates Pi-hole every night at 3 AM.**  

---

## 🎯 Example: GitHub Actions Workflow  
If needed, here’s the GitHub Actions workflow that runs automatically:  

```yaml
name: Update Blocklist

on:
  schedule:
    - cron: "0 0 * * *"  # Runs every 24h at midnight UTC
  workflow_dispatch:  # Allows manual triggering

jobs:
  update-blocklist:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout Repository
        uses: actions/checkout@v3

      - name: Install Dependencies
        run: sudo apt update && sudo apt install -y curl

      - name: Download and Merge Blocklists
        run: |
          mkdir -p merged_lists
          touch merged_lists/combined.txt

          while read -r url; do
            curl -s $url >> merged_lists/combined.txt
          done < lists.txt

          sort -u merged_lists/combined.txt -o merged_lists/final_blocklist.txt

      - name: Commit and Push Updated List
        run: |
          git config --global user.name "GitHub Actions"
          git config --global user.email "actions@github.com"
          git add merged_lists/final_blocklist.txt
          git commit -m "Updated blocklist on $(date)" || echo "No changes to commit"
          git push
```

---

## 🎉 Done!  
Now, Pi-hole will always have an **up-to-date, optimized** blocklist without duplicates, fully automated with **GitHub Actions**! 🚀🔥  
