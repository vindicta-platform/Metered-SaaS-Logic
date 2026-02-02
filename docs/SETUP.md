# Setup Guide

## Prerequisites
- Python 3.10+

## Development Setup
```bash
git clone https://github.com/vindicta-platform/Metered-SaaS-Logic.git
cd Metered-SaaS-Logic
python -m venv .venv
.venv\Scripts\activate
pip install -e ".[dev]"
```

## Running Tests
```powershell
pytest tests/ -v
```
