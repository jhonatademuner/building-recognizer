# Building Recognizer

Image comparison API that uses histogram analysis, CNN features (InceptionV3), and MSE to determine similarity between two images.

## Requirements

- Python 3.12+
- pip

## Setup

```bash
python3.12 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
```

## Running

```bash
uvicorn app.main:app --host 0.0.0.0 --port 8000
```

> First startup takes ~25 seconds while TensorFlow loads the InceptionV3 model.

## API

### Health Check

```
GET /api/health
```

Returns `{"status": "ok"}`.

### Compare Images

```
POST /api/image/compare-images
```

Accepts two images as multipart form data and returns similarity scores.

**Request:**

```bash
curl -X POST http://localhost:8000/api/image/compare-images \
  -F "image1=@photo1.png" \
  -F "image2=@photo2.png"
```

**Response:**

```json
{
  "histogram_similarity": 97.49,
  "cnn_similarity": 72.95,
  "mse_similarity": 75.0,
  "final_similarity": 80.72
}
```

**Similarity metrics:**

| Metric | Weight | Description |
|--------|--------|-------------|
| `histogram_similarity` | 30% | Color distribution comparison |
| `cnn_similarity` | 50% | Deep feature similarity via InceptionV3 |
| `mse_similarity` | 20% | Pixel-level mean squared error (inverted) |
| `final_similarity` | — | Weighted combination (0–100) |

## Project Structure

```
app/
├── main.py              # FastAPI app setup and health endpoint
├── routers/
│   └── image.py         # Image comparison route
└── services/
    └── image_service.py # Image processing and comparison logic
```
