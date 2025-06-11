# 🚀 Day 1: Complete Development Environment Setup

## 📋 Today's Mission
Set up your complete development environment and get all essential tools running smoothly.

## ⏰ Time Allocation (6-8 hours total)
- **Morning (2-3 hours):** Python & Git Setup
- **Afternoon (2-3 hours):** VS Code & Extensions
- **Evening (2 hours):** Docker & Database Setup

---

## 🐍 Step 1: Python Environment Setup (45-60 mins)

### Install Python 3.11.7 (Official Installer)
1. **Download Python 3.11.7:**
   - Visit: https://www.python.org/downloads/windows/
   - Download "Python 3.11.7" (Windows installer 64-bit)

2. **Install with correct settings:**
   - ✅ Check "Add Python to PATH"
   - ✅ Check "Install for all users" (if admin)
   - Choose "Customize installation"
   - ✅ Check all optional features
   - ✅ Check "Add Python to environment variables"
   - ✅ Check "Install Python 3.11 for all users"

3. **Verify installation:**
```cmd
# Open Command Prompt or PowerShell
python --version
# Should show Python 3.11.7

# Check pip
pip --version
```

### Alternative: Use Python Launcher for Windows
```cmd
# Install multiple Python versions side by side
py -3.11 --version  # Use specific version
py -0p              # List all installed versions
```

### Install Poetry for dependency management
```powershell
# Method 1: Using pip (Recommended for Windows)
pip install --upgrade pip
pip install poetry

# Method 2: Using PowerShell (Alternative)
# (Invoke-WebRequest -Uri https://install.python-poetry.org -UseBasicParsing).Content | python -

# Verify installation
poetry --version

# Configure Poetry to create virtual envs in project directory (optional)
poetry config virtualenvs.in-project true
```

**✅ Checkpoint:** Python 3.11.7 and Poetry are installed and working

---

## 🔧 Step 2: Git Configuration (15-20 mins)

### Install Git for Windows
1. **Download Git:**
   - Visit: https://git-scm.com/download/win
   - Download the latest version for Windows

2. **Install with recommended settings:**
   - Use default options, but pay attention to:
   - ✅ Git Bash + Git GUI
   - ✅ Use Git from the Windows Command Prompt
   - ✅ Use the native Windows Secure Channel library
   - ✅ Checkout Windows-style, commit Unix-style line endings

### Configure Git globally
```cmd
# Open Command Prompt, PowerShell, or Git Bash
git config --global user.name "Your Full Name"
git config --global user.email "your.email@example.com"
git config --global init.defaultBranch main

# Windows-specific: Handle line endings
git config --global core.autocrlf true

# Verify configuration
git config --list
```

### Install GitHub CLI (Optional but recommended)
1. **Download GitHub CLI:**
   - Visit: https://cli.github.com/
   - Download the Windows installer (.msi)
   - Install with default settings

2. **Authenticate:**
```cmd
# After installation
gh auth login
# Follow the interactive prompts
```

### Alternative: GitHub Desktop (User-friendly option)
- Download from: https://desktop.github.com/
- Great visual interface for Git operations

**✅ Checkpoint:** Git is configured and GitHub CLI is authenticated

---

## 💻 Step 3: VS Code Setup (45-60 mins)

### Download and install VS Code
- Visit: https://code.visualstudio.com/
- Download for your operating system
- Install with default settings

### Essential Extensions Installation
Create this extensions configuration:

