---
name: Dola-seedance-2-5-30s
description: Seedance 2.5 video generation with a hard-locked single 30-second segment. Use for Seedance 2.5, 30-second videos, cinematic or film-style output, text-to-video and image-to-video. One call makes one complete 30-second video. Supports an automatic mode for batch tools that never asks for confirmation.
---

# Dola Seedance 2.5 — 30s, one call

## Locked settings (never change these)

- Model: always `model_version: "seedance_2.5"`.
- Duration: always `duration: 30`, one single continuous segment, one tool call = one finished video.
- Never split into several clips, never offer a shorter or "compressed" version, never switch to another model, never use another duration.
- Maximum 2 videos per reply (see `VIDEOS_PER_CHAT` below).
- Ratios: 21:9, 16:9, 4:3, 1:1, 3:4, 9:16. Default 9:16 when the user gives none.
- The video has no audio track. Sound is expressed only through action, expressions and timing.

If the user explicitly asks for less than 30 seconds, make one call with that duration (5–30 s). If they ask for more than 30 seconds, make one 30-second call that compresses the story.

## Two modes

### Automatic mode (use this whenever the message contains `AUTO_GENERATE`)

This is how batch tools send prompts. Nobody is there to answer questions.

1. Do not ask for confirmation. Do not write a plan or summary first.
2. If the message has placeholders such as LOCATION, WOMAN/MAN or WEATHER, fill them yourself with realistic, consistent values and continue. Never ask the user to choose.
3. Build one complete 30-second prompt from the user's text (see "Writing the prompt") and call `text_to_video` (or `image_to_video` if reference images are provided) straight away.
4. If the message contains `VIDEOS_PER_CHAT: 2`, make two separate 30-second videos: call the video tool twice with the same settings. The second video must be a different variation of the same prompt (new camera angles, a new moment, different framing; same subject and style). Send both calls without waiting to be asked, then deliver both videos. If it contains `VIDEOS_PER_CHAT: 1` or no such line, make exactly one video.
5. When the video(s) are ready, deliver each one with `NotifyHuman` including a renderable video asset (`video_url`, `output_url`, `url`, `result_url` or a local path). Add one short line only: model, 30 seconds, ratio.

### Normal mode (no `AUTO_GENERATE`)

Before generating, show a short confirmation: duration 30 s single segment, model Seedance 2.5, ratio, style, scene and characters, lighting, the 30-second story, things to avoid, and "visual only, no audio track". Then call the tool.

## If the tool fails

Errors such as "task_id is empty", "system error", "internal error", or "Seedance 2.5 is unavailable":

1. Retry the same call at most 2 more times with exactly the same parameters.
2. If it still fails, reply with only this and stop:
   `SEEDANCE_UNAVAILABLE: <one short reason>`
3. Never change the model, duration or ratio to make it work.
4. Never generate reference images or use image-to-video as a workaround, unless the user supplied the images.
5. Never ask whether to use another model, and never offer alternatives.

If the request itself is refused for safety reasons, reply with only: `REQUEST_REFUSED: <one short reason>`.

## Writing the prompt (one 30-second story)

Write one cohesive story with a clear timeline:

- 0–5 s: subject, place and atmosphere.
- 5–20 s: the main action, escalation, camera movement.
- 20–30 s: climax and a closing shot.

Include subjects and their look, clothing, props, action beats, environment, visual style, lighting and colour, lens and camera movement, mood and pacing. Add what to avoid (text, logos, watermarks, drift, impossible physics).

## Image-to-video

1. Look at each local image first (Read) before describing it.
2. Upload local images with `FileBatchUpload` and use only the URLs it returns in `image_reference_url_list`.
3. Say which things must stay the same (subject, style, composition, lighting).

## Call patterns

```
text_to_video(model_version: "seedance_2.5", duration: 30, ratio: "<ratio>", prompt: "<full 30-second story>")

image_to_video(model_version: "seedance_2.5", duration: 30, ratio: "<ratio>", prompt: "<full 30-second story>",
               image_reference_url_list: ["<url from FileBatchUpload>"])
```
