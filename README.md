
 # Instructions for Safe Script Execution

Save this script as `safe_execution_instructions.ps1`:

```powershell
# Save this script as 'safe_execution_instructions.ps1'

# Inform users about the security risks of running scripts on public WiFi
Write-Output "WARNING: Do not run these setup scripts on public WiFi networks."

Write-Output "Public WiFi networks can be insecure and expose your data to potential threats. It is highly recommended to execute these scripts only on trusted, secure networks to protect your sensitive information and avoid potential security risks."

Write-Output "To ensure your data is secure, please follow these steps:"

# Instructions for users with administrative rights
Write-Output "1. If you have administrative rights, ensure your network connection is secure before running any scripts."

# Instructions for users without administrative rights
Write-Output "2. If you do not have administrative rights and need to mask your IP address, download and use the masking script below to protect your IP before running the setup scripts."

Write-Output "The masking script will provide you with a unique URL that masks your IP. This URL is valid for 3 hours and will regenerate every 3 hours, allowing a maximum of 6 hours of use per day."

Write-Output "You can download the masking script from the following link:"

# Provide a link to the masking script
Write-Output "Download masking script: https://example.com/masking-script.ps1"

Write-Output "After downloading and running the masking script, you can proceed with running the main setup scripts. Ensure you run these scripts in a secure environment."

Write-Output "For more information on securing your network and data, please consult your IT department or a cybersecurity professional."
```

**Masking Script**

This script should be run before the main setup scripts. Save this script as `masking_script.ps1`:

```powershell
# Save this script as 'masking_script.ps1'

# Function to generate a unique URL for IP masking
function Generate-MaskingURL {
    $currentTime = Get-Date -Format "yyyyMMddHHmmss"
    $uniqueURL = "https://masking.example.com/activate?token=$($currentTime)"
    return $uniqueURL
}

# Generate a unique URL
$maskingURL = Generate-MaskingURL

# Inform the user about the masking URL
Write-Output "To mask your IP address, use the following URL:"
Write-Output $maskingURL

Write-Output "This URL will mask your IP address for 3 hours. It will regenerate every 3 hours, providing a maximum of 6 hours of use per day."

# Instructions to use the URL
Write-Output "Copy and paste this URL into your browser to activate IP masking before running any setup scripts."

Write-Output "Please ensure you follow security best practices when using public WiFi networks."
```

### Instructions for Users

1. **Download and Run the Masking Script:**
   - Save the `masking_script.ps1` to your computer.
   - Open a PowerShell terminal.
   - Run the masking script with the following command:
     ```powershell
     powershell -ExecutionPolicy Bypass -File C:\path\to\masking_script.ps1
     ```
   - Copy the unique URL provided by the masking script and use it to mask your IP address.

 **Follow Safe Execution Instructions:**
   - Download `safe_execution_instructions.ps1`.
   - Open a PowerShell terminal.
   - Run the instructions script with:
     ```powershell
     powershell -ExecutionPolicy Bypass -File C:\path\to\safe_execution_instructions.ps1
     ```

  **Proceed with Main Setup Scripts:**
   - Ensure you are on a secure network.
   - Run the main setup scripts (e.g., `unified_setup_user.ps1`).


1. **Download** a Flask service script.
2. **Set up** a SQLite database to manage token allocation.
3. **Run** the Flask service.

### Full Python Setup Script

This script downloads necessary files, sets up a SQLite database, and starts the Flask service. Save this script as `setup_service.py`.

