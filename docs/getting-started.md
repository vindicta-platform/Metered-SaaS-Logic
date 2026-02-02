# Getting Started

```bash
uv pip install git+https://github.com/vindicta-platform/Metered-SaaS-Logic.git
```

```python
from metered_saas import UsageTracker

tracker = UsageTracker()
tracker.record("api_call", user="user-1", quantity=1)
```
