# Day 2: Project Structure Setup - Complete Guide
# Run these commands step by step

echo "🚀 Starting Day 2: Project Structure Setup"
echo "=========================================="

# Step 1: Create Master Learning Repository
echo "📁 Creating master repository structure..."
mkdir swe-mastery-journey
cd swe-mastery-journey

# Initialize Poetry project
echo "🎯 Initializing Poetry project..."
poetry init --no-interaction \
  --name "swe-mastery-journey" \
  --version "0.1.0" \
  --description "24-week journey to senior software engineer mastery" \
  --author "Your Name <your.email@example.com>" \
  --python "^3.11"

# Add essential dependencies
echo "📦 Adding core dependencies..."
poetry add fastapi uvicorn sqlalchemy psycopg2-binary redis pymongo
poetry add httpx requests beautifulsoup4 streamlit plotly pandas numpy

# Add development dependencies
echo "🛠️ Adding development dependencies..."
poetry add --group dev pytest black mypy ruff pre-commit
poetry add --group dev pytest-cov pytest-asyncio httpx pytest-mock
poetry add --group dev sphinx mkdocs mkdocs-material
poetry add --group dev bandit safety

# Create comprehensive project structure
echo "🏗️ Creating project directories..."
mkdir -p projects/week-{01..24}
mkdir -p algorithms/{sorting,searching,graphs,trees,dynamic-programming,greedy,backtracking}
mkdir -p patterns/{creational,structural,behavioral,architectural}
mkdir -p architecture/{diagrams,designs,decisions,reviews}
mkdir -p docs/{requirements,design,testing,deployment,guides}
mkdir -p tools/{generators,analyzers,benchmarks,utilities}
mkdir -p tests/{unit,integration,e2e,performance}
mkdir -p config/{development,staging,production}
mkdir -p scripts/{setup,deployment,monitoring,backup}
mkdir -p data/{samples,schemas,migrations,seeds}

# Create essential configuration files
echo "⚙️ Creating configuration files..."

# .gitignore
cat > .gitignore << 'EOF'
# Python
__pycache__/
*.py[cod]
*$py.class
*.so
.Python
build/
develop-eggs/
dist/
downloads/
eggs/
.eggs/
lib/
lib64/
parts/
sdist/
var/
wheels/
share/python-wheels/
*.egg-info/
.installed.cfg
*.egg
MANIFEST

# Virtual environments
.env
.venv
env/
venv/
ENV/
env.bak/
venv.bak/

# IDEs
.vscode/
.idea/
*.swp
*.swo
*~

# Testing
.coverage
.pytest_cache/
.tox/
.nox/
htmlcov/
.coverage.*
coverage.xml
*.cover
*.py,cover
.hypothesis/

# Databases
*.db
*.sqlite3

# Logs
*.log
logs/

# OS
.DS_Store
.DS_Store?
._*
.Spotlight-V100
.Trashes
ehthumbs.db
Thumbs.db

# Project specific
temp/
tmp/
*.tmp
.cache/
.mypy_cache/
.ruff_cache/
EOF

# pyproject.toml updates
cat >> pyproject.toml << 'EOF'

[tool.black]
line-length = 88
target-version = ['py311']
include = '\.pyi?$'
extend-exclude = '''
/(
  # directories
  \.eggs
  | \.git
  | \.mypy_cache
  | \.tox
  | \.venv
  | build
  | dist
)/
'''

[tool.mypy]
python_version = "3.11"
warn_return_any = true
warn_unused_configs = true
disallow_untyped_defs = true
disallow_incomplete_defs = true
check_untyped_defs = true
disallow_untyped_decorators = true
no_implicit_optional = true
warn_redundant_casts = true
warn_unused_ignores = true
warn_no_return = true
warn_unreachable = true
strict_equality = true

[tool.ruff]
target-version = "py311"
line-length = 88
select = [
    "E",  # pycodestyle errors
    "W",  # pycodestyle warnings
    "F",  # pyflakes
    "I",  # isort
    "B",  # flake8-bugbear
    "C4", # flake8-comprehensions
    "UP", # pyupgrade
]
ignore = [
    "E501",  # line too long, handled by black
    "B008",  # do not perform function calls in argument defaults
    "C901",  # too complex
]

[tool.ruff.per-file-ignores]
"__init__.py" = ["F401"]

[tool.pytest.ini_options]
minversion = "6.0"
addopts = "-ra -q --strict-markers --strict-config"
testpaths = [
    "tests",
]
python_files = [
    "test_*.py",
    "*_test.py",
]
markers = [
    "slow: marks tests as slow (deselect with '-m \"not slow\"')",
    "integration: marks tests as integration tests",
    "e2e: marks tests as end-to-end tests",
]

[tool.coverage.run]
source = ["src"]
omit = [
    "*/tests/*",
    "*/test_*",
    "*/conftest.py",
]

