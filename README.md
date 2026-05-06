[README.md](https://github.com/user-attachments/files/27425357/README.md)
# YT Comment Excavator

A single-file browser tool that searches YouTube videos by topic and surfaces every comment left by a specific channel. Also displays a channel's public activity feed. No installation required — open the HTML file in any browser, or use it live via GitHub Pages.

---

## Features

- Search YouTube for videos matching any query and scan them for comments from a specified channel
- Parallel worker system scans multiple videos simultaneously for faster results
- Comments stream into the results panel live as each video finishes scanning
- Filter and sort found comments by date or like count
- Channel activity feed showing uploads, likes, subscriptions, and playlist additions
- Session quota counter tracking YouTube API units spent
- Export all found comments to a standalone HTML file

---

## Requirements

- A **YouTube Data API v3 key** from [Google Cloud Console](https://console.cloud.google.com)
  - Create a project → Enable the YouTube Data API v3 → Create an API key under Credentials
  - The default free quota is **10,000 units per day**, resetting at midnight Pacific Time

---

## Usage

1. Open `youtube_comment_finder.html` in any modern browser
2. Paste your YouTube Data API v3 key into the API key field
3. Enter a search query (e.g. `javascript tutorials`)
4. Set how many videos to scan and how many parallel workers to use
5. Enter the target channel as either a **Channel ID** (`UCxxxxxx`) or a **display name**
6. Click **Excavate Comments**

For the Activity tab, a Channel ID is required — display names are not accepted by the Activities API.

---

## Known Limitations

### No server-side comment filtering
The YouTube API does not support filtering comments by author. There is no parameter to say "only return comments from channel X." The tool must download every comment on every video and filter locally. On videos with tens of thousands of comments, this is slow and quota-expensive, and there is no way around this within the official API.

### Comment pages are sequential, not parallel
Each page of comments requires a `nextPageToken` from the previous page's response. This means pages within a single video cannot be fetched in parallel — they must be requested one after another. Only the work across *different videos* can be parallelised.

### The Activities API does not include comments
The `activities` endpoint tracks uploads, likes, subscriptions, and playlist additions — but commenting is not an activity type Google exposes through this API. The Activity tab therefore cannot show where a channel has left comments. That remains only discoverable through the brute-force comment search.

### Display name matching is approximate
When you enter a display name instead of a Channel ID, matching is done by comparing the author name on each comment case-insensitively. If two channels share a similar display name, both will appear in results. Using a Channel ID (`UCxxxxxx`) gives exact, unambiguous matching.

### The session quota counter is an estimate
The counter tracks API calls made during the current browser session and applies known unit costs (100 per search call, 1 per comment or activity call). It does not know your true remaining daily balance — it cannot account for calls made before the page was loaded, calls made by other apps sharing the same key, or calls from previous sessions. For your real remaining quota, check [Google Cloud Console → APIs & Services → Quotas](https://console.cloud.google.com/apis/api/youtube.googleapis.com/quotas).

### Quota cost scales with video popularity
Because all comments must be fetched before filtering, a single video with 100,000 comments costs ~1,000 API units to scan (100 pages × 1 unit each). Searches that include very popular videos can exhaust a day's quota quickly.

### Top-level comments only
The tool scans top-level comments only. Replies nested under other comments are not fetched.

---

## API Quota Reference

| Operation | Cost |
|---|---|
| Search page (50 results) | 100 units |
| Comment page (100 comments) | 1 unit |
| Activity page (50 activities) | 1 unit |
| Daily default limit | 10,000 units |

To request a free quota increase, use the form in [Google Cloud Console](https://console.cloud.google.com/apis/api/youtube.googleapis.com/quotas).

---

## License

This project is licensed under the [MIT License](https://mit-license.org/). You are free to use, copy, modify, merge, publish, distribute, and sublicense it, provided the original copyright notice is kept intact. See the `LICENSE` file for the full text.

---

Built with the assistance of [Claude](https://claude.ai), an AI assistant by [Anthropic](https://www.anthropic.com).