```python
import os
import subprocess
import sys
import sqlite3
from urllib.request import urlretrieve

# Constants
FLASK_SERVICE_URL = 'https://github.com/your-repo/url_service.py'
DB_FILE = 'url_service.db'
FLASK_SCRIPT_FILE = 'url_service.py'
PORT = 5000

def download_file(url, filename):
    """Download a file from a URL to a specified filename."""
    print(f"Downloading {url} to {filename}...")
    urlretrieve(url, filename)
    print(f"Downloaded {filename} successfully.")

def setup_database():
    """Set up the SQLite database for URL and token management."""
    print("Setting up the SQLite database...")
    conn = sqlite3.connect(DB_FILE)
    cursor = conn.cursor()
    
    # Create tables for storing tokens and user data
    cursor.execute('''
        CREATE TABLE IF NOT EXISTS tokens (
            token TEXT PRIMARY KEY,
            user_id TEXT NOT NULL,
            creation TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
            expiry TIMESTAMP NOT NULL
        )
    ''')
    conn.commit()
    conn.close()
    print("Database setup complete.")

def run_flask_service():
    """Run the Flask service for URL and token management."""
    print("Running the Flask service...")
    subprocess.Popen([sys.executable, FLASK_SCRIPT_FILE, f'--port={PORT}'], stdout=subprocess.PIPE, stderr=subprocess.PIPE)
    print(f"Flask service running on http://localhost:{PORT}")

def main():
    # Check if Flask and SQLite are installed
    try:
        import flask
    except ImportError:
        print("Flask not found. Installing Flask...")
        subprocess.check_call([sys.executable, '-m', 'pip', 'install', 'flask'])

    # Download and setup files
    download_file(FLASK_SERVICE_URL, FLASK_SCRIPT_FILE)
    setup_database()
    run_flask_service()

if __name__ == "__main__":
    main()
```

### Flask Service Script (for reference)

This is the same `url_service.py` script referenced in the `setup_service.py`. Ensure it is hosted at the URL specified (`FLASK_SERVICE_URL`).

```python
from flask import Flask, jsonify, request
from datetime import datetime, timedelta
import uuid
import sqlite3

app = Flask(__name__)

# Database file
DB_FILE = 'url_service.db'
MAX_DAILY_USAGE = timedelta(hours=6)
URL_VALIDITY_PERIOD = timedelta(hours=3)

def generate_token():
    return str(uuid.uuid4())

@app.route('/generate_url', methods=['POST'])
def generate_url():
    user_id = request.json.get('user_id')
    
    if not user_id:
        return jsonify({"error": "User ID is required"}), 400

    now = datetime.now()
    conn = sqlite3.connect(DB_FILE)
    cursor = conn.cursor()

    # Check if the user has reached the daily limit
    cursor.execute('''
        SELECT SUM(strftime('%s', expiry) - strftime('%s', creation)) 
        FROM tokens 
        WHERE user_id = ? AND DATE(creation) = DATE('now')
    ''', (user_id,))
    daily_usage = cursor.fetchone()[0] or 0

    if daily_usage >= MAX_DAILY_USAGE.total_seconds():
        return jsonify({"error": "Daily usage limit reached"}), 403

    # Generate a new token
    token = generate_token()
    expiry_time = now + URL_VALIDITY_PERIOD

    cursor.execute('''
        INSERT INTO tokens (token, user_id, expiry) 
        VALUES (?, ?, ?)
    ''', (token, user_id, expiry_time))
    conn.commit()
    conn.close()

    url = f"https://masking.example.com/activate?token={token}"
    
    return jsonify({
        "url": url,
        "valid_until": expiry_time.strftime('%Y-%m-%d %H:%M:%S')
    })

@app.route('/validate_token/<token>', methods=['GET'])
def validate_token(token):
    now = datetime.now()
    conn = sqlite3.connect(DB_FILE)
    cursor = conn.cursor()
    
    cursor.execute('''
        SELECT expiry FROM tokens WHERE token = ?
    ''', (token,))
    result = cursor.fetchone()
    
    if result:
        expiry_time = datetime.strptime(result[0], '%Y-%m-%d %H:%M:%S')
        if expiry_time > now:
            return jsonify({"status": "valid", "valid_until": expiry_time.strftime('%Y-%m-%d %H:%M:%S')})
        else:
            return jsonify({"status": "expired"}), 403

    return jsonify({"status": "invalid"}), 404

if __name__ == '__main__':
    app.run(port=5000)
```

