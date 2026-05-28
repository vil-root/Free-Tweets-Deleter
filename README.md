# X (Twitter) Bulk Tweet Deleter Script

A lightweight, free, and open-source JavaScript snippet designed to bulk delete tweets directly from your browser's developer console. It automates the tedious process of clicking through menus by simulating real human interactions with the updated X (Twitter) web interface.

## 🚀 Features

- **100% Free:** No premium API subscriptions or paid third-party platforms required.
- **Privacy-Focused:** Runs entirely locally in your browser. Your account credentials, tokens, and data never leave your machine.
- **Smart Skipping:** Automatically handles retweets or non-deletable items by dismissing the menu and moving forward.
- **Customizable Delays:** Includes built-in safety intervals to comply with browser rendering and prevent immediate rate limits.

## 🛠️ How to Use

Follow these steps carefully to clean up your profile:

1. **Switch to English:** Log into your account on [X.com](https://x.com) and temporarily change your display language to **English** (the script relies on the word "Delete" to identify the correct menu action).
2. **Go to Your Profile:** Navigate to your personal profile page where your tweets are visible (`https://x.com/your_username`).
3. **Open Developer Tools:** Right-click anywhere on the page and select **Inspect** (or press `F12` / `Ctrl+Shift+I` on Windows, `Cmd+Option+I` on Mac) and switch to the **Console** tab.
4. **Paste and Run:** Copy the script code below, paste it into the console, and press `Enter`.

### The Script

```javascript
async function deleteAllTweets() {
  const delay = (ms) => new Promise(res => setTimeout(res, ms));
  
  while (true) {
    // 1. Locate the caret/more button for the first tweet on screen
    let caret = document.querySelector('[data-testid="caret"]');
    if (!caret) {
      // Scroll down to load more tweets if none are found on screen
      window.scrollTo(0, document.body.scrollHeight);
      await delay(2000);
      caret = document.querySelector('[data-testid="caret"]');
      if (!caret) {
        console.log("No more tweets found or process finished successfully!");
        break;
      }
    }
    
    caret.click();
    await delay(500); // Wait for the dropdown menu to animate open
    
    // 2. Locate the "Delete" option within the active menu
    const menuItems = Array.from(document.querySelectorAll('[role="menuitem"]'));
    const deleteBtn = menuItems.find(item => item.textContent.includes('Delete'));
    
    if (deleteBtn) {
      deleteBtn.click();
      await delay(500); // Wait for the confirmation dialog
      
      // 3. Confirm the deletion
      const confirmBtn = document.querySelector('[data-testid="confirmationSheetConfirm"]');
      if (confirmBtn) {
        confirmBtn.click();
        console.log("Successfully deleted a tweet.");
        await delay(1500); // Safety cooldown before taking the next action
      }
    } else {
      // If it's a retweet/item owned by someone else, close the menu and scroll past it
      document.body.click();
      window.scrollBy(0, 300);
      await delay(1000);
    }
  }
}

deleteAllTweets();
