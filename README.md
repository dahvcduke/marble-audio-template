# Marble Spatial Audio Viewer

An adapted World Labs Marble starter page with spatial audio, local editing, and static-site playback, powered by [SparkJS](https://sparkjs.dev). Download a Marble website starter export and replace its `index.html` with this file, keeping the exported assets beside it.

All viewer and editor code lives in `index.html`. Audio files and their settings are saved in the project folder and `scene.json`.

## Run locally

Serve the project over HTTP. Opening `index.html` directly as a file is not supported.

```bash
cd /path/to/your/exported/scene
python3 -m http.server 8000 --bind 127.0.0.1
```

No administrator privileges or `sudo` are needed. Keep the terminal running; press Ctrl+C to stop the server.

- [Viewer](http://127.0.0.1:8000/)
- [Local editor](http://127.0.0.1:8000/?edit=1)

Use a server without automatic reload while editing. Reloading after an autosave disconnects the project folder. The Python command above does not reload the page.

## Edit audio locally

Local editing requires a browser with folder write access, such as desktop Chrome or Edge. Editing is enabled only on the exact hostnames `localhost` or `127.0.0.1`, with `?edit=1` in the URL.

1. Open the local editor and click **Connect project folder**.
2. Select the folder being served, containing `index.html` and `scene.json`, and grant write access. The editor checks that its manifest matches the loaded scene.
3. Drop audio onto the page or click **+ Add**. Files are copied beside `index.html` with unique filenames.
4. Select an orb or its name in the audio panel. Drag the orb to move it across the view; scroll with it selected to adjust its distance. Click empty space or press Escape to deselect it.
5. Adjust volume and looping. Uploads, completed moves, and setting changes save automatically. Wait for **Saved** before closing the page or deploying the folder.

New uploads are limited to **10 sources per scene** and **5 MB (5,000,000 bytes) per file**. Oversized or unsupported files are skipped with a message. Audio format support depends on the browser's decoder. Existing manifest entries are preserved even if their audio cannot load; those entries still count toward the source limit.

Removing a source opens a confirmation dialog. Confirmation first removes its entry from `scene.json`, then permanently deletes the audio file if no remaining audio, mesh, or splat entry references it. Cancel or Escape leaves it intact.

A failed save keeps pending changes in the current tab and offers **Retry save / cleanup**. The editor refuses to overwrite a manifest changed outside the current tab. Avoid editing the same project from multiple tabs or tools at once.

Folder access is held only for the current page session. Reconnect the folder after each reload.

## Viewer controls

| Control | Behavior |
| --- | --- |
| Play / Pause, Play all / Pause all | Start or pause audio. Sources initially load paused. |
| Volume | Adjust playback for this visit. Viewer changes do not modify the saved scene. |
| Show orbs | Hide or show the audio markers without stopping playback. Hidden orbs cannot be selected or moved. |
| Reset view | Animate back to the starting camera position and orientation. Navigation input cancels the animation; reduced-motion preferences are respected. |
| Hide UI / Show UI | Hide the interface until explicitly restored. A dim Show UI button remains at the bottom right and brightens on hover. |
| H | Toggle UI visibility, except while typing or using the deletion dialog. |
| Fullscreen | Enter or exit fullscreen when supported. Escape also exits. Fullscreen is independent of UI visibility. |

Moving, clicking, and scrolling through the scene do not reveal hidden UI. Loop and deletion controls are available only in local edit mode. Orb visibility, UI visibility, and camera position are session preferences and are not saved to `scene.json`.

## Scene files

Keep these together when serving or deploying the project:

- `index.html` — viewer and editor.
- `scene.json` — scene manifest with splat, mesh, and audio references.
- `spark.module.min.js` — SparkJS renderer.
- `*.spz` and `*.glb` — assets referenced by the exported scene.
- `audio-<id>.<extension>` — audio files copied by the editor.

The editor preserves the existing manifest fields and adds or updates an `audio` array. Each source uses this structure:

```json
{
  "id": "unique-source-id",
  "filename": "audio-unique-source-id.mp3",
  "name": "Ambience.mp3",
  "position": [1.2, 0.8, -2],
  "loop": true,
  "volume": 0.8,
  "color": 16755200
}
```

Filenames refer to audio files directly beside `index.html`. Volume ranges from 0 to 1; color is an integer RGB value.

## Deploy

Upload the project folder to a static host after all edits have saved. Keep the manifest and its referenced assets together with the same relative paths. No backend or build step is required.

On a deployed domain, the Local edit button is hidden and `?edit=1` cannot enable editing. Visitors load the saved audio from `scene.json`; their playback controls do not write to the server.

Three.js, lil-gui, and Stats.js load from CDNs, so the page still requires access to those services. Shipping the scene assets alongside the page does not make it fully offline.

## Resources

- [SparkJS documentation](https://sparkjs.dev/docs)
- [SparkJS examples](https://sparkjs.dev/examples/)
- [SparkJS example source](https://github.com/sparkjsdev/spark/tree/main/examples)
