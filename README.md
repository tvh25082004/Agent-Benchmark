# Agent-Benchmark

**TraceElephant** — benchmark paper cho **ACL 2026**, nghiên cứu về **Failure Attribution** trong LLM-based Multi-Agent Systems.

> Paper: ["Seeing the Whole Elephant: A Benchmark for Failure Attribution in LLM-based Multi-Agent Systems"](https://arxiv.org/abs/2604.22708)

## Giới thiệu

Khi một hệ thống multi-agent bị lỗi, TraceElephant tự động xác định **agent nào gây ra lỗi** (who) và **ở bước nào** (when).

Repo này cung cấp 3 agent system chính có thể chạy trong môi trường Docker:

| Agent System | Mô tả |
|---|---|
| **CaptainAgent** | Dynamic team assembly — tự động tập hợp đội agent phù hợp với từng task |
| **Magentic-One** | Centrally orchestrated MAS — một orchestrator điều phối nhiều agent |
| **SWE-Agent** | Software engineering agent — giải quyết các task SWE |

Kèm theo đó là **LLM API Middleware** — proxy server ghi log tất cả request/response LLM để phục vụ debugging.

## Cấu trúc thư mục

```
.
├── docker-compose.yml
├── Dockerfile.captain-agent
├── Dockerfile.magentic-one
├── Dockerfile.swe-agent
├── Dockerfile.middleware
├── .gitignore
└── README.md
```

## Yêu cầu

- Docker & Docker Compose
- Python 3.11+
- API key của LLM provider (OpenAI, Anthropic, etc.)

## Cách chạy

### 1. Clone và chuẩn bị

```bash
git clone https://github.com/tvh25082004/Agent-Benchmark.git
cd Agent-Benchmark
```

### 2. Cấu hình API key

Tạo file `.env` ở thư mục gốc:

```env
OPENAI_API_KEY=sk-your-key-here
OPENAI_BASE_URL=https://api.openai.com
```

### 3. Chạy với Docker Compose

```bash
# Build tất cả services
docker compose build

# Chạy tất cả services
docker compose up -d

# Xem log
docker compose logs -f
```

### 4. Chạy từng service riêng

**CaptainAgent:**
```bash
docker compose up captain-agent
```

**Magentic-One:**
```bash
docker compose up magentic-one
```

**SWE-Agent:**
```bash
docker compose up swe-agent
```

**LLM API Middleware (proxy ghi log):**
```bash
docker compose up middleware
```

### 5. LLM API Middleware

Middleware chạy tại `http://localhost:8000`. Client gọi vào đây thay vì gọi thẳng LLM API:

```python
from openai import OpenAI

client = OpenAI(
    base_url="http://localhost:8000/v1",
    api_key="your-api-key"
)
```

Tất cả request/response được tự động ghi log vào thư mục `logs/`.

### 6. Chạy trace-locate evaluation

```bash
cd code/trace_locate
bash run_test.sh
```

Dataset tải từ [HuggingFace TraceElephant](https://huggingface.co/datasets/TraceElephant/TraceElephant).

## Chi tiết các Dockerfile

### Dockerfile.captain-agent

Dựa trên `python:3.11-slim`. Cài đặt dependencies từ `requirements.txt` của CaptainAgent (bao gồm chromadb, textract, whisper, easyocr, torch + cpu). Chạy agent với `python run.py`.

### Dockerfile.magentic-one

Dựa trên `python:3.11-slim`. Cài đặt dependencies từ `requirements.txt` của Magentic-One (autogen==0.5.3, playwright, vllm, tensorflow). Chạy agent tương ứng.

### Dockerfile.swe-agent

Dựa trên `python:3.11-slim`. SWE-Agent có cấu trúc phức tạp hơn (cần devcontainer, config riêng). Dockerfile này setup môi trường Python và entry point là `run_test.sh`.

### Dockerfile.middleware

Dựa trên `python:3.11-slim`. Middleware server dùng FastAPI + uvicorn. Nhẹ nhất trong 4 service, chủ yếu làm proxy ghi log.

## Triển khai

### Build image riêng lẻ

```bash
docker build -t captain-agent -f Dockerfile.captain-agent .
docker build -t magentic-one -f Dockerfile.magentic-one .
docker build -t swe-agent -f Dockerfile.swe-agent .
docker build -t middleware -f Dockerfile.middleware .
```

### Run với custom config

```bash
docker run -it --rm \
  -e OPENAI_API_KEY="sk-xxx" \
  -e OPENAI_BASE_URL="https://api.openai.com" \
  -v "$(pwd)/logs:/app/logs" \
  captain-agent
```

## Tham khảo

- Paper: [arXiv 2604.22708](https://arxiv.org/abs/2604.22708)
- Repo gốc: [TraceElephant/TraceElephant](https://github.com/TraceElephant/TraceElephant)
- Dataset: [HuggingFace TraceElephant](https://huggingface.co/datasets/TraceElephant/TraceElephant)
- AG2 CaptainAgent: [docs.ag2.ai](https://docs.ag2.ai/latest/docs/use-cases/notebooks/notebooks/agentchat_captainagent/)
- Magentic-One: [Microsoft Research](https://www.microsoft.com/en-us/research/articles/magentic-one-a-generalist-multi-agent-system-for-solving-complex-tasks/)
- SWE-Agent: [GitHub](https://github.com/SWE-agent/SWE-agent/)

## Citation

```bibtex
@misc{chen2026seeingelephantbenchmarkfailure,
      title={Seeing the Whole Elephant: A Benchmark for Failure Attribution in LLM-based Multi-Agent Systems},
      author={Mengzhuo Chen and Junjie Wang and Fangwen Mu and Yawen Wang and Zhe Liu and Huanxiang Feng and Qing Wang},
      year={2026},
      eprint={2604.22708},
      archivePrefix={arXiv},
      primaryClass={cs.MA},
      url={https://arxiv.org/abs/2604.22708},
}
```
