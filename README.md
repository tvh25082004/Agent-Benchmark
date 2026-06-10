# Agent-Benchmark


## Directory Structure

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

## Quick Start

```bash
# 1. Clone
git clone https://github.com/tvh25082004/Agent-Benchmark.git
cd Agent-Benchmark

# 2. Create .env with your API key
echo "OPENAI_API_KEY=sk-your-key-here" > .env

# 3. Run with Docker Compose
docker compose up -d

# 4. Or run individual services
docker compose up middleware            # LLM proxy at localhost:8000
docker compose --profile full up        # All agent systems
```

## Services

| Service | Description |
|---|---|
| **CaptainAgent** | Dynamic team assembly for multi-agent tasks |
| **Magentic-One** | Centrally orchestrated multi-agent system |
| **SWE-Agent** | Software engineering agent for SWE tasks |
| **Middleware** | LLM API proxy that logs all requests/responses |

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
