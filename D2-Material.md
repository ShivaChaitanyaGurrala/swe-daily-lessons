#!/usr/bin/env python3
"""
Smart Project Template Generator
Creates standardized project structures for the 24-week SWE mastery journey
"""

import os
import argparse
from pathlib import Path
from datetime import datetime
from typing import Dict, List, Optional
import json


class ProjectTemplateGenerator:
    """Intelligent project template generator with multiple project types."""
    
    def __init__(self):
        self.project_types = {
            "web_app": self._create_web_app_template,
            "algorithm": self._create_algorithm_template,
            "data_structure": self._create_data_structure_template,
            "microservice": self._create_microservice_template,
            "cli_tool": self._create_cli_template,
            "analysis": self._create_analysis_template,
        }
        
    def create_project(
        self, 
        project_name: str, 
        week: str, 
        project_type: str = "web_app",
        description: str = "",
        author: str = "Your Name",
        email: str = "your.email@example.com"
    ) -> Path:
        """Create a new project with the specified template."""
        
        if project_type not in self.project_types:
            raise ValueError(f"Unknown project type: {project_type}")
            
        project_path = Path(f"projects/week-{week.zfill(2)}/{project_name}")
        project_path.mkdir(parents=True, exist_ok=True)
        
        # Create base structure
        self._create_base_structure(project_path)
        
        # Apply specific template
        self.project_types[project_type](
            project_path, project_name, description, author, email
        )
        
        # Create common files
        self._create_common_files(project_path, project_name, week, description, author, email)
        
        print(f"✅ Created {project_type} project: {project_path}")
        return project_path
    
    def _create_base_structure(self, project_path: Path) -> None:
        """Create the base directory structure."""
        directories = [
            "src", "tests", "docs", "config", "scripts", 
            "data", "logs", "temp", "assets"
        ]
        
        for directory in directories:
            (project_path / directory).mkdir(exist_ok=True)
            
        # Create __init__.py files
        (project_path / "src" / "__init__.py").touch()
        (project_path / "tests" / "__init__.py").touch()
    
    def _create_web_app_template(
        self, project_path: Path, name: str, description: str, author: str, email: str
    ) -> None:
        """Create a FastAPI web application template."""
        
        # Main application
        main_py = f'''"""
{name.replace("-", " ").title()} - FastAPI Web Application
{description}
"""

from fastapi import FastAPI, HTTPException, Depends
from fastapi.middleware.cors import CORSMiddleware
from fastapi.responses import HTMLResponse
from pydantic import BaseModel
from typing import List, Optional
import uvicorn

app = FastAPI(
    title="{name.replace("-", " ").title()}",
    description="{description}",
    version="0.1.0",
    docs_url="/docs",
    redoc_url="/redoc"
)

# CORS middleware
app.add_middleware(
    CORSMiddleware,
    allow_origins=["*"],  # Configure appropriately for production
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)

# Pydantic models
class HealthResponse(BaseModel):
    status: str
    timestamp: str
    version: str

class ItemBase(BaseModel):
    name: str
    description: Optional[str] = None

class ItemCreate(ItemBase):
    pass

class Item(ItemBase):
    id: int
    created_at: str
    
    class Config:
        from_attributes = True

# In-memory storage (replace with database in production)
items_db: List[Item] = []
next_id = 1

@app.get("/", response_class=HTMLResponse)
async def root():
    return """
    <html>
        <head><title>{name.replace("-", " ").title()}</title></head>
        <body>
            <h1>Welcome to {name.replace("-", " ").title()}!</h1>
            <p>{description}</p>
            <p><a href="/docs">API Documentation</a></p>
        </body>
    </html>
    """

@app.get("/health", response_model=HealthResponse)
async def health_check():
    """Health check endpoint."""
    from datetime import datetime
    return HealthResponse(
        status="healthy",
        timestamp=datetime.now().isoformat(),
        version="0.1.0"
    )

@app.get("/items", response_model=List[Item])
async def list_items():
    """Get all items."""
    return items_db

@app.post("/items", response_model=Item)
async def create_item(item: ItemCreate):
    """Create a new item."""
    global next_id
    from datetime import datetime
    
    new_item = Item(
        id=next_id,
        name=item.name,
        description=item.description,
        created_at=datetime.now().isoformat()
    )
    items_db.append(new_item)
    next_id += 1
    return new_item

@app.get("/items/{{item_id}}", response_model=Item)
async def get_item(item_id: int):
    """Get a specific item by ID."""
    for item in items_db:
        if item.id == item_id:
            return item
    raise HTTPException(status_code=404, detail="Item not found")

@app.delete("/items/{{item_id}}")
async def delete_item(item_id: int):
    """Delete an item by ID."""
    global items_db
    items_db = [item for item in items_db if item.id != item_id]
    return {{"message": "Item deleted successfully"}}

if __name__ == "__main__":
    uvicorn.run(
        "main:app",
        host="0.0.0.0",
        port=8000,
        reload=True,
        log_level="info"
    )
'''
        (project_path / "src" / "main.py").write_text(main_py)
        
        # Database models
        models_py = '''"""Database models."""

from sqlalchemy import Column, Integer, String, DateTime, Text
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy.sql import func

Base = declarative_base()

class Item(Base):
    __tablename__ = "items"
    
    id = Column(Integer, primary_key=True, index=True)
    name = Column(String(255), nullable=False, index=True)
    description = Column(Text, nullable=True)
    created_at = Column(DateTime(timezone=True), server_default=func.now())
    updated_at = Column(DateTime(timezone=True), onupdate=func.now())
    
    def __repr__(self):
        return f"<Item(id={self.id}, name='{self.name}')>"
'''
        (project_path / "src" / "models.py").write_text(models_py)
        
        # Docker configuration
        dockerfile = f'''FROM python:3.11-slim

WORKDIR /app

# Install system dependencies
RUN apt-get update && apt-get install -y \\
    gcc \\
    && rm -rf /var/lib/apt/lists/*

# Copy poetry files
COPY pyproject.toml poetry.lock* ./

# Install Poetry
RUN pip install poetry

# Configure poetry
RUN poetry config virtualenvs.create false

# Install dependencies
RUN poetry install --no-dev

# Copy application code
COPY src/ ./src/

# Expose port
EXPOSE 8000

# Run the application
CMD ["python", "src/main.py"]
'''
        (project_path / "Dockerfile").write_text(dockerfile)
        
        # Docker Compose
        docker_compose = f'''version: '3.8'

services:
  app:
    build: .
    ports:
      - "8000:8000"
    environment:
      - DATABASE_URL=postgresql://user:password@db:5432/{name.replace("-", "_")}_db
      - REDIS_URL=redis://redis:6379
    depends_on:
      - db
      - redis
    volumes:
      - ./src:/app/src
      - ./logs:/app/logs

  db:
    image: postgres:15
    environment:
      POSTGRES_DB: {name.replace("-", "_")}_db
      POSTGRES_USER: user
      POSTGRES_PASSWORD: password
    ports:
      - "5432:5432"
    volumes:
      - postgres_data:/var/lib/postgresql/data

  redis:
    image: redis:latest
    ports:
      - "6379:6379"

volumes:
  postgres_data:
'''
        (project_path / "docker-compose.yml").write_text(docker_compose)
    
    def _create_algorithm_template(
        self, project_path: Path, name: str, description: str, author: str, email: str
    ) -> None:
        """Create an algorithm implementation template."""
        
        main_py = f'''"""
{name.replace("-", " ").title()} - Algorithm Implementation
{description}
"""

from typing import List, Tuple, Optional, Any
from abc import ABC, abstractmethod
import time
import random
from dataclasses import dataclass


@dataclass
class AlgorithmResult:
    """Container for algorithm execution results."""
    result: Any
    execution_time: float
    operations_count: int
    memory_usage: Optional[int] = None


class Algorithm(ABC):
    """Base class for all algorithm implementations."""
    
    def __init__(self, name: str):
        self.name = name
        self.operations_count = 0
    
    @abstractmethod
    def execute(self, data: Any) -> Any:
        """Execute the algorithm on the given data."""
        pass
    
    def benchmark(self, data: Any) -> AlgorithmResult:
        """Benchmark the algorithm execution."""
        self.operations_count = 0
        start_time = time.perf_counter()
        
        result = self.execute(data)
        
        end_time = time.perf_counter()
        execution_time = end_time - start_time
        
        return AlgorithmResult(
            result=result,
            execution_time=execution_time,
            operations_count=self.operations_count
        )
    
    def _increment_operation(self):
        """Increment the operations counter."""
        self.operations_count += 1


class SortingAlgorithm(Algorithm):
    """Base class for sorting algorithms."""
    
    def __init__(self, name: str):
        super().__init__(name)
    
    def is_sorted(self, arr: List[int]) -> bool:
        """Check if array is sorted."""
        return all(arr[i] <= arr[i + 1] for i in range(len(arr) - 1))
    
    def generate_test_data(self, size: int, min_val: int = 1, max_val: int = 1000) -> List[int]:
        """Generate random test data."""
        return [random.randint(min_val, max_val) for _ in range(size)]


class ExampleSort(SortingAlgorithm):
    """Example sorting algorithm implementation."""
    
    def __init__(self):
        super().__init__("Example Sort")
    
    def execute(self, data: List[int]) -> List[int]:
        """
        Implement your sorting algorithm here.
        This is a placeholder implementation using Python's built-in sort.
        """
        arr = data.copy()  # Don't modify original data
        
        # Example: Bubble Sort implementation
        n = len(arr)
        for i in range(n):
            for j in range(0, n - i - 1):
                self._increment_operation()
                if arr[j] > arr[j + 1]:
                    arr[j], arr[j + 1] = arr[j + 1], arr[j]
        
        return arr


def main():
    """Main function to test the algorithm."""
    algorithm = ExampleSort()
    
    # Test with small dataset
    test_data = algorithm.generate_test_data(10)
    print(f"Original data: {{test_data}}")
    
    result = algorithm.benchmark(test_data)
    print(f"Sorted data: {{result.result}}")
    print(f"Execution time: {{result.execution_time:.6f}} seconds")
    print(f"Operations: {{result.operations_count}}")
    print(f"Is sorted: {{algorithm.is_sorted(result.result)}}")
    
    # Benchmark with different sizes
    sizes = [100, 500, 1000, 2000]
    print(f"\\n{{algorithm.name}} Performance Analysis:")
    print("-" * 50)
    
    for size in sizes:
        data = algorithm.generate_test_data(size)
        result = algorithm.benchmark(data)
        
        print(f"Size: {{size:>6}} | Time: {{result.execution_time:.6f}}s | "
              f"Operations: {{result.operations_count:>8}}")


if __name__ == "__main__":
    main()
'''
        (project_path / "src" / "main.py").write_text(main_py)
    
    def _create_data_structure_template(
        self, project_path: Path, name: str, description: str, author: str, email: str
    ) -> None:
        """Create a data structure implementation template."""
        
        main_py = f'''"""
{name.replace("-", " ").title()} - Data Structure Implementation
{description}
"""

from typing import Any, Optional, Iterator, List
from abc import ABC, abstractmethod


class DataStructure(ABC):
    """Base class for all data structure implementations."""
    
    def __init__(self):
        self._size = 0
    
    @property
    def size(self) -> int:
        """Get the current size of the data structure."""
        return self._size
    
    @property
    def is_empty(self) -> bool:
        """Check if the data structure is empty."""
        return self._size == 0
    
    @abstractmethod
    def clear(self) -> None:
        """Clear all elements from the data structure."""
        pass


class ExampleDataStructure(DataStructure):
    """
    Example data structure implementation.
    Replace this with your actual data structure (Stack, Queue, LinkedList, etc.)
    """
    
    def __init__(self):
        super().__init__()
        self._data: List[Any] = []
    
    def add(self, item: Any) -> None:
        """Add an item to the data structure."""
        self._data.append(item)
        self._size += 1
    
    def remove(self) -> Any:
        """Remove and return an item from the data structure."""
        if self.is_empty:
            raise IndexError("Cannot remove from empty data structure")
        
        item = self._data.pop()
        self._size -= 1
        return item
    
    def peek(self) -> Any:
        """Look at the top item without removing it."""
        if self.is_empty:
            raise IndexError("Cannot peek at empty data structure")
        
        return self._data[-1]
    
    def clear(self) -> None:
        """Clear all elements from the data structure."""
        self._data.clear()
        self._size = 0
    
    def __iter__(self) -> Iterator[Any]:
        """Make the data structure iterable."""
        return iter(self._data)
    
    def __len__(self) -> int:
        """Get the length of the data structure."""
        return self._size
    
    def __str__(self) -> str:
        """String representation of the data structure."""
        return f"ExampleDataStructure({{self._data}})"
    
    def __repr__(self) -> str:
        """Developer representation of the data structure."""
        return f"ExampleDataStructure(size={{self._size}}, data={{self._data}})"


def demonstrate_usage():
    """Demonstrate the usage of the data structure."""
    print("Data Structure Demonstration")
    print("=" * 40)
    
    ds = ExampleDataStructure()
    
    # Test basic operations
    print(f"Initial state: {{ds}}")
    print(f"Is empty: {{ds.is_empty}}")
    print(f"Size: {{ds.size}}")
    
    # Add items
    items = [1, 2, 3, "hello", 4.5]
    for item in items:
        ds.add(item)
        print(f"Added {{item}}: {{ds}}")
    
    # Peek at top item
    print(f"\\nTop item (peek): {{ds.peek()}}")
    print(f"Size after peek: {{ds.size}}")
    
    # Remove items
    print("\\nRemoving items:")
    while not ds.is_empty:
        item = ds.remove()
        print(f"Removed {{item}}: {{ds}}")
    
    print(f"\\nFinal state: {{ds}}")
    print(f"Is empty: {{ds.is_empty}}")


def run_performance_tests():
    """Run performance tests on the data structure."""
    import time
    import random
    
    print("\\nPerformance Tests")
    print("=" * 40)
    
    ds = ExampleDataStructure()
    sizes = [1000, 5000, 10000, 20000]
    
    for size in sizes:
        # Test addition performance
        start_time = time.perf_counter()
        for i in range(size):
            ds.add(random.randint(1, 1000))
        add_time = time.perf_counter() - start_time
        
        # Test removal performance
        start_time = time.perf_counter()
        for _ in range(size):
            ds.remove()
        remove_time = time.perf_counter() - start_time
        
        print(f"Size: {{size:>6}} | Add: {{add_time:.6f}}s | Remove: {{remove_time:.6f}}s")


if __name__ == "__main__":
    demonstrate_usage()
    run_performance_tests()
'''
        (project_path / "src" / "main.py").write_text(main_py)
    
    def _create_microservice_template(
        self, project_path: Path, name: str, description: str, author: str, email: str
    ) -> None:
        """Create a microservice template."""
        self._create_web_app_template(project_path, name, description, author, email)
        
        # Add microservice-specific files
        k8s_dir = project_path / "k8s"
        k8s_dir.mkdir(exist_ok=True)
        
        deployment_yaml = f'''apiVersion: apps/v1
kind: Deployment
metadata:
  name: {name}
  labels:
    app: {name}
spec:
  replicas: 3
  selector:
    matchLabels:
      app: {name}
  template:
    metadata:
      labels:
        app: {name}
    spec:
      containers:
      - name: {name}
        image: {name}:latest
        ports: