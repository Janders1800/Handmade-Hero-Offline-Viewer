## Handmade Hero — Offline Viewer

A tiny, self-contained web app for watching Casey Muratori’s *Handmade Hero* episodes **offline**.
It loads locally saved **MP4 videos** and optional **chapter timestamps**, and provides:

- Video player with keyboard shortcuts
- Resume-where-you-left-off per episode
- Playback speed controls
- Clickable chapter list
- Simple index with visited ✓ markers

No external dependencies or servers required, just open the **index.html** file in your browser (or run a simple local server) and go.


# Handmade Hero — Offline Viewer (Local Setup)

This viewer loads **local MP4 files** and **chapter timestamps** from two folders:

./video_data/
./timestamps_data/


Both the video and the timestamps file names must follow the **exact same base name** the player expects, otherwise the episode won’t load or won’t show chapters.

---

## 1) Folder layout
```
Handmade Hero — Offline Viewer/
├─ index.html
├─ video_player.html
├─ /video_data
│ └─ Handmade Hero Day 051 - Separating Entities By Update Frequency (1080p_30fps_H264-128kbit_AAC).mp4
└─ /timestamps_data
  └─ Handmade Hero Day 051 - Separating Entities By Update Frequency (1080p_30fps_H264-128kbit_AAC).html
```

> The video player builds a base name like:
>
> ```
> Handmade Hero Day <DDD> - <TITLE> (1080p_30fps_H264-128kbit_AAC)
> ```
>
> where `<DDD>` is the **3-digit** day (e.g., `051`).
> `<TITLE>` must match the title in `index.html` for that day **exactly** (same punctuation, spacing, and case).

---

## 2) Exact naming conventions

For **each episode**:

- **Base name**

Handmade Hero Day DDD - TITLE (1080p_30fps_H264-128kbit_AAC)


- **Video** → `/video_data` with `.mp4`:

/video_data/Handmade Hero Day DDD - TITLE (1080p_30fps_H264-128kbit_AAC).mp4


- **Timestamps** → `/timestamps_data` with `.html` (or `.htm`):

/timestamps_data/Handmade Hero Day DDD - TITLE (1080p_30fps_H264-128kbit_AAC).html


The player tries `.html` first, then `.htm`.

**Examples**

- Day 051 (`Separating Entities By Update Frequency`)

/video_data/Handmade Hero Day 051 - Separating Entities By Update Frequency (1080p_30fps_H264-128kbit_AAC).mp4
/timestamps_data/Handmade Hero Day 051 - Separating Entities By Update Frequency (1080p_30fps_H264-128kbit_AAC).html


- Day 045 (replace `TITLE` with whatever your `index.html` lists)

/video_data/Handmade Hero Day 045 - TITLE (1080p_30fps_H264-128kbit_AAC).mp4
/timestamps_data/Handmade Hero Day 045 - TITLE (1080p_30fps_H264-128kbit_AAC).html


---

## 3) Downloading / placing videos

1. Obtain each episode MP4 from your archive/source.
2. Rename to the **exact** base name + `.mp4` (see above).
3. Place in the `video_data` folder.

If the filename doesn’t match precisely, the player won’t find the video.

---

## 4) Creating timestamps (chapters)

Create **one file per episode** in `/timestamps_data` using this minimal template:

```html
<!doctype html>
<meta charset="utf-8">
<script>
// Chapter markers (seconds). `end` is optional; the player infers it.
const CHAPTERS = [
  { start: 0,    label: "Intro" },
  { start: 75,   label: "Update frequency overview" },
  { start: 420,  label: "Hot vs cold entities" },
  { start: 1230, label: "Data layout implications" },
  // ...
];

// Respond to the player's query
window.addEventListener("message", (ev) => {
  const msg = ev.data || {};
  if (msg.type !== "timestamps-query") return;
  ev.source?.postMessage({ type: "timestamps-response", chapters: CHAPTERS }, "*");
});
</script>
```

Save as (pick one extension):

/timestamps_data/Handmade Hero Day DDD - TITLE (1080p_30fps_H264-128kbit_AAC).html

or

/timestamps_data/Handmade Hero Day DDD - TITLE (1080p_30fps_H264-128kbit_AAC).htm

## 5) Example: Day 051 files

    Video

    /video_data/Handmade Hero Day 051 - Separating Entities By Update Frequency (1080p_30fps_H264-128kbit_AAC).mp4

    Timestamps

    /timestamps_data/Handmade Hero Day 051 - Separating Entities By Update Frequency (1080p_30fps_H264-128kbit_AAC).html



## 6) Tips & troubleshooting

Video doesn’t load → The MP4 filename in /video_data does not exactly match the expected base name. Check title/spaces/punctuation/case.

Chapters panel is empty → The timestamps file is missing or misnamed in /timestamps_data, or extension mismatch (.html vs .htm).

Running from file:// usually works. If your browser is restrictive, ¯\\\_(ツ)_/¯.

The player remembers time per episode and a global volume using localStorage. Clearing site data resets these.

The index marks episodes as visited (✓) using localStorage. If it doesn’t update instantly, refresh the index page.
