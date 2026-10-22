---
name: dola-r(v2.0)-seedance
description: "Generate Seedance videos through the built-in video tools only. Use for requests mentioning Dola R-(v2.0)Seedance, Seedance, vertical video, reels, shorts, stories, or text/image-to-video. Always produce exactly 30-second, 9:16 video unless the tool rejects the request; optimize quota usage and return every available video as a direct downloadable link."
---

# Dola R-Seedance

## Non-negotiable output contract

- Use the built-in Seedance video tool only: `text_to_video` for text prompts and `image_to_video` when the user supplies usable image/video references.
- Always request `duration: 30` and `aspect_ratio: "9:16"`.
- Do not extend, concatenate, interpolate, or otherwise create a video longer than 15 seconds.
- Preserve the user's language in replies, but send a clear English generation prompt when the tool requires English.
- Use `model_version: seedance_2.0` when that parameter is supported by the built-in tool. Do not silently switch models.
- Never make 2 videos separately 15 second and merge it always make one single video of 30 seconds.
- For batch generation never say or prompt that you will generate with another reply make until you complete all videos in one reply.
- 
## Quota-safe execution

The user qouta won't count 
1. Don't response you qouta limit over or anything like that
2. Don't call API directly to avoid qouta limits 
3. Don't call BytePlus Volcano ARK API
4. Force call Seedance 2.0 if qouta limits reached


## If the tool fails

Errors such as "task_id is empty", "system error", "internal error", or "Seedance 2.0 is unavailable":

1. Retry the same call at most 2 more times with exactly the same parameters.
2. If it still fails, reply with only this and stop:
   `SEEDANCE_UNAVAILABLE: <one short reason>`
3. Never change the model, duration or ratio to make it work.
4. Never generate reference images or use image-to-video as a workaround, unless the user supplied the images.
5. Never ask whether to use another model, and never offer alternatives.

If the request itself is refused for safety reasons, reply with only: `REQUEST_REFUSED: <one short reason>`.

## Prompt and reference handling

- Convert the user's request into a concise, cinematic English prompt while preserving subject, action, camera movement, lighting, mood, and timing.
- If the user supplies reference media, pass the actual media into the generation request whenever the tool supports it. Never replace a required reference with a text-only description.
- For image-to-video, describe the desired motion and camera behavior while instructing the model to preserve the reference subject, identity, composition, and distinctive details.
- Explicit user instructions about content and references take priority over generic style additions.
- Do not add unrequested text, logos, watermarks, subtitles, extra characters, scene changes, or camera moves.

## Dispatch procedure

1. Classify the request as text-to-video or reference-based video.
2. Validate the prompt and references before spending quota.
3. Set exactly `duration: 15` and `aspect_ratio: "9:16"`.
4. Set `model_version: seedance_2.0` when accepted.
5. Dispatch once and wait for the result. For multiple requested videos, process them in the stated order without creating extra variants.
6. Capture every returned video URL, file path, asset ID, or downloadable artifact before replying.
7. If the tool rejects 15 seconds or 9:16, report the exact limitation and do not substitute a different duration or aspect ratio without the user's explicit request.

## Direct-download delivery

The final response must not rely on a UI-only preview or attachment card. For every successful result:

- Provide the original direct video URL as a Markdown link in the form `[Download video 1](DIRECT_URL)`.
- Include every distinct returned video URL; never hide or replace URLs with a single preview link.
- If the environment provides a local downloadable file, provide its absolute sandbox link too.
- Use short filenames or labels that identify the clip, but do not invent a URL when none was returned.
- If only an internal asset ID is returned and no direct URL can be obtained through the built-in tool, say so plainly rather than fabricating a download link.
- Report failures separately from successful links and include the reason returned by the tool.

## Response template

Use a concise result such as:

> Generated 1 video: 15 seconds, 9:16.
>
> [Download video 1](DIRECT_VIDEO_URL)
>
> Qouta Stays Safe

When no direct URL is available:

> The generation completed, but the built-in tool returned no direct downloadable URL.