```json
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

### Install extensions via command palette
1. Open VS Code
2. Press `Ctrl+Shift+P` (or `Cmd+Shift+P` on Mac)
3. Type "Extensions: Install Extensions"
4. Search for and install each extension listed above

### Configure VS Code settings
Open VS Code settings (Ctrl+,) and add these configurations:

**Via Settings UI:**
- Python › Default Interpreter Path: `python`
- Python › Linting: Enabled ✅
- Python › Linting › Mypy Enabled ✅  
- Python › Formatting › Provider: `black`
- Editor › Format On Save ✅

**Via settings.json (Ctrl+Shift+P → "Preferences: Open Settings JSON"):**
```json
{
  "python.defaultInterpreterPath": "python",
  "python.linting.enabled": true,
  "python.linting.pylintEnabled": false,
  "python.linting.mypyEnabled": true,
  "[python]": {
    "editor.defaultFormatter": "ms-python.black-formatter",
    "editor.formatOnSave": true,
    "editor.codeActionsOnSave": {
      "source.organizeImports": true
    }
  },
  "terminal.integrated.defaultProfile.windows": "PowerShell"
}
```

**✅ Checkpoint:** VS Code is set up with all essential extensions

---

## 🐳 Step 4: Docker Installation (30-45 mins)

### Install Docker Desktop for Windows
1. **System Requirements Check:**
   - Windows 10/11 (64-bit)
   - Enable Hyper-V and Containers Windows features
   - BIOS-level hardware virtualization support

2. **Download and Install:**
   - Visit: https://www.docker.com/products/docker-desktop/
   - Download "Docker Desktop for Windows"
   - Run installer as Administrator
   - **Important:** Choose "Use WSL 2 instead of Hyper-V" when prompted

3. **Post-installation:**
   - Restart your computer
   - Start Docker Desktop from Start Menu
   - Accept the service agreement
   - Complete the tutorial (optional)

### Verify Docker installation
```cmd
# Open Command Prompt or PowerShell
docker --version
docker-compose --version

# Test Docker with hello-world
docker run hello-world
```

### Windows-specific Docker tips
```cmd
# If using WSL 2, you can also run Docker from WSL
wsl --list --verbose  # Check WSL distributions

# Docker Desktop integrates with WSL 2 automatically
```

**✅ Checkpoint:** Docker is installed and running

---

## 🗄️ Step 5: Database Setup with Docker (45-60 mins)

### Start PostgreSQL container
```bash
docker run --name postgres-dev \
  -e POSTGRES_PASSWORD=devpassword \
  -e POSTGRES_DB=devdb \
  -p 5432:5432 -d postgres:15
```

### Start Redis container
```bash
docker run --name redis-dev -p 6379:6379 -d redis:latest
```

### Start MongoDB container
```bash
docker run --name mongo-dev -p 27017:27017 -d mongo:latest
```

### Verify all databases are running
```cmd
# Check running containers
docker ps

# Test PostgreSQL connection
docker exec -it postgres-dev psql -U postgres -d devdb
# Type \q to exit

# Test Redis connection  
docker exec -it redis-dev redis-cli ping
# Should return PONG

# Test MongoDB connection
docker exec -it mongo-dev mongosh
# Type exit to quit
```

### Windows-specific database notes
- Database data persists in Docker volumes
- Access databases via `localhost:PORT` from Windows applications
- Use Docker Desktop dashboard for easy container management

**✅ Checkpoint:** All databases are running and accessible

---

## 📁 Step 6: Create Development Workspace (20-30 mins)

### Create your main development directory
```cmd
# Create main workspace in your user directory
mkdir C:\Development
cd C:\Development

# Create subdirectories for organization
mkdir projects learning tools playground
```

### Alternative: Use PowerShell for better experience
```powershell
# Create development workspace
New-Item -ItemType Directory -Path "C:\Development" -Force
Set-Location "C:\Development"

# Create subdirectories
@("projects", "learning", "tools", "playground") | ForEach-Object { 
    New-Item -ItemType Directory -Path $_ -Force 
}
```

### Set up PowerShell aliases (optional but recommended)
Create/edit your PowerShell profile:
```powershell
# Check if profile exists
Test-Path $PROFILE

# Create profile if it doesn't exist
if (!(Test-Path $PROFILE)) { New-Item -Type File -Path $PROFILE -Force }

# Edit profile (opens in notepad)
notepad $PROFILE
```

Add these aliases to your profile:
```powershell
# Development aliases
function dev { Set-Location "C:\Development" }
function ll { Get-ChildItem -Force }
function gs { git status }
function gp { git push }
function gpu { git pull }
function dps { docker ps }