[tool.coverage.report]
exclude_lines = [
    "pragma: no cover",
    "def __repr__",
    "if self.debug:",
    "if settings.DEBUG",
    "raise AssertionError",
    "raise NotImplementedError",
    "if 0:",
    "if __name__ == .__main__.:",
    "class .*\bProtocol\):",
    "@(abc\.)?abstractmethod",
]
EOF

# Pre-commit configuration
cat > .pre-commit-config.yaml << 'EOF'
repos:
  - repo: https://github.com/pre-commit/pre-commit-hooks
    rev: v4.4.0
    hooks:
      - id: trailing-whitespace
      - id: end-of-file-fixer
      - id: check-yaml
      - id: check-added-large-files
      - id: check-merge-conflict
      - id: debug-statements
      - id: check-docstring-first

  - repo: https://github.com/psf/black
    rev: 23.3.0
    hooks:
      - id: black
        language_version: python3.11

  - repo: https://github.com/charliermarsh/ruff-pre-commit
    rev: v0.0.270
    hooks:
      - id: ruff
        args: [--fix, --exit-non-zero-on-fix]

  - repo: https://github.com/pre-commit/mirrors-mypy
    rev: v1.3.0
    hooks:
      - id: mypy
        additional_dependencies: [types-requests, types-redis]

  - repo: https://github.com/PyCQA/bandit
    rev: 1.7.5
    hooks:
      - id: bandit
        args: ['-c', 'pyproject.toml']
        additional_dependencies: ['bandit[toml]']
EOF

# Main README.md
cat > README.md << 'EOF'
# 🚀 Software Engineering Mastery Journey

Welcome to my 24-week transformation from Python Developer to Senior Software Engineer/Architect!

## 🎯 Mission
Develop unbeatable senior software engineer/architect level knowledge with rock-solid fundamentals across all SWEBOK knowledge areas.

## 📊 Current Progress
- **Week**: 1/24
- **Phase**: Foundation & Environment Setup
- **Completion**: 8%

## 🏗️ Repository Structure

```
swe-mastery-journey/
├── projects/          # Weekly hands-on projects
│   ├── week-01/      # Algorithm Visualization Tool
│   ├── week-02/      # Performance Benchmarking Suite
│   └── ...
├── algorithms/        # Algorithm implementations & analysis
├── patterns/         # Design patterns playground
├── architecture/     # System design & architecture docs
├── docs/            # Documentation & learning notes
├── tools/           # Custom development tools
├── tests/           # Comprehensive test suites
└── scripts/         # Automation scripts
```

## 🛠️ Tech Stack Mastery
- **Languages**: Python, JavaScript/TypeScript, SQL
- **Frameworks**: FastAPI, React, Django
- **Databases**: PostgreSQL, Redis, MongoDB
- **Cloud**: AWS (Solutions Architect Pro path)
- **DevOps**: Docker, Kubernetes, Terraform
- **Tools**: Git, VS Code, Docker Desktop

## 📚 Learning Resources
- SWEBOK v3.0 (Software Engineering Body of Knowledge)
- Clean Code, Design Patterns, System Design Interview
- AWS Documentation & Hands-on Labs
- LeetCode (Daily Problem Solving)

## 🎖️ Milestones & Certifications
- [ ] Week 12: Can design scalable web applications
- [ ] Week 24: Can architect complex distributed systems
- [ ] AWS Solutions Architect Professional
- [ ] Kubernetes Administrator (CKA)

## 📈 Daily Practice
- **Morning**: Algorithm problems (LeetCode/HackerRank)
- **Afternoon**: Project development & testing
- **Evening**: Reading & documentation

## 🤝 Connect & Follow
- GitHub: [Your GitHub Profile]
- LinkedIn: [Your LinkedIn Profile]
- Blog: [Your Technical Blog]

---
*"The journey of a thousand miles begins with one step."* - Starting Week 1, Day 2 ✨
EOF

# Initialize git repository
echo "🎯 Initializing Git repository..."
git init
git add .
git commit -m "feat: initial project structure setup

- Created comprehensive directory structure for 24-week journey
- Added Poetry configuration with essential dependencies
- Set up pre-commit hooks for code quality
- Configured linting, formatting, and type checking
- Added comprehensive gitignore and documentation"

echo ""
echo "✅ Day 2 Setup Complete!"
echo "========================"
echo "📁 Master repository created: swe-mastery-journey/"
echo "🔧 Poetry environment configured with all dependencies"
echo "🎯 Pre-commit hooks ready for code quality enforcement"
echo "📖 Documentation foundation established"
echo ""
echo "🚀 Ready for next steps:"
echo "1. Run 'poetry install' to install all dependencies"
echo "2. Run 'poetry shell' to activate virtual environment"
echo "3. Run 'pre-commit install' to setup git hooks"
echo "4. Create GitHub repository and push code"
echo ""
echo "Tomorrow: Day 3 - Algorithm Visualizer Project! 🎨"