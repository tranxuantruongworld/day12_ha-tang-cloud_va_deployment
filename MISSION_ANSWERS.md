#  Delivery Checklist — Day 12 Lab Submission

> **Student Name:** Trần Xuân Trường
> **Student ID:** 2A202600321
> **Date:** 17/04/2026

---

##  Submission Requirements

### 1. Mission Answers (40 points)

```markdown
# Day 12 Lab - Mission Answers

## Part 1: Localhost vs Production

### Exercise 1.1: Anti-patterns found
1. API key hardcode trong code
2. Print thay vì proper logging
3. Không có config management
4. Không có health check endpoint
5. Port cố định — không đọc từ environment
...

### Exercise 1.3: Comparison table
| Feature | Basic | Advanced | Why important? |
|---------|-------|----------|---------------------|
| Config | Hardcode | Env vars | Bảo mật dữ liệu, dễ dàng thay đổi khi chuyển sang nhiều môi trường khác nhau |
| Health check |  |  | Không kiểm tra nhanh chóng hệ thống đang hoạt động hay bị crack |
| Logging | print() | JSON | Lưu lại lịch sử lỗi, dễ dàng truy xuất lỗi |
| Shutdown | Đột ngột | Graceful | Bảo toàn dữ liệu, tránh treo hệ thống thời gian dài  |
...

## Part 2: Docker

### Exercise 2.1: Dockerfile questions
1. Base image: [Your answer]
2. Working directory: [Your answer]
...

### Exercise 2.3: Image size comparison
- Develop: [X] MB
- Production: [Y] MB
- Difference: [Z]%

## Part 3: Cloud Deployment

### Exercise 3.1: Railway deployment
- URL: https://cloud-deployment-production.up.railway.app/
- Screenshot: ![railway](screenshot/image.png)

## Part 4: API Security

### Exercise 4.1-4.3: Test results
#### 4.1
Không có key
curl.exe http://localhost:8000/ask -X POST -H "Content-Type: application/json"  -d "'{\`"question\`": \`"Hello\`"}'"
{"detail":"Missing API key. Include header: X-API-Key: <your-key>"}

Có key
curl.exe http://localhost:8000/ask -X POST `
  -H "X-API-Key: demo-key-change-in-production" `
  -H "Content-Type: application/json" `
  -d "{\`"question\`": \`"Hello\`"}"
{"question":"Hello","answer":"Agent đang hoạt động tốt! (mock response) Hỏi thêm câu hỏi đi nhé."}
#### 4.2
2. Lấy token:
{
  "access_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiJ0ZWFjaGVyIiwicm9sZSI6ImFkbWluIiwiaWF0IjoxNzc2NDk4NjQ2LCJleHAiOjE3NzY1MDIyNDZ9.JmkO1uxOxjvorMpB3sBpVPAyirSFqTdhwFY5h5QJrq0",
  "token_type": "bearer",
  "expires_in_minutes": 60,
  "hint": "Include in header: Authorization: Bearer eyJhbGciOiJIUzI1NiIs..."
}
3. Dùng token để gọi API:
{
  "question": "hello",
  "answer": "Agent đang hoạt động tốt! (mock response) Hỏi thêm câu hỏi đi nhé.",
  "usage": {
    "requests_remaining": 99,
    "budget_remaining_usd": 0.000016
  }
}
### Exercise 4.3: Rate limiting
- Algorithm nào được dùng? (Token bucket? Sliding window?)
-> Sử dụng Sliding window
- Limit là bao nhiêu requests/minute?
-> Với User: 10 req/phút
-> Với Admin: 100 req/phút
- Làm sao bypass limit cho admin?
-> Chuyển sang rate limit không giới hạn, thiết lập whitelist IP đặc quyền

###  Exercise 4.4: Cost guard
```python
import redis
from datetime import datetime

r = redis.Redis()

def check_budget(user_id: str, estimated_cost: float) -> bool:
    month_key = datetime.now().strftime("%Y-%m")
    key = f"budget:{user_id}:{month_key}"
    
    current = float(r.get(key) or 0)
    if current + estimated_cost > 10:
        return False
    
    r.incrbyfloat(key, estimated_cost)
    r.expire(key, 32 * 24 * 3600)  # 32 days
    return True
