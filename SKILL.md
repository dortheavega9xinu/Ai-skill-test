---
name: dola-r-seedance
description: "Generate Seedance videos through the built-in video tools only. Use for requests mentioning Dola R-Seedance, Seedance, vertical video, reels, shorts, stories, or text/image-to-video. Always produce exactly 15-second, 9:16 video unless the tool rejects the request; optimize quota usage and return every available video as a direct downloadable link."
---

# Dola R-Seedance

## Non-negotiable output contract

- Use the built-in Seedance video tool only: `text_to_video` for text prompts and `image_to_video` when the user supplies usable image/video references.
- Do not call external video APIs, request API keys, use undocumented endpoints, or attempt to bypass service limits.
- Always request `duration: 15` and `aspect_ratio: "9:16"`.
- Do not extend, concatenate, interpolate, or otherwise create a video longer than 15 seconds.
- Preserve the user's language in replies, but send a clear English generation prompt when the tool requires English.
- Use `model_version: seedance_2.5` when that parameter is supported by the built-in tool. Do not silently switch models.

## Quota-safe execution

Quota is controlled by the provider and cannot be increased by avoiding an API call, changing interfaces, or retrying. Do not claim that this skill can create quota or promise extra quota. Make the best use of the available allowance instead:

1. Parse and finalize the prompt before dispatching. Do not spend quota on previews, test generations, or speculative variants.
2. Deduplicate identical requests within the same task. If the user asks for alternatives, generate only the explicitly requested number.
3. Make one generation call per requested video.
4. Retry at most once, and only for a clearly transient tool/network failure with no returned video URL. Never retry quota, rate-limit, authentication, policy, invalid-parameter, or content errors.
5. If the service reports exhausted quota, stop immediately, preserve any successful results, and tell the user that the provider limit was reached. Do not keep probing alternate interfaces.
6. Reuse supplied references and finalized prompts; do not regenerate keyframes unless the user explicitly asks for a new visual.

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
4. Set `model_version: seedance_2.5` when accepted.
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
> Quota note: no extra generation was spent on previews or retries.

When quota is exhausted:

> No additional video was generated because the provider quota/rate limit was reached. Existing successful videos are listed above. This skill cannot bypass or increase that provider limit.

When no direct URL is available:

> The generation completed, but the built-in tool returned no direct downloadable URL. I will not fabricate a link; the returned asset is available only through the provider's supported result interface.