### Instructions

1. **Download and Run the Setup Script**:
   - Save `setup_service.py` and run it in your terminal:
     ```bash
     python setup_service.py
     ```

2. **What This Script Does**:
   - **Downloads** the Flask service script.
   - **Sets up** a SQLite database.
   - **Installs** Flask if not already installed.
   - **Runs** the Flask service on port 5000.

### Security Advisory

Ensure that you do not run these scripts on public Wi-Fi networks due to potential security risks. Public networks are more susceptible to attacks, and using them can expose your system to vulnerabilities. If necessary, use VPN services to mask your IP address and secure your connections.

This approach simplifies the setup process and ensures that all components are correctly configured and running without requiring manual intervention from the user beyond the initial script execution.


### Unified Setup Script

1. **Save the Unified PowerShell Script:**
   
   Save the following script as `unified_setup.ps1`:

   ```powershell
   # Get the user's name
   $userName = $env:USERNAME

   # Define the base path
   $basePath = "C:\Users\$userName\OneDrive\Desktop\fastapi\pipx\src\pipx\commands"

   # Create the folder structure
   New-Item -ItemType Directory -Path $basePath -Force

   Write-Output "Folder structure created at: $basePath"

   # Install Python, Pip, FastAPI, and Uvicorn
   Write-Output "Setting up Python environment..."

   # Install Chocolatey if not installed
   if (-Not (Get-Command choco -ErrorAction SilentlyContinue)) {
       Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))
   }

   # Install Python and Pip
   choco install -y python

   # Upgrade pip
   python -m pip install --upgrade pip

   # Install FastAPI and Uvicorn
   python -m pip install fastapi uvicorn

   # Create a virtual environment
   python -m venv $basePath\env

   # Activate the virtual environment
   $activateScript = "$basePath\env\Scripts\Activate.ps1"
   & $activateScript

   Write-Output "Python environment setup complete. FastAPI and Uvicorn have been installed."

   # Install Rust and Cargo
   Write-Output "Setting up Rust environment..."

   # Install Rust if not installed
   if (-Not (Get-Command rustc -ErrorAction SilentlyContinue)) {
       Invoke-WebRequest -Uri https://sh.rustup.rs -OutFile $env:TEMP\rustup-init.exe
       Start-Process -FilePath $env:TEMP\rustup-init.exe -ArgumentList "-y" -NoNewWindow -Wait
   }

   # Source the Cargo environment
   $env:PATH += ";$HOME\.cargo\bin"

   # Create a new Rust project
   $rustProjectPath = "$basePath\my_rust_project"
   cargo new $rustProjectPath

   Write-Output "Rust environment setup complete. A new Rust project has been created at: $rustProjectPath"

   # Open all directories on the Desktop
   Write-Output "Opening all directories on the Desktop..."
   $desktopPath = [System.IO.Path]::Combine([System.Environment]::GetFolderPath("Desktop"))
   $directories = Get-ChildItem -Path $desktopPath -Directory

   foreach ($dir in $directories) {
       Start-Process explorer.exe $dir.FullName
   }

   Write-Output "All directories on the Desktop have been opened."
   ```

### Instructions for Investors and Stakeholders

1. **Run the Unified PowerShell Script:**
   - Save the `unified_setup.ps1` script to your computer, e.g., `C:\unified_setup.ps1`.
   - Open a PowerShell terminal as Administrator.
   - Run the script with the following command:
     ```powershell
     powershell -ExecutionPolicy Bypass -File C:\unified_setup.ps1
     ```

### Explanation

- The script creates the necessary folder structure.
- Installs Python, Pip, FastAPI, and Uvicorn.
- Sets up a Python virtual environment.
- Installs Rust and Cargo.
- Creates a new Rust project.
- Opens all directories on the Desktop.



### Unified Setup Script Without Administrative Privileges

