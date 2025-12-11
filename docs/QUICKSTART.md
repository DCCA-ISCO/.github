# 🚀 Quick Start Guide - Python Application Development

This guide outlines the standard setup for any Python application within the DCCA-ISCO organization.

## Local Development Setup

### Prerequisites
- Python 3.14 (Standard version)
- Git
- Appropriate IDE (VS Code recommended)

### 1. Clone and Install
```bash
# Clone the repository
git clone [https://github.com/DCCA-ISCO/YOUR-PROJECT-NAME.git](https://github.com/DCCA-ISCO/YOUR-PROJECT-NAME.git)
cd YOUR-PROJECT-NAME

# Create virtual environment
python -m venv venv --prompt <shortened project name>

# Activate virtual environment
venv\Scripts\activate  # Windows
source venv/bin/activate  # Linux/Mac

# Install dependencies
pip install -r requirements.txt
pip install -r requirements-dev.txt # For testing tools
