# 🚀 Immediate Setup Guide & First Week Action Plan

## Day 1-2: Environment Setup Checklist

### Core Development Setup

#### Development Tools Installation

**VS Code Setup**
```json
// Essential Extensions
{
  "recommendations": [
    "ms-python.python",
    "ms-python.black-formatter",
    "ms-python.mypy-type-checker",
    "ms-vscode.vscode-json",
    "redhat.vscode-yaml",
    "ms-vscode.vscode-typescript-next",
    "bradlc.vscode-tailwindcss",
    "github.copilot",
    "ms-azuretools.vscode-docker",
    "ms-kubernetes-tools.vscode-kubernetes-tools"
  ]
}
```

**Python Environment**
```bash
# 1. Python Environment
curl -L https://github.com/pyenv/pyenv-installer/raw/master/bin/pyenv-installer | bash
pyenv install 3.11.7
pyenv global 3.11.7

# 2. Essential Python Packages
pip install --upgrade pip
pip install poetry  # For dependency management
poetry --version

# 3. Git Configuration
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"
git config --global init.defaultBranch main

# 4. GitHub CLI
# Download from: https://github.com/cli/cli/releases
gh auth login
```

#### Docker Installation
```bash
# Install Docker Desktop from: https://www.docker.com/products/docker-desktop/
# Verify installation
docker --version
docker-compose --version
```

#### Database Setup
```bash
# PostgreSQL with Docker
docker run --name postgres-dev \
  -e POSTGRES_PASSWORD=devpassword \
  -e POSTGRES_DB=devdb \
  -p 5432:5432 -d postgres:15

# Redis with Docker
docker run --name redis-dev -p 6379:6379 -d redis:latest

# MongoDB with Docker
docker run --name mongo-dev -p 27017:27017 -d mongo:latest
```

## Day 3: Project Structure Setup

### Create Master Learning Repository

```bash
mkdir swe-mastery-journey
cd swe-mastery-journey

# Initialize with Poetry
poetry init
poetry add fastapi uvicorn sqlalchemy psycopg2-binary redis pymongo
poetry add --group dev pytest black mypy ruff pre-commit
poetry add --group dev pytest-cov pytest-asyncio httpx

# Create project structure
mkdir -p {projects,algorithms,patterns,architecture,docs,tools}
mkdir -p projects/{week-{01..24}}
mkdir -p algorithms/{sorting,searching,graphs,trees,dynamic-programming}
mkdir -p patterns/{creational,structural,behavioral}
mkdir -p architecture/{diagrams,designs,decisions}

# Initialize git
git init
echo "
__pycache__/
*.pyc
.env
.venv/
.coverage
htmlcov/
.pytest_cache/
.mypy_cache/
.DS_Store
" > .gitignore

git add .
git commit -m "Initial project structure"
```

### Project Template Creation

```python
# tools/template_generator.py
#!/usr/bin/env python3
"""
Template generator for new projects
"""
import os
import argparse
from pathlib import Path

def create_project_template(project_name: str, week: str):
    """Create a standardized project template"""
    project_path = Path(f"projects/week-{week.zfill(2)}/{project_name}")
    project_path.mkdir(parents=True, exist_ok=True)
    
    # Create standard directories
    dirs = ['src', 'tests', 'docs', 'config', 'scripts']
    for dir_name in dirs:
        (project_path / dir_name).mkdir(exist_ok=True)
    
    # Create template files
    files = {
        'README.md': f"# {project_name}\n\n## Overview\n\n## Setup\n\n## Usage\n",
        'requirements.txt': "# Add project-specific requirements here\n",
        'src/__init__.py': "",
        'tests/__init__.py': "",
        'tests/test_main.py': f'"""Tests for {project_name}"""\nimport pytest\n',
        'pyproject.toml': f"""[tool.poetry]
name = "{project_name}"
version = "0.1.0"
description = ""
authors = ["Your Name <your.email@example.com>"]

[tool.poetry.dependencies]
python = "^3.11"

[tool.poetry.group.dev.dependencies]
pytest = "^7.0"
black = "^23.0"
mypy = "^1.0"

[build-system]
requires = ["poetry-core"]
build-backend = "poetry.core.masonry.api"
"""
    }
    
    for file_path, content in files.items():
        (project_path / file_path).write_text(content)
    
    print(f"✅ Created project template: {project_path}")

if __name__ == "__main__":
    parser = argparse.ArgumentParser()
    parser.add_argument("project_name", help="Name of the project")
    parser.add_argument("week", help="Week number")
    args = parser.parse_args()
    create_project_template(args.project_name, args.week)
```

