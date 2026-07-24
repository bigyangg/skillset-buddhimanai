---
name: load-testing
description: Design and run load, stress, and performance tests for APIs and web services. Use when a user says "load test", "stress test", "performance test", "k6", "Locust", "API throughput", "p99 latency", "requests per second", "test under load", "find the breaking point", "capacity planning", or "benchmark my API".
---

# Load Testing

## What This Does

Design load tests to validate API throughput, find breaking points, and measure latency percentiles under realistic traffic. Covers k6 (JavaScript, default) and Locust (Python), test scenario design, metrics interpretation, and CI integration.

## Non-Negotiables

- **Never run load tests against production without prior approval and off-peak scheduling.**
- **Warm up before ramping.** Cold runs produce misleading results (JIT compilation, connection pool warm-up).
- **Measure p95/p99, not average.** Averages hide tail latency — the slowest 1% of users experience the worst.
- **Establish a baseline before optimizing.** You can't know if you improved without a pre-optimization measurement.
- **Isolate the system under test.** Other services and databases should be in a known, stable state.

## Test Scenario Types

| Type | Goal | Traffic Pattern |
|------|------|----------------|
| **Smoke test** | Verify system works at minimal load | 1–5 VUs, 1–2 min |
| **Load test** | Validate expected production traffic | Ramp to expected peak, hold 15–30 min |
| **Stress test** | Find the breaking point | Ramp beyond expected peak until failure |
| **Spike test** | Simulate sudden traffic bursts | Instant ramp to 10x normal, quick drop |
| **Soak/endurance** | Detect memory leaks over time | Expected load, 4–8 hours |

## k6 (Primary Tool)

### Installation

```bash
# macOS
brew install k6

# Windows
winget install k6 --source winget

# Docker
docker run -i grafana/k6 run - <script.js
```

### Basic Load Test

```javascript
// scripts/load-test.js
import http from 'k6/http'
import { sleep, check, group } from 'k6'
import { Rate, Trend } from 'k6/metrics'

// Custom metrics
const errorRate    = new Rate('error_rate')
const loginTrend   = new Trend('login_duration_ms', true)

export const options = {
  // Test stages: gradually ramp up, hold, ramp down
  stages: [
    { duration: '30s', target: 10  },  // Warm-up
    { duration: '2m',  target: 50  },  // Ramp to expected load
    { duration: '5m',  target: 50  },  // Hold at peak
    { duration: '1m',  target: 100 },  // Stress: push beyond peak
    { duration: '30s', target: 0   },  // Cool down
  ],

  // Pass/fail thresholds
  thresholds: {
    'http_req_duration':          ['p(95)<500'],   // 95% of requests under 500ms
    'http_req_duration{type:api}': ['p(99)<1000'], // API calls under 1s at p99
    'http_req_failed':            ['rate<0.01'],   // Fewer than 1% errors
    'error_rate':                 ['rate<0.01'],
  },
}

const BASE_URL = __ENV.BASE_URL || 'http://localhost:3000'

// Setup: runs once before test (not counted in metrics)
export function setup() {
  // Create test user or fetch auth token
  const res = http.post(`${BASE_URL}/api/auth/login`, JSON.stringify({
    email:    'loadtest@example.com',
    password: 'testpassword123',
  }), { headers: { 'Content-Type': 'application/json' } })

  return { token: res.json('token') }   // passed to default function
}

// Main test function — runs once per VU per iteration
export default function (data) {
  const headers = {
    Authorization:  `Bearer ${data.token}`,
    'Content-Type': 'application/json',
  }

  group('User Dashboard', () => {
    const start = Date.now()
    const res   = http.get(`${BASE_URL}/api/dashboard`, { headers, tags: { type: 'api' } })

    loginTrend.add(Date.now() - start)

    const ok = check(res, {
      'status is 200':              (r) => r.status === 200,
      'response has data':          (r) => r.json('data') !== null,
      'response time < 500ms':      (r) => r.timings.duration < 500,
    })

    errorRate.add(!ok)
  })

  group('Post List', () => {
    const res = http.get(`${BASE_URL}/api/posts?page=1&limit=20`, { headers, tags: { type: 'api' } })

    check(res, {
      'status is 200': (r) => r.status === 200,
      'has posts array': (r) => Array.isArray(r.json('posts')),
    })
  })

  // Realistic user think time (1–3 seconds between actions)
  sleep(Math.random() * 2 + 1)
}

// Teardown: runs once after test (not counted)
export function teardown(data) {
  // Clean up test data if needed
}
```

### Running k6

