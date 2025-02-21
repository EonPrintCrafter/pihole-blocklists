# pihole-blocklists
🚀 Pi-hole Automated Blocklist Updater

This repository automates the process of fetching, merging, and deduplicating multiple blocklists for Pi-hole. It ensures that Pi-hole always has an up-to-date and optimized blocklist without duplicates.
🔹 How It Works

    GitHub Actions automatically runs every 24 hours to:
        Fetch multiple blocklists from different sources.
        Merge them into a single, clean list.
        Remove duplicates and format it for Pi-hole.
        Store the final blocklist in the repository.
    Pi-hole fetches the cleaned list every 24 hours to update its Gravity database.

📌 Setup Guide
1️⃣ Add Your Blocklists

    Open lists.txt in this repository.
    Add the URLs of blocklists you want to use, one per line:

    https://some-blocklist-url.com/list.txt
    https://another-source.com/ads.txt
    https://raw.githubusercontent.com/someuser/blocklist/main/list.txt

    Save and commit the changes.

2️⃣ Configure GitHub Actions

The repository already includes a GitHub Actions workflow (.github/workflows/update-blocklist.yml) that:

    Runs every 24 hours at midnight UTC.
    Fetches all blocklists.
    Merges and deduplicates them.
    Saves the final blocklist in merged_lists/final_blocklist.txt.

📌 You can also manually trigger the workflow from the "Actions" tab.
3️⃣ Get the Final Blocklist URL

Once the workflow runs, your cleaned blocklist will be available at:

https://raw.githubusercontent.com/YOUR-USERNAME/pihole-blocklists/main/merged_lists/final_blocklist.txt

4️⃣ Add the Blocklist to Pi-hole

On your Raspberry Pi, run:

pihole -a adlist add https://raw.githubusercontent.com/YOUR-USERNAME/pihole-blocklists/main/merged_lists/final_blocklist.txt
pihole -g  # Update Gravity

5️⃣ Automate Pi-hole Updates

To ensure Pi-hole fetches the latest blocklist daily, set up a cron job:

crontab -e

Add this line:

0 3 * * * pihole -g

(This updates Pi-hole every night at 3 AM)
🎉 Done!

Now, Pi-hole will always have an up-to-date, optimized blocklist without duplicates, fully automated with GitHub Actions! 🚀🔥