## Day 4-7: First Week Projects

### Project 1: Algorithm Visualization Tool

```python
# projects/week-01/algo-visualizer/src/main.py
import streamlit as st
import numpy as np
import pandas as pd
import time
import matplotlib.pyplot as plt
from typing import List, Callable, Tuple

class AlgorithmVisualizer:
    def __init__(self):
        self.algorithms = {
            'bubble_sort': self.bubble_sort,
            'quick_sort': self.quick_sort,
            'merge_sort': self.merge_sort,
            'heap_sort': self.heap_sort,
        }
    
    def bubble_sort(self, arr: List[int]) -> List[Tuple[List[int], int, int]]:
        """Bubble sort with step-by-step visualization data"""
        steps = []
        arr = arr.copy()
        n = len(arr)
        
        for i in range(n):
            for j in range(0, n - i - 1):
                if arr[j] > arr[j + 1]:
                    arr[j], arr[j + 1] = arr[j + 1], arr[j]
                    steps.append((arr.copy(), j, j + 1))
        
        return steps
    
    def visualize_algorithm(self, algorithm_name: str, data: List[int]):
        """Create interactive visualization"""
        if algorithm_name not in self.algorithms:
            st.error(f"Algorithm {algorithm_name} not found!")
            return
        
        steps = self.algorithms[algorithm_name](data)
        
        # Create placeholder for animation
        chart_placeholder = st.empty()
        
        for i, (arr, highlight1, highlight2) in enumerate(steps):
            fig, ax = plt.subplots(figsize=(10, 6))
            colors = ['red' if j in [highlight1, highlight2] else 'blue' 
                     for j in range(len(arr))]
            
            ax.bar(range(len(arr)), arr, color=colors)
            ax.set_title(f'{algorithm_name.replace("_", " ").title()} - Step {i + 1}')
            ax.set_xlabel('Index')
            ax.set_ylabel('Value')
            
            chart_placeholder.pyplot(fig)
            plt.close()
            time.sleep(0.1)

# Streamlit app
def main():
    st.title("🔍 Algorithm Visualizer")
    st.markdown("Interactive visualization of sorting algorithms")
    
    # Sidebar controls
    st.sidebar.header("Configuration")
    array_size = st.sidebar.slider("Array Size", 10, 100, 30)
    algorithm = st.sidebar.selectbox("Choose Algorithm", 
                                   ['bubble_sort', 'quick_sort', 'merge_sort', 'heap_sort'])
    
    # Generate random data
    if st.sidebar.button("Generate New Data"):
        data = np.random.randint(1, 100, array_size).tolist()
        st.session_state.data = data
    
    if 'data' not in st.session_state:
        st.session_state.data = np.random.randint(1, 100, array_size).tolist()
    
    # Display current data
    st.subheader("Current Array")
    st.bar_chart(pd.DataFrame({'values': st.session_state.data}))
    
    # Visualization
    if st.button("Start Visualization"):
        visualizer = AlgorithmVisualizer()
        visualizer.visualize_algorithm(algorithm, st.session_state.data)

if __name__ == "__main__":
    main()
```

### Project 2: Performance Benchmarking Suite

