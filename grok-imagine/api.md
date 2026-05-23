#### Inference API

# Videos

## POST /v1/videos/generations

Generate a video from a text prompt and optionally an image.
This is an asynchronous operation that returns a request\_id for polling.

### Request Body

* `aspect_ratio` ("1:1" | "16:9" | "9:16" | "4:3" | "3:4" | "3:2" | "2:3")

* `duration` (integer | null) — Video duration in seconds. Range: \[1, 15]. Default: 8.
  Also accepts \`seconds\` for OpenAI API compatibility.
  Accepts both number (8) and string ("8") values.

* `image` (object)

  * `url` (string) — Public URL or base64-encoded data URL of the image (JPEG, PNG, or WebP).
    Also accepts \`image\_url\` for compatibility.
    Required when \`file\_id\` is not set.

* `prompt` (string) — Prompt for video generation. Required for text-to-video (T2V) and
  reference-to-video (R2V). Optional for image-to-video (I2V) — when
  omitted, the model generates a video from the image alone.

* `reference_images` (array\<object>) — Optional reference images for reference-to-video (R2V) generation.
  When provided generates video using these images
  as style/content references.

  * `url` (string) — Public URL or base64-encoded data URL of the image (JPEG, PNG, or WebP).
    Also accepts \`image\_url\` for compatibility.
    Required when \`file\_id\` is not set.

* `resolution` ("480p" | "720p" | "1080p")

### Code Examples

```bash
curl -s https://api.x.ai/v1/videos/generations \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $XAI_API_KEY" \
  -d '{
    "model": "grok-imagine-video",
    "prompt": "A serene lake at sunrise with mist rolling over the water"
  }'
```
