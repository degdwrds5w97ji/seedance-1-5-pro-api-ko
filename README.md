# Seedance 1.5 Pro API (`seedance-1.5-pro`)

<!-- conv-kit:v1 -->

<p align="center">
  <img src="assets/badges/price.svg" alt="observed unit price"> <img src="assets/badges/billing.svg" alt="billing model"> <img src="assets/badges/compat.svg" alt="OpenAI-compatible endpoint">
</p>

<p align="center">
  <img src="assets/01-product-ad-ceramic-mug-thumb.jpg" width="820" alt="Sample video frame generated through APIMart">
</p>

> **from $0.0104 per second** on the APIMart route (billed by resolution tier) — `https://api.apimart.ai/v1` OpenAI 호환 엔드포인트 하나면 되고, 월 구독 플랜은 필요 없습니다. *(2026-10-12 기준)*

**[API 키](https://go.apimart.ai/k-a8bc52)** · **[가격](https://go.apimart.ai/k-8dbc9e)** · **[모델 페이지](https://go.apimart.ai/k-34db3c)** · [⚡ 60초 퀵스타트](#quickstart)

**팀들이 APIMart를 통해 Seedance 1.5 Pro(`seedance-1.5-pro`)을 쓰는 이유**

- **키 하나로 카탈로그 전체를.** 동일한 `https://api.apimart.ai/v1` base URL과 `Authorization` 헤더로 Seedance 1.5 Pro(`seedance-1.5-pro`)과 이미지·비디오·언어 모델 300+ 개에 접근할 수 있습니다. 바꾸는 건 `model` 필드뿐이고 클라이언트는 그대로입니다.
- **최소 $1, 종량제.** 구독도, 사전에 규모를 정해야 하는 선불 플랜도 없습니다. $1부터 충전해 호출한 만큼만 쓰면 됩니다.
- **과금 내역은 응답으로 돌아옵니다.** 완료된 모든 작업은 청구 금액(`cost` / `credits_cost`)을 보고하고, `failed`로 표시된 작업은 환불됩니다.
- **비동기 설계.** 제출하고 `task_id`를 받아 `GET /v1/tasks/{id}`를 폴링하면 끝입니다. 배치와 재시도는 별도 통합이 아니라 평범한 큐 작업입니다.

<!-- /conv-kit:v1 -->

**다른 이름:** 시댄스

Seedance 1.5 Pro is ByteDance's AI video model with smoother motion, 4K quality, stability and precise control.

## Model id and routes

| Route | `model` value | Billing | Notes |
| --- | --- | --- | --- |
| Text-to-video / image-to-video | `seedance-1.5-pro` | per generated second, by resolution | see the [model page](https://go.apimart.ai/k-34db3c) for the exact tiers |

Endpoint: `POST https://api.apimart.ai/v1/videos/generations`, then poll `GET /v1/tasks/{task_id}`.

## Pricing

<!-- pricing:model:start -->
| Resolution | List price | Effective price |
| --- | --- | --- |
| per generated second | — | see the live pricing page |


<!-- pricing:model:end -->

Prices are a snapshot; the [pricing page](https://go.apimart.ai/k-8dbc9e) and [`data/model.json`](data/model.json) are refreshed by CI, and a completed task reports the exact amount in its `cost` field.

## Request parameters

| Field | Type | Default | Notes |
| --- | --- | --- | --- |
| `model` | string | required | the model id shown in the route table above |
| `prompt` | string | required | the shot description; camera and subject motion belong here |
| `duration` | integer | route default | clip length in seconds — billing is per generated second |
| `resolution` | string | route default | 480P / 720P / 1080P or the route’s own tiers |
| `size` / `aspect_ratio` | string | `16:9` | output framing |
| `image_urls` | string[] | — | first frame, first + last frame, or style references, where supported |
| `seed` | integer | — | fix the seed to reproduce a take |

The table lists the fields accepted by the APIMart `videos/generations` endpoint. Check the [model page](https://go.apimart.ai/k-34db3c) for anything this model adds on top.

## Quickstart

```bash
curl --request POST --url https://api.apimart.ai/v1/videos/generations \
  --header "Authorization: Bearer $APIMART_API_KEY" --header 'Content-Type: application/json' \
  -d '{"model":"seedance-1.5-pro","prompt":"A bamboo forest at sunrise, slow push-in","duration":5,"resolution":"720P","size":"16:9"}'
```

```python
import os, time, requests

BASE = "https://api.apimart.ai/v1"
HEADERS = {"Authorization": f"Bearer {os.environ['APIMART_API_KEY']}", "Content-Type": "application/json"}

created = requests.post(f"{BASE}/videos/generations", headers=HEADERS, timeout=60, json={
    "model": "seedance-1.5-pro", "prompt": "A bamboo forest at sunrise, slow push-in", "duration": 5,
}).json()
task_id = created["data"]["id"]

while True:
    task = requests.get(f"{BASE}/tasks/{task_id}", headers=HEADERS, timeout=60).json()["data"]
    if task["status"] in ("completed", "failed"):
        break
    time.sleep(5)
print(task.get("cost"), task["result"]["videos"][0]["url"])
```

Runnable versions: [`examples/`](examples). The task lifecycle is `pending → processing → completed | failed`.

## FAQ

**Seedance 1.5 Pro 모델 id는 무엇인가요?**

`seedance-1.5-pro`입니다. 위의 라우트 표를 참고하세요.

**과금 방식은 어떻게 되나요?**

**from $0.0104 per second** on the APIMart route (billed by resolution tier). 완료된 작업은 `cost` 필드에 정확한 금액을 보고하고, 실패한 작업은 환불됩니다.

**How do I get a key?**

Use the [Get an API key](https://go.apimart.ai/k-a8bc52) link — $1 minimum top-up, no plan required.

## Attributed links (how this repository is measured)

| Purpose | Attributed link | Target |
| --- | --- | --- |
| Model page | <https://go.apimart.ai/k-34db3c> | `apimart.ai/ko/model/doubao-seedance-1-5-pro` |
| Live pricing | <https://go.apimart.ai/k-8dbc9e> | `apimart.ai/ko/pricing` |
| Get an API key | <https://go.apimart.ai/k-a8bc52> | `apimart.ai/ko/keys` |

Outbound APIMart links are minted through the promo link API; hand-made tracking parameters are rejected by
`tools/check_links.py` in CI.

## Disclosure

Seedance 1.5 Pro is a third-party model served through APIMart; this repository documents how to call it and publishes
model ids and prices, and does not claim official status. Model names, prices and documentation belong to their
respective owners.

## License

MIT — see [LICENSE](LICENSE).