```python
# projects/week-01/performance-bench/src/benchmark.py
import time
import memory_profiler
import matplotlib.pyplot as plt
from typing import List, Dict, Callable, Any
import pandas as pd

class PerformanceBenchmark:
    def __init__(self):
        self.results = {}
    
    def measure_time(self, func: Callable, *args, **kwargs) -> float:
        """Measure execution time of a function"""
        start_time = time.perf_counter()
        func(*args, **kwargs)
        end_time = time.perf_counter()
        return end_time - start_time
    
    def measure_memory(self, func: Callable, *args, **kwargs) -> float:
        """Measure memory usage of a function"""
        mem_before = memory_profiler.memory_usage()[0]
        func(*args, **kwargs)
        mem_after = memory_profiler.memory_usage()[0]
        return mem_after - mem_before
    
    def benchmark_algorithm(self, name: str, func: Callable, 
                          test_sizes: List[int], iterations: int = 5):
        """Comprehensive benchmarking of an algorithm"""
        results = {
            'sizes': [],
            'avg_time': [],
            'avg_memory': [],
            'time_complexity': None
        }
        
        for size in test_sizes:
            # Generate test data
            test_data = list(range(size))
            
            # Time measurements
            times = []
            memories = []
            
            for _ in range(iterations):
                exec_time = self.measure_time(func, test_data.copy())
                memory_usage = self.measure_memory(func, test_data.copy())
                times.append(exec_time)
                memories.append(memory_usage)
            
            results['sizes'].append(size)
            results['avg_time'].append(sum(times) / len(times))
            results['avg_memory'].append(sum(memories) / len(memories))
        
        # Analyze time complexity
        results['time_complexity'] = self._analyze_complexity(
            results['sizes'], results['avg_time'])
        
        self.results[name] = results
        return results
    
    def _analyze_complexity(self, sizes: List[int], times: List[float]) -> str:
        """Analyze time complexity based on growth pattern"""
        # Simple heuristic-based complexity analysis
        if len(sizes) < 2:
            return "Unknown"
        
        # Calculate growth ratios
        ratios = []
        for i in range(1, len(sizes)):
            size_ratio = sizes[i] / sizes[i-1]
            time_ratio = times[i] / times[i-1]
            ratios.append(time_ratio / size_ratio)
        
        avg_ratio = sum(ratios) / len(ratios)
        
        if avg_ratio < 1.5:
            return "O(n)"
        elif avg_ratio < 3:
            return "O(n log n)"
        elif avg_ratio < 5:
            return "O(n²)"
        else:
            return "O(n³) or worse"
    
    def generate_report(self) -> str:
        """Generate comprehensive performance report"""
        report = "# Performance Benchmarking Report\n\n"
        
        for algo_name, results in self.results.items():
            report += f"## {algo_name}\n"
            report += f"**Estimated Complexity:** {results['time_complexity']}\n\n"
            
            # Create DataFrame for better formatting
            df = pd.DataFrame({
                'Input Size': results['sizes'],
                'Avg Time (s)': [f"{t:.6f}" for t in results['avg_time']],
                'Avg Memory (MB)': [f"{m:.2f}" for m in results['avg_memory']]
            })
            
            report += df.to_markdown(index=False) + "\n\n"
        
        return report
    
    def plot_results(self):
        """Create visualization plots"""
        fig, (ax1, ax2) = plt.subplots(1, 2, figsize=(15, 6))
        
        for algo_name, results in self.results.items():
            ax1.plot(results['sizes'], results['avg_time'], 
                    marker='o', label=f"{algo_name} ({results['time_complexity']})")
            ax2.plot(results['sizes'], results['avg_memory'], 
                    marker='s', label=algo_name)
        
        ax1.set_xlabel('Input Size')
        ax1.set_ylabel('Execution Time (seconds)')
        ax1.set_title('Time Complexity Comparison')
        ax1.legend()
        ax1.grid(True)
        
        ax2.set_xlabel('Input Size')
        ax2.set_ylabel('Memory Usage (MB)')
        ax2.set_title('Memory Usage Comparison')
        ax2.legend()
        ax2.grid(True)
        
        plt.tight_layout()
        plt.show()

# Example usage
if __name__ == "__main__":
    # Define algorithms to benchmark
    def bubble_sort(arr):
        n = len(arr)
        for i in range(n):
            for j in range(0, n - i - 1):
                if arr[j] > arr[j + 1]:
                    arr[j], arr[j + 1] = arr[j + 1], arr[j]
    
    def quick_sort(arr):
        if len(arr) <= 1:
            return arr
        pivot = arr[len(arr) // 2]
        left = [x for x in arr if x < pivot]
        middle = [x for x in arr if x == pivot]
        right = [x for x in arr if x > pivot]
        return quick_sort(left) + middle + quick_sort(right)
    
    # Run benchmarks
    benchmark = PerformanceBenchmark()
    test_sizes = [100, 500, 1000, 2000, 5000]
    
    benchmark.benchmark_algorithm("Bubble Sort", bubble_sort, test_sizes)
    benchmark.benchmark_algorithm("Quick Sort", quick_sort, test_sizes)
    
    # Generate results
    print(benchmark.generate_report())
    benchmark.plot_results()
```

