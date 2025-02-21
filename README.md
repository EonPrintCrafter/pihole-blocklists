# 🚀 Pi-hole Automated Blocklist Updater  

This repository automates the process of **fetching, merging, and deduplicating multiple blocklists** for **Pi-hole**.  
With this setup, Pi-hole will always use an up-to-date and optimized blocklist without duplicates, fully automated through **GitHub Actions**.  

---

## 📌 Features  

✅ **Fetches blocklists** from multiple sources (GitHub, raw URLs, etc.).  
✅ **Merges and removes duplicates** to create a single, optimized list.  
✅ **Automatically updates** every 24 hours via **GitHub Actions**.  
✅ **Pi-hole fetches the final blocklist** every 24 hours for **seamless updates**.  
✅ **Manually trigger updates** whenever needed.  

---

## ⚙️ Setup Guide  

### 1️⃣ Fork This Repository  

1. Click **Fork** in the top-right corner of this repository to create your own copy.  
2. Clone your forked repository to your local system:  

   ```sh
   git clone https://github.com/YOUR-USERNAME/pihole-blocklists.git
   cd pihole-blocklists
   ```

---

### 2️⃣ Add Your Blocklists  

1. Open `lists.txt` in this repository.  
2. Add the URLs of blocklists you want to include, **one per line**:  

   ```txt
   https://some-blocklist-url.com/list.txt
   https://another-source.com/ads.txt
   https://raw.githubusercontent.com/someuser/blocklist/main/list.txt
   ```

3. Commit and push your changes:  

   ```sh
   git add lists.txt
   git commit -m "Added new blocklists"
   git push
   ```

---

### 3️⃣ Enable GitHub Actions  

1. Go to your forked repository on GitHub.  
2. Click on the **"Actions"** tab.  
3. If GitHub Actions is disabled, click **"Enable Actions"**.  
4. The **workflow will now automatically run every 24 hours** to update your blocklist.  

---

### 4️⃣ Get the Final Blocklist URL  

Once the workflow completes, your **cleaned and deduplicated blocklist** will be available at:  

```
https://raw.githubusercontent.com/YOUR-USERNAME/pihole-blocklists/main/merged_lists/final_blocklist.txt
```

---

### 5️⃣ Add the Blocklist to Pi-hole  

On your Raspberry Pi, **run these commands** to add the new blocklist to Pi-hole:  

```sh
pihole -a adlist add https://raw.githubusercontent.com/YOUR-USERNAME/pihole-blocklists/main/merged_lists/final_blocklist.txt
pihole -g  # Update Gravity
```

---

### 6️⃣ Automate Pi-hole Updates  

To ensure Pi-hole fetches the latest blocklist **automatically every 24 hours**, create a cron job:  

```sh
crontab -e
```

Add this line at the bottom:  

```
0 3 * * * pihole -g
```

📌 **This updates Pi-hole every night at 3 AM.**  

---

## 🎯 How to Manually Update the Blocklist  

If you don’t want to wait for the automatic GitHub Actions run, you can manually **trigger an update**:  

1. Go to the **"Actions"** tab in your GitHub repository.  
2. Select **"Update Blocklist"** workflow.  
3. Click **"Run workflow"** to manually trigger an update.  

---

## 🔧 Advanced Usage  

### 🔹 Run the Merge Script Locally  

If you want to manually test and generate a blocklist on your local system, you can do it with:  

```sh
mkdir -p merged_lists
touch merged_lists/combined.txt

while read -r url; do
  curl -s $url >> merged_lists/combined.txt
done < lists.txt

sort -u merged_lists/combined.txt -o merged_lists/final_blocklist.txt
```

This will:  
✅ Fetch all blocklists from `lists.txt`.  
✅ Merge them into `merged_lists/combined.txt`.  
✅ Remove duplicates and save the **final blocklist** in `merged_lists/final_blocklist.txt`.  

---

### 🔹 Modify GitHub Actions Schedule  

By default, the blocklist updates **every 24 hours at midnight UTC**.  
To change this schedule, edit `.github/workflows/update-blocklist.yml` and modify this line:  

```yaml
- cron: "0 0 * * *"  # Runs every day at midnight UTC
```

📌 **Use [crontab.guru](https://crontab.guru/) to generate a custom cron schedule.**  

---

## 🎯 Example: GitHub Actions Workflow  

This workflow:  
- Runs **every 24 hours**.  
- Fetches blocklists from `lists.txt`.  
- Merges and deduplicates them.  
- Pushes the final blocklist back to GitHub.  

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

Now, Pi-hole will always have an **up-to-date, optimized** blocklist **without duplicates**, fully automated with **GitHub Actions**! 🚀🔥  

---

## ❓ FAQ  

### ❔ What happens if a blocklist URL goes down?  
- The workflow will **skip the broken URL** and continue processing other blocklists.  
- If a blocklist is permanently down, remove it from `lists.txt`.  

### ❔ Can I add my own custom domains?  
Yes! Just edit `merged_lists/final_blocklist.txt` and add your own domains manually, then push the changes.  

### ❔ How do I manually edit the final blocklist?  
Clone the repository, edit `merged_lists/final_blocklist.txt`, and commit the changes:  

```sh
git add merged_lists/final_blocklist.txt
git commit -m "Added custom blocklist entries"
git push
```

### ❔ Can I disable GitHub Actions if I don’t want automatic updates?  
Yes! Go to the **"Actions"** tab, click on **"Manage Actions"**, and disable it.  

---

## 📜 License  

This project is licensed under the **MIT License**.  
Feel free to modify and improve it!  

---

## 💡 Contributing  

Want to improve this script? Contributions are welcome!  

1. Fork the repository.  
2. Make your changes.  
3. Submit a pull request.  

---

## 🚀 Support  

If you find this useful, consider **starring ⭐ the repository**!  
For any issues, feel free to open a **GitHub Issue**.  

---
