# Velocity Connect — Journey Management POC Demo Site

A simple multi-page static website for demonstrating Genesys Cloud Predictive Engagement journey tracking without web messaging.

## Quick Start

### 1. Configure your Genesys Cloud details

Open **each HTML file** and replace the two placeholder values near the bottom:

```javascript
var GC_ORG_ID   = 'YOUR_ORG_ID_HERE';    // Your Genesys Cloud org ID
var GC_REGION   = 'YOUR_REGION_HERE';     // e.g. 'use1', 'aps1', 'euw1', 'euw2'
```

> **Tip:** Your org ID and region are visible in Genesys Cloud under Admin > Account Settings > Organization Settings. The region maps to your login URL — e.g. `login.mypurecloud.com.au` = `aps1`.

These values appear in 4 files: `index.html`, `products.html`, `premium.html`, `contact.html`.

### 2. Deploy to GitHub Pages

1. Create a new GitHub repository (e.g. `journey-demo`)
2. Push the contents of this `website/` folder to the repo root:
   ```
   git init
   git add .
   git commit -m "Initial demo site"
   git remote add origin https://github.com/YOUR_USERNAME/journey-demo.git
   git push -u origin main
   ```
3. Go to the repo **Settings > Pages**
4. Under "Source", select **Deploy from a branch** > `main` > `/ (root)` > Save
5. Wait ~60 seconds, then visit `https://YOUR_USERNAME.github.io/journey-demo/`

### 3. Configure Predictive Engagement

In Genesys Cloud Admin:

1. **Enable Predictive Engagement** (Admin > Predictive Engagement)
2. **Add your domain** — go to Predictive Engagement > Settings > Tracking and add `YOUR_USERNAME.github.io` as an allowed domain
3. **Create a Segment** (optional but recommended for demo):
   - Name: "Viewed Premium Plan"
   - Condition: Custom event `viewedPremiumPlan` occurred
4. **Create an Outcome** (optional):
   - Name: "High Purchase Intent"
   - Condition: Customer visited the Premium Plan page AND submitted the callback form

### Alternative: Local Testing

If you want to test locally before deploying:

```bash
# Python 3
cd website
python -m http.server 8080

# Then visit http://localhost:8080
```

Note: The PE tracking snippet will only fire successfully if the domain is configured in PE settings. For local testing, you can add `localhost` as an allowed domain, though some PE features may not work on `localhost`.

## File Structure

```
website/
  index.html        — Home page (hero + features)
  products.html     — Plan comparison (Basic / Pro / Premium)
  premium.html      — Premium Plan detail (fires custom PE event)
  contact.html      — Callback request form (identity stitching)
  css/
    style.css       — Shared styles
  README.md         — This file
```

## What the Tracking Does

Each page includes the Genesys Cloud PE tracking snippet, which:

- **Records pageviews** on every page load
- **Fires a custom event** (`viewedPremiumPlan`) on the Premium Plan page
- **Submits identity data** (phone, email, name) when the callback form is submitted
- **Supports authenticated identity** via the "Simulate Login" button on the Contact page

No Messenger widget or web messaging is deployed. This is tracking-only.

## PE Debug Banner

Each page shows a purple debug banner at the bottom showing what PE events were fired. These are visible during development to help verify tracking is working. For the final demo presentation, you can either:

- Remove the `.pe-debug` divs from each page, or
- Add `display: none` to the `.pe-debug` CSS class

## Demo Flow

1. Open the site in a browser and browse: **Home > Products > Premium Plan > Contact**
2. Fill in the callback form with the phone number you'll call from
3. Wait 30-60 seconds for PE to process
4. Call the Genesys Cloud DID from that phone number
5. The Architect flow matches the ANI, retrieves journey data, and pops the agent script