## Daily Learning Schedule

### Week 1 Schedule

```markdown
## Day 1 (Monday): Environment Setup
- [ ] Install all development tools
- [ ] Configure VS Code and extensions
- [ ] Set up databases with Docker
- [ ] Create GitHub repositories

## Day 2 (Tuesday): Project Structure
- [ ] Create master learning repository
- [ ] Set up project templates
- [ ] Configure pre-commit hooks
- [ ] Initialize documentation

## Day 3 (Wednesday): Algorithm Visualizer
- [ ] Implement sorting algorithm visualizations
- [ ] Create Streamlit interface
- [ ] Add interactive controls
- [ ] Test with different data sets

## Day 4 (Thursday): Performance Benchmarking
- [ ] Implement benchmarking framework
- [ ] Add complexity analysis
- [ ] Create performance reports
- [ ] Visualize results

## Day 5 (Friday): Data Structures Library
- [ ] Implement basic data structures
- [ ] Add comprehensive tests
- [ ] Document with type hints
- [ ] Performance comparisons

## Day 6 (Saturday): Integration & Review
- [ ] Integrate all week 1 projects
- [ ] Code review and refactoring
- [ ] Update documentation
- [ ] Plan week 2 activities

## Day 7 (Sunday): LeetCode Practice
- [ ] Solve 10 easy problems
- [ ] Focus on array/string manipulation
- [ ] Document solutions and approaches
- [ ] Analyze time/space complexity
```

## Essential Commands Cheat Sheet

### Git Workflow
```bash
# Daily workflow
git add .
git commit -m "feat: implement algorithm visualizer"
git push origin main

# Feature branches
git checkout -b feature/new-algorithm
git merge feature/new-algorithm
git branch -d feature/new-algorithm
```

### Poetry Commands
```bash
# Dependency management
poetry add package-name
poetry add --group dev package-name
poetry install
poetry update

# Virtual environment
poetry shell
poetry env info
```

### Docker Quick Commands
```bash
# Database containers
docker-compose up -d  # Start all services
docker-compose down   # Stop all services
docker logs container-name  # View logs
docker exec -it container-name bash  # Interactive shell
```

### Testing Commands
```bash
# Run tests
pytest
pytest --cov=src --cov-report=html
pytest -v --tb=short

# Code quality
black .
mypy src/
ruff check .
```

## First Week Success Metrics

### Completion Criteria
- All development tools installed and configured
- Master repository created with proper structure
- Algorithm visualizer working with 4+ algorithms
- Performance benchmarking suite functional
- Data structures library with tests
- 20+ LeetCode problems solved
- Daily progress documented

### Quality Gates
- Code coverage > 80%
- All type hints added
- Documentation complete
- No linting errors
- All tests passing

## Immediate Action Items

1. **Right Now:** Start with environment setup
2. **Today:** Complete Python and Git configuration
3. **Tomorrow:** Set up Docker and databases
4. **This Week:** Complete all Week 1 projects

---

Ready to start your transformation into a senior software engineer? Let's begin with the environment setup! 🚀