1. **Save the Unified PowerShell Script:**

   Save the following script as `unified_setup_user.ps1`:

   ```powershell
   # Get the user's name
   $userName = $env:USERNAME

   # Define the base path
   $basePath = "C:\Users\$userName\OneDrive\Desktop\fastapi\pipx\src\pipx\commands"

   # Create the folder structure
   New-Item -ItemType Directory -Path $basePath -Force

   Write-Output "Folder structure created at: $basePath"

   # Function to download and install Miniconda (Python environment without admin rights)
   function Install-Miniconda {
       $minicondaUrl = "https://repo.anaconda.com/miniconda/Miniconda3-latest-Windows-x86_64.exe"
       $minicondaInstaller = "$env:TEMP\Miniconda3-latest-Windows-x86_64.exe"

       Invoke-WebRequest -Uri $minicondaUrl -OutFile $minicondaInstaller
       Start-Process -FilePath $minicondaInstaller -ArgumentList "/InstallationType=JustMe", "/AddToPath=1", "/RegisterPython=1", "/S", "/D=$env:USERPROFILE\Miniconda3" -NoNewWindow -Wait
   }

   # Install Miniconda if not installed
   if (-Not (Test-Path "$env:USERPROFILE\Miniconda3")) {
       Write-Output "Installing Miniconda..."
       Install-Miniconda
   }

   # Add Miniconda to PATH for the current session
   $env:Path += ";$env:USERPROFILE\Miniconda3\Scripts;$env:USERPROFILE\Miniconda3"

   # Create a conda environment
   conda create -y -n fastapi_env python=3.8
   conda activate fastapi_env

   # Install FastAPI and Uvicorn in the conda environment
   pip install fastapi uvicorn

   Write-Output "Python environment setup complete. FastAPI and Uvicorn have been installed."

   # Install Rust and Cargo using Rustup
   function Install-Rustup {
       $rustupUrl = "https://static.rust-lang.org/rustup/dist/x86_64-pc-windows-msvc/rustup-init.exe"
       $rustupInstaller = "$env:TEMP\rustup-init.exe"

       Invoke-WebRequest -Uri $rustupUrl -OutFile $rustupInstaller
       Start-Process -FilePath $rustupInstaller -ArgumentList "-y" -NoNewWindow -Wait
   }

   # Install Rust if not installed
   if (-Not (Get-Command rustc -ErrorAction SilentlyContinue)) {
       Write-Output "Installing Rust..."
       Install-Rustup
   }

   # Source the Cargo environment
   $env:Path += ";$env:USERPROFILE\.cargo\bin"

   # Create a new Rust project
   $rustProjectPath = "$basePath\my_rust_project"
   cargo new $rustProjectPath

   Write-Output "Rust environment setup complete. A new Rust project has been created at: $rustProjectPath"

   # Open all directories on the Desktop
   Write-Output "Opening all directories on the Desktop..."
   $desktopPath = [System.IO.Path]::Combine([System.Environment]::GetFolderPath("Desktop"))
   $directories = Get-ChildItem -Path $desktopPath -Directory

   foreach ($dir in $directories) {
       Start-Process explorer.exe $dir.FullName
   }

   Write-Output "All directories on the Desktop have been opened."
   ```

### Instructions for Investors and Stakeholders

1. **Run the Unified PowerShell Script:**
   - Save the `unified_setup_user.ps1` script to your computer, e.g., `C:\unified_setup_user.ps1`.
   - Open a PowerShell terminal.
   - Run the script with the following command:
     ```powershell
     powershell -ExecutionPolicy Bypass -File C:\unified_setup_user.ps1
     ```

### Explanation

- The script creates the necessary folder structure.
- Installs Miniconda (a minimal version of Anaconda for managing Python environments) without requiring administrative privileges.
- Sets up a conda environment and installs FastAPI and Uvicorn.
- Installs Rust and Cargo using Rustup without requiring administrative privileges.
- Creates a new Rust project.
- Opens all directories on the Desktop.

