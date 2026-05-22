# Seedream 4.5 cURL Quickstart

## What this example shows

This example shows how to submit a Seedream 4.5 image task through APIDot, store the returned `task_id`, and poll the shared status endpoint for completion.

It includes the documented request shapes:

- `seedream-4.5` for image generation.
- `seedream-4.5-edit` for edits with reference image URLs.
- A custom-size `seedream-4.5` request using width and height.

## When to use it

Use this example when you need a server-side cURL quickstart for campaign images, product visuals, controlled image edits, or custom-size image generation.

For production apps, store the APIDot task ID and connect it to your own application job record.

## Requirements

- An APIDot account.
- An APIDot API key stored server-side.
- `curl` installed locally.
- A public source image URL when using `seedream-4.5-edit`.

## Environment variables

Use placeholders only. Do not commit real credentials.

```env
APIDOT_API_KEY=YOUR_API_KEY_HERE
```

## How to run

These examples use Bash line continuation. On Windows, run them in Git Bash/WSL or adapt them to `curl.exe` PowerShell syntax.

Add `callback_url` only when you have a real webhook receiver. See the [webhooks docs](https://apidot.ai/docs/webhooks) for the production callback flow.

```bash
export APIDOT_API_KEY="YOUR_API_KEY_HERE"

curl --fail-with-body --request POST \
  --url https://api.apidot.ai/api/generate/submit \
  --header "Authorization: Bearer $APIDOT_API_KEY" \
  --header "Content-Type: application/json" \
  --data '{
    "model": "seedream-4.5",
    "input": {
      "prompt": "A premium product campaign image of a matte ceramic mug on a warm gray studio background, precise typography on the mug reading APIDot, soft window light, balanced shadows, 4K commercial photography style.",
      "size": "16:9",
      "n": 1
    }
  }'
```

Store the returned `data.task_id`, then poll status:

```bash
curl --fail-with-body --request GET \
  --url https://api.apidot.ai/api/generate/status/task-unified-example \
  --header "Authorization: Bearer $APIDOT_API_KEY"
```

Use `seedream-4.5-edit` when the request depends on a source image:

```json
{
  "model": "seedream-4.5-edit",
  "callback_url": "https://example.com/api/apidot/webhook",
  "input": {
    "prompt": "Keep the product shape and label from the source image. Replace the background with a clean winter studio setup, preserve realistic shadows, and make the final image suitable for an ecommerce hero asset.",
    "image_urls": [
      "https://example.com/source-image.png"
    ],
    "size": "2K",
    "n": 1
  }
}
```

Use a custom size when your application needs an explicit canvas:

```json
{
  "model": "seedream-4.5",
  "callback_url": "https://example.com/api/apidot/webhook",
  "input": {
    "prompt": "A fashion editorial portrait in a studio with clean typography and refined lighting.",
    "size": {
      "width": 2304,
      "height": 3072
    },
    "n": 1
  }
}
```

## Expected response

Submit response:

```json
{
  "code": 200,
  "data": {
    "task_id": "task-unified-example",
    "status": "not_started",
    "created_time": "2026-04-19T21:19:42"
  }
}
```

Shortened status response:

```json
{
  "code": 200,
  "data": {
    "task_id": "task-unified-example",
    "status": "finished",
    "output": {
      "files": [
        {
          "file_url": "https://example.com/generated-image.png",
          "file_type": "image"
        }
      ]
    },
    "error_message": null
  }
}
```

## Production notes

- Store `task_id`, selected model, and request variant together.
- Validate custom sizes in your application before submitting them.
- Keep source image URLs reachable long enough for processing.
- Use webhooks for production workflows where users may not keep a browser session open.
- Never log API keys or private reference image URLs.
- Avoid retrying invalid custom-size or edit payloads unchanged.

## Common mistakes

- Using `seedream-4-5` instead of the documented model string `seedream-4.5` in the request payload.
- Using edit mode without `input.image_urls`.
- Sending a custom-size object where your application expected a ratio string.
- Polling too aggressively.
- Dropping the task record before the image reaches `finished` or `failed`.

## Related links

- Website: https://apidot.ai
- Docs: https://apidot.ai/docs
- Seedream 4.5 docs: https://apidot.ai/docs/seedream-4-5
- Image models: https://apidot.ai/models/image
- Quickstart: https://apidot.ai/docs/quickstart
- Webhooks: https://apidot.ai/docs/webhooks
- GitHub: https://github.com/APIDotAI
- Examples: https://github.com/APIDotAI/apidot-examples
- Related landing page: https://apidot.ai/models/seedream-4-5

