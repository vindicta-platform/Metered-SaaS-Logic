# Metered-SaaS-Logic

Usage metering and billing logic for SaaS platforms.

## Overview

Metered-SaaS-Logic provides the core logic for tracking usage, calculating costs, and managing billing cycles in metered SaaS applications.

## Features

- **Usage Tracking**: Record and aggregate usage events
- **Cost Calculation**: Dynamic pricing with time-of-day multipliers
- **Billing Cycles**: Manage subscription periods and invoicing
- **Gas Tank Integration**: Platform credit consumption tracking

## Installation

Install from source using uv:

```bash
uv pip install git+https://github.com/vindicta-platform/Metered-SaaS-Logic.git
```

Or clone and install locally:

```bash
git clone https://github.com/vindicta-platform/Metered-SaaS-Logic.git
cd Metered-SaaS-Logic
uv pip install -e .
```

## Quick Start

```python
from metered_saas import UsageMeter, PricingTier

meter = UsageMeter(
    tier=PricingTier.STANDARD,
    monthly_limit=10000
)

# Record usage
meter.record("api_call", quantity=1)
meter.record("compute_minutes", quantity=5.5)

# Check balance
print(f"Used: {meter.used_credits}")
print(f"Remaining: {meter.remaining_credits}")
```

## Related Repositories

| Repository | Relationship |
|------------|-------------|
| [Atomic-Ledger-Py](https://github.com/vindicta-platform/Atomic-Ledger-Py) | Transaction ledger |
| [platform-core](https://github.com/vindicta-platform/platform-core) | Gas Tank economy |

## License

MIT License - See [LICENSE](./LICENSE) for details.