# Quick navigation
function projects { Set-Location "C:\Development\projects" }
function learning { Set-Location "C:\Development\learning" }
```

### Windows Terminal (Highly Recommended)
Install Windows Terminal from Microsoft Store for better terminal experience:
- Multiple tabs
- PowerShell, Command Prompt, and WSL in one place
- Customizable themes
- Better font rendering

**✅ Checkpoint:** Development workspace is organized and ready

---

## 🔍 Step 7: Verification & Testing (30 mins)

### Create a test project to verify everything works
```cmd
cd C:\Development\playground
mkdir test-setup
cd test-setup

# Initialize with Poetry
poetry init --no-interaction --name test-setup --version 0.1.0

# Add dependencies
poetry add fastapi uvicorn

# Create a simple test file
echo from fastapi import FastAPI > main.py
echo app = FastAPI() >> main.py
echo. >> main.py
echo @app.get("/") >> main.py
echo def read_root(): >> main.py
echo     return {"message": "Windows setup successful!"} >> main.py
echo. >> main.py
echo if __name__ == "__main__": >> main.py
echo     import uvicorn >> main.py
echo     uvicorn.run(app, host="0.0.0.0", port=8000) >> main.py

# Alternative: Create file in VS Code
code main.py
# Then paste the content manually
```

**Content for main.py:**
```python
from fastapi import FastAPI

app = FastAPI()

@app.get("/")
def read_root():
    return {"message": "Windows setup successful!"}

if __name__ == "__main__":
    import uvicorn
    uvicorn.run(app, host="0.0.0.0", port=8000)
```

```cmd
# Test the setup
poetry install
poetry run python main.py
```

Visit http://localhost:8000 in your browser - you should see the JSON message!

---

## 📝 Day 1 Completion Checklist

- [ ] Python 3.11.7 installed via pyenv
- [ ] Poetry installed and working
- [ ] Git configured with your credentials
- [ ] GitHub CLI authenticated
- [ ] VS Code installed with all essential extensions
- [ ] Docker Desktop installed and running
- [ ] PostgreSQL container running on port 5432
- [ ] Redis container running on port 6379
- [ ] MongoDB container running on port 27017
- [ ] Development workspace created and organized
- [ ] Test project successfully created and runs
- [ ] All tools verified and working

---

## 🎯 Success Metrics
- ✅ All installations completed without errors
- ✅ Test project runs successfully
- ✅ All databases accessible
- ✅ VS Code extensions active and working
- ✅ Ready to start Day 2 activities

---

## 🚨 Troubleshooting Common Windows Issues

### Python/Poetry issues
- **Python not found:** Ensure "Add Python to PATH" was checked during installation
- **Permission errors:** Run Command Prompt as Administrator
- **Poetry installation fails:** Try `py -m pip install poetry` instead

### Git issues
- **Command not found:** Restart Command Prompt after Git installation
- **Line ending warnings:** Normal on Windows, handled by `core.autocrlf true`
- **Authentication issues:** Use GitHub Desktop or `gh auth login`

### Docker issues
- **Docker Desktop won't start:** Enable Hyper-V and Containers Windows features
- **WSL 2 errors:** Update Windows and install WSL 2 kernel update
- **Port conflicts:** Check if other applications are using ports 5432, 6379, 27017
- **Permission denied:** Run Docker Desktop as Administrator

### VS Code issues
- **Python interpreter not found:** Use Ctrl+Shift+P → "Python: Select Interpreter"
- **Extensions not working:** Restart VS Code after installing extensions
- **Terminal issues:** Set PowerShell as default terminal in VS Code

### Windows-specific tips
- **Use PowerShell instead of Command Prompt** for better experience
- **Install Windows Terminal** from Microsoft Store
- **Consider WSL 2** for Linux-like development experience
- **Run as Administrator** when facing permission issues

---

## 🎉 Celebration Time!
Once you complete this checklist, you've successfully set up a professional-grade development environment! 

**Next Up:** Tomorrow (Day 2) we'll create your master learning repository and set up project templates.

---

## 💡 Pro Tips
- Take screenshots of your setup for future reference
- Document any customizations you make
- Consider using a dotfiles repository for your configurations
- Join the VS Code and Python communities for tips and tricks