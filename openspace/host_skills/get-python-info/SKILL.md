---
name: get-python-info
description: Get Python version and list installed packages
---

# Get Python Info

Run these commands in order:

1. Show Python version with detailed build info:

python -c "import sys; print(f'Python {sys.version_info.major}.{sys.version_info.minor}.{sys.version_info.micro}')"


2. List installed packages sorted by size:
pip list format-freeze | sort | head -20