```
## Part 5: Scaling & Reliability

### Exercise 5.1-5.5: Implementation notes
[Your explanations and test results]
```

---

### 2. Full Source Code - Lab 06 Complete (60 points)

Your final production-ready agent with all files:

```
your-repo/
├── app/
│   ├── main.py              # Main application
│   ├── config.py            # Configuration
│   ├── auth.py              # Authentication
│   ├── rate_limiter.py      # Rate limiting
│   └── cost_guard.py        # Cost protection
├── utils/
│   └── mock_llm.py          # Mock LLM (provided)
├── Dockerfile               # Multi-stage build
├── docker-compose.yml       # Full stack
├── requirements.txt         # Dependencies
├── .env.example             # Environment template
├── .dockerignore            # Docker ignore
├── railway.toml             # Railway config (or render.yaml)
└── README.md                # Setup instructions
```

**Requirements:**
-  All code runs without errors
-  Multi-stage Dockerfile (image < 500 MB)
-  API key authentication
-  Rate limiting (10 req/min)
-  Cost guard ($10/month)
-  Health + readiness checks
-  Graceful shutdown
-  Stateless design (Redis)
-  No hardcoded secrets

---

### 3. Service Domain Link

Create a file `DEPLOYMENT.md` with your deployed service information:

```markdown
# Deployment Information

## Public URL
https://your-agent.railway.app

## Platform
Railway / Render / Cloud Run

## Test Commands

### Health Check
```bash
curl https://your-agent.railway.app/health
# Expected: {"status": "ok"}
```

### API Test (with authentication)
```bash
curl -X POST https://your-agent.railway.app/ask \
  -H "X-API-Key: YOUR_KEY" \
  -H "Content-Type: application/json" \
  -d '{"user_id": "test", "question": "Hello"}'
```

## Environment Variables Set
- PORT
- REDIS_URL
- AGENT_API_KEY
- LOG_LEVEL

## Screenshots
- [Deployment dashboard](screenshots/dashboard.png)
- [Service running](screenshots/running.png)
- [Test results](screenshots/test.png)
```

##  Pre-Submission Checklist

- [ ] Repository is public (or instructor has access)
- [ ] `MISSION_ANSWERS.md` completed with all exercises
- [ ] `DEPLOYMENT.md` has working public URL
- [ ] All source code in `app/` directory
- [ ] `README.md` has clear setup instructions
- [ ] No `.env` file committed (only `.env.example`)
- [ ] No hardcoded secrets in code
- [ ] Public URL is accessible and working
- [ ] Screenshots included in `screenshots/` folder
- [ ] Repository has clear commit history

---

##  Self-Test

Before submitting, verify your deployment:

```bash
# 1. Health check
curl https://your-app.railway.app/health

# 2. Authentication required
curl https://your-app.railway.app/ask
# Should return 401

# 3. With API key works
curl -H "X-API-Key: YOUR_KEY" https://your-app.railway.app/ask \
  -X POST -d '{"user_id":"test","question":"Hello"}'
# Should return 200

# 4. Rate limiting
for i in {1..15}; do 
  curl -H "X-API-Key: YOUR_KEY" https://your-app.railway.app/ask \
    -X POST -d '{"user_id":"test","question":"test"}'; 
done
# Should eventually return 429
```

---

##  Submission

**Submit your GitHub repository URL:**

```
https://github.com/your-username/day12-agent-deployment
```

**Deadline:** 17/4/2026

---

##  Quick Tips

1.  Test your public URL from a different device
2.  Make sure repository is public or instructor has access
3.  Include screenshots of working deployment
4.  Write clear commit messages
5.  Test all commands in DEPLOYMENT.md work
6.  No secrets in code or commit history

---

##  Need Help?

- Check [TROUBLESHOOTING.md](TROUBLESHOOTING.md)
- Review [CODE_LAB.md](CODE_LAB.md)
- Ask in office hours
- Post in discussion forum

---

**Good luck! **
