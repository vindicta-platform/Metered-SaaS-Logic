# Feature Proposal: Usage-Based Dynamic Pricing Engine

**Proposal ID**: FEAT-009
**Author**: Unified Product Architect (Autonomous)
**Created**: 2026-02-01
**Status**: Draft
**Priority**: High
**Target Repository**: Metered-SaaS-Logic

---

## Part A: Software Design Document (SDD)

### 1. Executive Summary

Implement a dynamic pricing engine that adjusts Gas Credit costs based on demand, time-of-day, and user tier, maximizing platform revenue while rewarding loyal users with better rates.

### 2. System Architecture

#### 2.1 Current State
- Static pricing tiers
- Time-of-day multipliers (partial)
- No demand-based pricing
- No loyalty discounts

#### 2.2 Proposed Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                 Dynamic Pricing Engine                          │
│  ┌─────────────────────────────────────────────────────────┐    │
│  │                   Price Calculator                      │    │
│  │   base_rate × demand_multiplier × time_multiplier       │    │
│  │              × loyalty_discount × tier_modifier          │    │
│  └─────────────────────────────────────────────────────────┘    │
│                              │                                  │
│              ┌───────────────┼───────────────┐                  │
│              ▼               ▼               ▼                  │
│  ┌────────────────┐ ┌────────────────┐ ┌────────────────┐       │
│  │ Demand Monitor │ │  Time Service  │ │ Loyalty Tracker│       │
│  │ (real-time)    │ │  (schedules)   │ │ (user history) │       │
│  └────────────────┘ └────────────────┘ └────────────────┘       │
└─────────────────────────────────────────────────────────────────┘
```

#### 2.3 File Changes

```
Metered-SaaS-Logic/
├── src/
│   └── metered_saas/
│       ├── pricing/
│       │   ├── __init__.py      [NEW]
│       │   ├── dynamic.py       [NEW] Dynamic pricing engine
│       │   ├── demand.py        [NEW] Demand monitoring
│       │   └── loyalty.py       [NEW] Loyalty discount logic
│       └── meter.py             [MODIFY] Use dynamic pricing
├── tests/
│   └── test_dynamic_pricing.py  [NEW]
└── docs/
    └── pricing.md               [NEW]
```

### 3. Pricing Factors

| Factor | Range | Description |
|--------|-------|-------------|
| Base Rate | 1.0 | Standard credit per unit |
| Demand Multiplier | 0.8 - 1.5 | Based on concurrent usage |
| Time Multiplier | 0.7 - 1.2 | Peak vs off-peak hours |
| Loyalty Discount | 0.85 - 1.0 | Based on tenure |
| Tier Modifier | 0.7 - 1.0 | Champion tier gets 30% off |

### 4. Demand-Based Pricing

```python
class DemandMonitor:
    """Monitor platform demand for dynamic pricing."""

    thresholds = {
        'low': (0, 30),      # 0-30% capacity: 0.8x
        'normal': (30, 70),  # 30-70% capacity: 1.0x
        'high': (70, 90),    # 70-90% capacity: 1.2x
        'peak': (90, 100),   # 90-100% capacity: 1.5x
    }

    def current_multiplier(self) -> Decimal:
        """Get current demand-based price multiplier."""
```

### 5. Loyalty Program Integration

```python
class LoyaltyTracker:
    """Track user tenure and spending for loyalty discounts."""

    tiers = {
        'new': {'months': 0, 'discount': 0},
        'regular': {'months': 3, 'discount': 5},
        'veteran': {'months': 12, 'discount': 10},
        'legend': {'months': 24, 'discount': 15},
    }
```

### 6. Price Transparency

- All multipliers shown to user before action
- Price lock option for premium users
- Daily price summary in dashboard
- No surprise charges

---

## Part B: Behavior Driven Development (BDD)

### User Stories

#### US-001: Off-Peak Savings
**As a** budget-conscious user
**I want to** see when prices are lowest
**So that** I can schedule my usage for maximum savings

#### US-002: Loyalty Rewards
**As a** long-time member
**I want to** receive automatic discounts
**So that** my loyalty is rewarded

#### US-003: Price Transparency
**As a** platform user
**I want to** see the current price multiplier
**So that** I can make informed decisions

### Acceptance Criteria

```gherkin
Feature: Dynamic Pricing

  Scenario: Off-peak discount applied
    Given it is 3am Eastern Time (off-peak)
    And demand is at 20% capacity
    When I perform a metered action
    Then my cost should be base_rate × 0.7 × 0.8

  Scenario: Loyalty discount stacked
    Given I am a "veteran" member (12+ months)
    And I have the "Champion" tier
    When I perform a metered action
    Then my cost should include 10% loyalty + 30% tier discount

  Scenario: Peak pricing notification
    Given demand is at 95% capacity
    When I attempt a metered action
    Then I should see a warning "Peak pricing: 1.5x rate"
    And have the option to defer or proceed
```

---

## Implementation Estimate

| Phase | Effort | Dependencies |
|-------|--------|--------------|
| Demand Monitor | 4 hours | Usage metrics |
| Loyalty Tracker | 4 hours | User data |
| Price Calculator | 4 hours | None |
| UI Integration | 4 hours | Portal |
| Testing | 4 hours | None |
| **Total** | **20 hours** | |
