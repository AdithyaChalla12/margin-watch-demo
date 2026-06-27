# Margin Watch

A running ledger of every SKU's price against its target margin. Ask it a question, or let it flag what's slipping.

**Live demo:** [adithyachalla12.github.io/margin-watch-demo](https://adithyachalla12.github.io/margin-watch-demo/)

Built for the [Agentforce In Action Challenge](https://trailhead.salesforce.com/agentforceinaction) — `#AgentforceInAction`

---

## Overview

Pricing teams need fast, defensible margin and pricing decisions, but the process is usually manual. Someone has to check margin data, calculate the impact of a price change, and write up a rationale, often pulling from spreadsheets and disconnected systems along the way.

Margin Watch is an Agentforce Service Agent that automates this from start to finish. This repo hosts the front end: a static pricing desk page that displays sample SKU margin data and embeds a live chat widget connected to the actual Agentforce agent running in Salesforce.

## Features

- **Scrolling ticker** of SKUs trending above or below their target margin
- **Margin board** of price tag cards, each showing cost, current price, and a visual gauge of margin against target
- **Live Agentforce chat widget** (bottom corner) backed by a real Salesforce Embedded Messaging deployment, not a mock-up
- Fully static page, no build step, deployed directly via GitHub Pages

## How It Works

```
Customer message
      |
      v
Omni-Channel Routing Flow  ("Route to AI Agent")
      |
      v
Margin Watch  (Agentforce Service Agent)
      |
      +-- Margin & Pricing Lookup subagent   -> retrieves live margin & pricing data
      |
      +-- Price Action subagent              -> calculates price-change impact
                                                  and drafts a rationale via Prompt Builder
      |
      v
Response delivered in the chat widget
```

The agent itself, its two subagents, the Omni-Channel routing flow, and the Prompt Builder rationale template are all configured in Salesforce. This repo is the embeddable surface that lets anyone talk to it from a browser, no Salesforce login required.

## Tech Stack

- HTML, CSS, vanilla JavaScript (no framework, no build step)
- Google Fonts (Fraunces, IBM Plex Sans, IBM Plex Mono)
- Salesforce Agentforce (Service Agent, Subagents, Prompt Builder)
- Salesforce Embedded Messaging (Agentforce chat widget)
- GitHub Pages (hosting)

## Project Structure

```
margin-watch-demo/
└── index.html   # everything: markup, styles, sample data, and the embedded chat snippet
```

## Sample Data

The SKU data shown on the board is illustrative and lives directly in `index.html`:

```js
const skus = [
  { cat:"Coffee", name:"Organic Roast Coffee 12oz", sku:"SKU-1042", cost:6.10, price:9.49, margin:35.7, target:42 },
  // ...
];
```

To use real data instead, replace the `skus` array with values pulled from your own pricing source (for example, a `Product_Pricing__c` object in Salesforce), or wire this page up to an API. Each entry needs a category, name, SKU, cost, current price, actual margin, and target margin; the board and ticker render automatically from this array.

## Running Locally

No build step needed. Either:

- Open `index.html` directly in a browser, or
- Serve it with any static file server, for example:
  ```bash
  npx serve .
  ```

## Deployment

This page is deployed via GitHub Pages from this repository. Pushing to the default branch updates the live demo automatically.

## The Agentforce Chat Widget

The chat widget is a real Salesforce Embedded Messaging deployment, initialized near the bottom of `index.html`:

```html
<script type='text/javascript'>
  function initEmbeddedMessaging() {
    try {
      embeddedservice_bootstrap.settings.language = 'en_US';
      embeddedservice_bootstrap.init(
        '<your org ID>',
        '<your deployment name>',
        '<your Experience Cloud site URL>',
        { scrt2URL: '<your SCRT2 URL>' }
      );
    } catch (err) {
      console.error('Error loading Embedded Messaging: ', err);
    }
  }
}
</script>
<script src='<your bootstrap.min.js URL>' onload='initEmbeddedMessaging()'></script>
```

These values come from **Setup → Embedded Service → [your deployment] → Code Snippets** in your Salesforce org, and are specific to your org and channel.

## Submission

This project is a submission to Salesforce's Agentforce In Action Challenge. See the [contest page](https://trailhead.salesforce.com/agentforceinaction) for details.

## Author

Adithya Challa
