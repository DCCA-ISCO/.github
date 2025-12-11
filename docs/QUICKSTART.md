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
```
### 2. Configure Environment
Create the necessary environment configuration file (often named `.env`, `config.ini`, or `SYS/clib.env` for historical projects). 
**Do not commit this file.**

Use `.env.example` files to template necessary configurations.  This document can be commited.

### 3. Run and Test
Run your application using the method defined in your project's main README (e.g., `python app.py`). Run tests using the standard command: `pytest test_app.py -v`.  

Run Unit Tests whenever possible.  General vulnerability scans, linting, and smoke tests are already set up in GH Action Workflows.  Additional unit tests can be triggered or defined in these workflows.
