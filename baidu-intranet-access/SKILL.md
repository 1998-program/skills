---
name: baidu-intranet-access
description: Use when the user needs to visit, read, summarize, or extract content from Baidu intranet pages behind UUAP login, such as ku.baidu-int.com, console.cloud.baidu-int.com, okr.baidu-int.com, family.baidu.com, erp.baidu.com, or other internal Baidu sites.
---

# Baidu Intranet Access

## Core Rules

- Communicate naturally. Do not mention skill workflows, headless/headed mode, persistent profiles, login detection internals, or implementation details unless the user explicitly asks.
- Try headless access first with `playwright-cli open --persistent`.
- Always use `--persistent` so the login session is reused.
- Switch to `--headed --persistent` only when the page redirects to UUAP/login.
- Never hardcode a user data directory or a local `playwright-cli` path.
- Treat persistent browser profile data as sensitive credentials. Do not print, copy, or commit it.

## Quick Workflow

1. Fetch the page in headless persistent mode:

```bash
playwright-cli open --persistent "<URL>" && playwright-cli eval "JSON.stringify({ url: location.href, title: document.title, body: document.body.innerText })" && playwright-cli close
```

2. Parse the returned JSON:

- Logged in: `url` remains on the requested intranet page and `body` contains the page text.
- Login required: `url` contains `uuap.baidu.com`, `title` looks like "Unified Authentication Center", or `body` mentions QR/fingerprint login.

3. If login is required:

```bash
playwright-cli close
playwright-cli open --headed --persistent "<URL>"
```

Tell the user: "I've opened a browser window. Please log in and let me know when you're done."

After the user confirms:

```bash
playwright-cli eval "JSON.stringify({ url: location.href, title: document.title, body: document.body.innerText })" && playwright-cli close
```

## If playwright-cli Is Missing

Locate it dynamically before installing:

```bash
PCLI=$(command -v playwright-cli || npm bin -g 2>/dev/null | xargs -I{} echo {}/playwright-cli)
```

If still unavailable and the task requires it, install with user approval if needed:

```bash
npm install -g @playwright/cli@latest
```

## Useful Commands

Open a page headlessly:

```bash
playwright-cli open --persistent "<URL>"
```

Open a page visibly for login:

```bash
playwright-cli open --headed --persistent "<URL>"
```

Extract page content:

```bash
playwright-cli eval "JSON.stringify({ url: location.href, title: document.title, body: document.body.innerText })"
```

Inspect elements:

```bash
playwright-cli snapshot
```

Take a screenshot:

```bash
playwright-cli screenshot --filename=intranet-page.png
```

Close the browser:

```bash
playwright-cli close
```

## Common Sites

Use these known entry points when the user names a Baidu internal platform without a URL:

| Platform | URL |
| --- | --- |
| Knowledge base | https://ku.baidu-int.com |
| Weekly reports | https://okr.baidu-int.com/pages/weekly.html |
| iCafe | https://console.cloud.baidu-int.com/devops/icafe |
| iCode | https://console.cloud.baidu-int.com/devops/icode |
| OKR | https://okr.baidu-int.com/ |
| Meeting rooms | http://meeting.baidu.com/index.html#/home |
| iPipe | https://console.cloud.baidu-int.com/devops/ipipe |
| Monitor | http://monitor.baidu.com/ |
| CNAP | https://console.cloud.baidu-int.com/devops/appspace |
| F-CNAP | https://console.cloud.baidu-int.com/vercel |
| iReport | https://console.cloud.baidu-int.com/devops/ireport/ |
| Sugar BI | https://sugar.baidu-int.com/groups?__scp__=Baidu |
| Family | https://family.baidu.com/index/ |
| ERP | https://erp.baidu.com |
| HR shared services | https://hssc.baidu.com |

For a larger Baidu intranet directory, read `references/references.md`.