```bash
# Basic run
k6 run scripts/load-test.js

# With env vars
k6 run -e BASE_URL=https://staging.example.com scripts/load-test.js

# Output to JSON for analysis
k6 run --out json=results.json scripts/load-test.js

# HTML report
k6 run --out dashboard scripts/load-test.js   # k6 Cloud or open-source dashboard

# Specific scenario
k6 run --vus 10 --duration 30s scripts/smoke-test.js
```

### Smoke Test (Quick Sanity Check)

```javascript
// scripts/smoke-test.js
import http from 'k6/http'
import { check } from 'k6'

export const options = {
  vus:      1,
  duration: '30s',
  thresholds: {
    'http_req_failed':   ['rate<0.01'],
    'http_req_duration': ['p(95)<1000'],
  },
}

export default function () {
  const res = http.get('http://localhost:3000/api/health')
  check(res, { 'health check passes': (r) => r.status === 200 })
}
```

## Locust (Python Alternative)

```python
# locustfile.py
from locust import HttpUser, task, between
import json

class AppUser(HttpUser):
    wait_time = between(1, 3)   # Think time between tasks (seconds)
    token: str = ""

    def on_start(self):
        """Called once per user on initialization — authentication"""
        response = self.client.post('/api/auth/login', json={
            'email':    'loadtest@example.com',
            'password': 'testpassword123',
        })
        if response.status_code == 200:
            self.token = response.json()['token']
        self.headers = {'Authorization': f'Bearer {self.token}'}

    @task(3)     # Weight: 3x more likely to hit than @task(1)
    def view_dashboard(self):
        with self.client.get(
            '/api/dashboard',
            headers=self.headers,
            name='/api/dashboard',   # Group URL variants
            catch_response=True
        ) as response:
            if response.status_code == 200:
                response.success()
            else:
                response.failure(f'Expected 200, got {response.status_code}')

    @task(1)
    def view_posts(self):
        self.client.get('/api/posts?page=1', headers=self.headers)

    @task(1)
    def create_post(self):
        self.client.post(
            '/api/posts',
            json={'title': 'Load Test Post', 'body': 'Test content'},
            headers=self.headers
        )
```

```bash
# Run Locust
pip install locust

# Interactive UI
locust -f locustfile.py --host=http://localhost:3000
# Open http://localhost:8089

# Headless (CI)
locust -f locustfile.py --host=http://localhost:3000 \
  --headless --users 50 --spawn-rate 5 --run-time 5m \
  --csv=results
```

## Reading Results

### Key Metrics to Track

| Metric | Description | Target |
|--------|-------------|--------|
| `p50` (median) | 50% of requests complete in X ms | < 200ms |
| `p95` | 95% of requests complete in X ms | < 500ms |
| `p99` | 99% of requests complete in X ms | < 1000ms |
| `rps` | Requests per second throughput | Baseline × safety factor |
| `error_rate` | % of failed requests | < 1% |
| `vus_max` | Concurrent users at breaking point | Capacity ceiling |

### k6 CLI Output Key

```
✓ status is 200 .................... 99.97% ✓ 12456 ✗ 4
  http_req_duration..............: avg=245ms  min=12ms p(90)=487ms p(95)=612ms p(99)=1.2s
  http_req_failed................: 0.03%  ✓ 4   ✗ 12452
  http_reqs......................: 12456   41.52/s
```

## CI Integration

```yaml
# .github/workflows/load-test.yml
name: Load Tests
on:
  schedule:
    - cron: '0 2 * * *'   # Nightly at 2am
  workflow_dispatch:        # Manual trigger

jobs:
  load-test:
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'
    steps:
      - uses: actions/checkout@v4
      - uses: grafana/setup-k6-action@v1
      - name: Run smoke test
        run: k6 run scripts/smoke-test.js
        env:
          BASE_URL: ${{ secrets.STAGING_URL }}
      - name: Run load test
        run: k6 run scripts/load-test.js
        env:
          BASE_URL: ${{ secrets.STAGING_URL }}
```

## Performance Baseline Template

Before any optimization work, record:

```markdown
## Performance Baseline — YYYY-MM-DD

**Environment:** staging / production
**Load:** X VUs, Y minutes
**Build:** commit SHA

| Endpoint | p50 | p95 | p99 | RPS | Error % |
|----------|-----|-----|-----|-----|---------|
| GET /api/dashboard | 145ms | 320ms | 680ms | 42 | 0.1% |
| POST /api/posts | 89ms | 210ms | 445ms | 18 | 0% |
| GET /api/search | 220ms | 890ms | 2100ms | 12 | 0.4% |

**Breaking point:** 180 VUs (errors spike above 5% at this point)
**Memory at peak:** 1.2 GB
**CPU at peak:** 78%
```
