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

- Use the Timestamps Bookmarklet

A single-click bookmarklet that extracts chapter timestamps from guide.handmadehero.org pages and downloads a self-contained HTML snippet the player can use. It generates the correct filename: Handmade Hero Day NNN – Title (1080p_30fps_H264-128kbit_AAC).html

### Install

1. Create a new bookmark in your browser (Bookmarks Bar recommended).
2. Set the Name to anything you like (e.g., HH Timestamps).
3. Copy–paste the code below into the bookmark’s URL/Location field:

```html
javascript:(function(){function saveHtmlFile(n,t){var b=new Blob([t],{type:'text/html;charset=utf-8'});var U=window.URL||window.webkitURL;var href=(U&&U.createObjectURL)?U.createObjectURL(b):'data:text/html;charset=utf-8,'+encodeURIComponent(t);var a=document.createElement('a');a.href=href;a.download=n;document.body.appendChild(a);a.click();a.remove();if(U&&U.revokeObjectURL&&href.indexOf('blob:')===0){setTimeout(function(){U.revokeObjectURL(href)},0)}}function toS(t){t=(t||'').trim();if(/^\d+$/.test(t))return+t;if(/^\d+:\d{1,2}$/.test(t)){var a=t.split(':').map(Number);return a[0]*60+a[1]}if(/^\d+:\d{2}:\d{2}$/.test(t)){var b=t.split(':').map(Number);return b[0]*3600+b[1]*60+b[2]}return null}function meta(){var d=null,title='TITLE';var e=document.querySelector('.episode_name');if(e) title=e.textContent.trim();var m=(location.pathname||'').match(/day(\d{1,3})/i);if(m) d=+m[1];if(d==null){var mt=(document.title||'').match(/Day\s*(\d+)\s*[\-%E2%80%93%E2%80%94]\s*(.+?)(?:\s*[\-%E2%80%93%E2%80%94]|$)/);if(mt){d=+mt[1];if(!e) title=mt[2].trim()}}return{day:d,title:title}}function parseCHAPTERS(text){var m=text.match(/const\s+CHAPTERS\s*=\s*\[[\s\S]*?\]/)||text.match(/CHAPTERS\s*=\s*\[[\s\S]*?\]/);if(!m)return null;var arr=m[0].replace(/^[^\[]*\[/,'[').replace(/\][^\]]*$/,' ]');var lines=arr.split('\n').map(function(l){return l.replace(/\/\/.*$/,'')});var jsonish=lines.join('\n').replace(/([,{]\s*)start\s*:/g,'$1"start":').replace(/([,{]\s*)label\s*:/g,'$1"label":').replace(/,\s*\]/g,']');try{var a=JSON.parse(jsonish);if(Array.isArray(a))return a.filter(function(x){return x&&typeof x.start==='number'&&x.label}).map(function(x){return{start:Math.round(x.start),label:String(x.label)}})}catch(e){}return null}function parseCinera(){var out=[];document.querySelectorAll('.markers .marker').forEach(function(n){var ts=n.getAttribute('data-timestamp');if(!ts)return;var start=Math.round(parseFloat(ts));var cont=n.querySelector('.cineraContent');if(!cont)return;var txt=(cont.textContent||'').replace(/^\s*\d(?::?\d{1,2}){0,2}\s*/,'').trim();if(txt) out.push({start:start,label:txt})});if(!out.length)return null;out.sort(function(a,b){return a.start-b.start});var seen=new Set(),uniq=[];out.forEach(function(it){var k=it.start+'|'+it.label.toLowerCase();if(!seen.has(k)){seen.add(k);uniq.push(it)}});return uniq}function parseText(){var items=[];var parts=(document.documentElement.textContent||'').split(/\r|\n/);for(var i=0;i<parts.length;i++){var t=parts[i].replace(/\s+/g,' ').trim();var mm=t.match(/^(\d(?::?\d{1,2}){0,2})\s+(.{3,})$/);if(!mm)continue;var s=toS(mm[1]);if(s!=null)items.push({start:s,label:mm[2].trim()})}if(!items.length)return null;items.sort(function(a,b){return a.start-b.start});var seen=new Set(),out=[];for(var j=0;j<items.length;j++){var k=items[j].start+'|'+items[j].label.toLowerCase();if(!seen.has(k)){seen.add(k);out.push(items[j])}}return out}function build(ch){var L=[];L.push('<!doctype html>');L.push('<meta charset="utf-8">');L.push('<script>');L.push('  // List your chapter markers here:');L.push('  // start/end are seconds; end is optional (player will infer)');L.push('  const CHAPTERS = [');for(var i=0;i<ch.length;i++){L.push('    { start: '+ch[i].start+', label: '+JSON.stringify(ch[i].label)+' }'+(i<ch.length-1?',':''))}L.push('  ];');L.push('');L.push('  // Respond to the player\\'+'s query');L.push('  window.addEventListener("message", (ev) => {');L.push('    const msg = ev.data || {};');L.push('    if (msg.type !== "timestamps-query") return;');L.push('    ev.source?.postMessage({ type: "timestamps-response", chapters: CHAPTERS }, "*");');L.push('  });');L.push('</script>');return L.join('\n')}var m=meta();var chapters=parseCinera()||parseCHAPTERS(document.documentElement.textContent||'')||parseText();if(!chapters||!chapters.length){alert('No chapters found on this page.');return}var html=build(chapters);var dayStr=m.day?('Day '+String(m.day).padStart(3,'0')):'Day XXX';var fname='Handmade Hero '+dayStr+' - '+(m.title||'TITLE')+' (1080p_30fps_H264-128kbit_AAC).html';saveHtmlFile(fname,html);})();
```
### Usage

1. Navigate to any HH guide page, e.g. https://guide.handmadehero.org/code/day051/.
2. Click the HH Timestamps bookmark.
3. Your browser will download a file named like:
  Handmade Hero Day 051 - Separating Entities By Update Frequency (1080p_30fps_H264-128kbit_AAC).html
4. Drop that file inside `/timestamps_data` folder the player expects. The file responds to the timestamps-query postMessage with the CHAPTERS array.

### What the output looks like

The generated file is a tiny HTML page containing:
 1. A const CHAPTERS = [ { start: <seconds>, label: "..." }, ... ] array
 2. A window.message listener that replies with { type: "timestamps-response", chapters: CHAPTERS }

No external dependencies; it’s fully self-contained.

Or

- Create **one file per episode** in `/timestamps_data` using this minimal template:

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
