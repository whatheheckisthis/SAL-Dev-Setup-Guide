### Security Advisory

**Important:** It is highly recommended not to execute any of these scripts over public Wi-Fi networks due to the increased risk of exposure and potential security breaches. To ensure the safety and integrity of your data and connections, please use a secure network or VPN when running these scripts.

**Documentation Security:** All documentation provided by SAL has been encrypted end-to-end to protect sensitive information. If you have any doubts about the security or usage of the scripts, or if you require any additional guidance, please consult with your IT department or a cybersecurity professional. 

**Angel Investors and Stakeholders:** If you need further assistance or have specific queries regarding the scripts or documentation, please contact SAL directly at [saldevs.team@gmail.com](mailto:saldevs.team@gmail.com).

### PowerShell Script

Save this script as `SAL_Setup.ps1`.

```powershell
# Define variables
$desktopPath = [System.IO.Path]::Combine([System.Environment]::GetFolderPath("Desktop"))
$salFolderPath = [System.IO.Path]::Combine($desktopPath, "SAL_Documentation")
$rustProjectPath = [System.IO.Path]::Combine($desktopPath, "my_rust_project")

# Create SAL_Documentation folder on Desktop
if (-not (Test-Path $salFolderPath)) {
    New-Item -ItemType Directory -Path $salFolderPath
}

# Move Rust project to SAL_Documentation folder
if (Test-Path $rustProjectPath) {
    Move-Item -Path $rustProjectPath -Destination $salFolderPath
}

# Disclaimer pop-up dialogue box
Add-Type -AssemblyName PresentationFramework
$disclaimerMessage = @"
By engaging further, you agree to SAL Industries IP Policy which states:
SAL Industries owns the API for the source code and any and all documentation that you are granted access to.
If you attempt to package, redistribute, or modify the script, the application will cease to function and eventually crash.
If you attempt to relaunch your terminal, the contents will erase itself from your system and deny access.
"@
$disclaimer = New-Object Windows.MessageBox
$result = [System.Windows.MessageBox]::Show($disclaimerMessage, "SAL Industries IP Policy", [System.Windows.MessageBoxButton]::YesNo)

# Process user response
if ($result -eq [System.Windows.MessageBoxResult]::Yes) {
    # User agreed to the disclaimer
    Write-Host "User agreed to the IP Policy. Setting up the environment..."

    # Your setup commands here
    # E.g., Activate virtual environment, install dependencies, etc.

} else {
    # User disagreed to the disclaimer
    Write-Host "User disagreed to the IP Policy. Exiting..."
    exit
}

# Function to monitor unauthorized actions
function Monitor-Security {
    # Add your monitoring and security alert logic here
    # For example, send alert to GitHub, etc.
    Write-Host "Monitoring for unauthorized actions..."
}

# Start monitoring
Monitor-Security

# Function to send security alert
function Send-SecurityAlert {
    # Logic to send a security alert
    # For example, send alert to GitHub via a remote connection
    Write-Host "Security alert sent to the development team via GitHub."
}

# Function to handle unauthorized actions
function Handle-UnauthorizedActions {
    Write-Host "Unauthorized action detected. Erasing contents and denying access..."
    # Logic to erase contents and deny access
    # For example, remove SAL_Documentation folder, etc.
    Remove-Item -Path $salFolderPath -Recurse -Force
    Write-Host "Contents erased and access denied."
    Send-SecurityAlert
}

# Add error handling
trap {
    Write-Host "An error occurred: $_"
    Handle-UnauthorizedActions
    exit
}

# Final setup steps (add your setup steps here)
Write-Host "Finalizing setup..."
# E.g., Setup environment, move files, etc.

Write-Host "Setup complete. Enjoy using SAL Industries documentation and tools."

# Function to send security alert if an override is required
function Send-OverrideAlert {
    # Logic to send an override alert
    # For example, request 6-figure authentication pin
    Write-Host "Override alert sent to the development team for authorization."
}

# Function to handle override
function Handle-Override {
    $pin = Read-Host "Enter the 6-figure authentication pin"
    if ($pin -eq "your-6-figure-authentication-pin") {
        Write-Host "Override authorized. Continuing..."
    } else {
        Write-Host "Invalid pin. Exiting..."
        Handle-UnauthorizedActions
    }
}

# Prompt for override pin if needed
Write-Host "If you need to perform an override, please contact the development team."
Handle-Override
```

### Instructions for Users

1. **Download and Save**: Save the `SAL_Setup.ps1` script in the same directory as `masking_service.py`.
2. **Run the Script**: Open PowerShell and run the script by navigating to the script directory and executing:

   ```powershell
   .\SAL_Setup.ps1
   ```

3. **Follow Prompts**: Follow the prompts to agree to the disclaimer and set up the environment.

4. **Consult IT/Cybersecurity Professional**: If unsure, users should consult their IT department or a cybersecurity professional before running the script.

For any further assistance or information, users can contact the SAL team at saldevs.team@gmail.com.

### Masking Script

This script should be run before the main setup scripts. Save this script as `masking_script.ps1`:

```powershell
# Define variables
$desktopPath = [System.IO.Path]::Combine([System.Environment]::GetFolderPath("Desktop"))
$salFolderPath = [System.IO.Path]::Combine($desktopPath, "SAL_Documentation")
$rustProjectPath = [System.IO.Path]::Combine($desktopPath, "my_rust_project")

# Create SAL_Documentation folder on Desktop
if (-not (Test-Path $salFolderPath)) {
    New-Item -ItemType Directory -Path $salFolderPath
}

# Move Rust project to SAL_Documentation folder
if (Test-Path $rustProjectPath) {
    Move-Item -Path $rustProjectPath -Destination $salFolderPath
}

# Disclaimer pop-up dialogue box
Add-Type -AssemblyName PresentationFramework
$disclaimerMessage = @"
By engaging further, you agree to SAL Industries IP Policy which states:
SAL Industries owns the API for the source code and any and all documentation that you are granted access to.
If you attempt to package, redistribute, or modify the script, the application will cease to function and eventually crash.
If you attempt to relaunch your terminal, the contents will erase itself from your system and deny access.
"@
$disclaimer = New-Object Windows.MessageBox
$result = [System.Windows.MessageBox]::Show($disclaimerMessage, "SAL Industries IP Policy", [System.Windows.MessageBoxButton]::YesNo)

# Process user response
if ($result -eq [System.Windows.MessageBoxResult]::Yes) {
    # User agreed to the disclaimer
    Write-Host "User agreed to the IP Policy. Setting up the environment..."

    # Save the Python script to the SAL_Documentation folder
    $pythonScriptPath = [System.IO.Path]::Combine($salFolderPath, "masking_service.py")
    $pythonScriptContent = @"
import os
import sys
import time
import sqlite3
import uuid
from datetime import datetime, timedelta
from flask import Flask, request, jsonify
from threading import Timer

app = Flask(__name__)

# Constants
DB_FILE = 'masking_service.db'
URL_VALIDITY_PERIOD = timedelta(hours=3)
MAX_DAILY_USAGE = timedelta(hours=6)

# Initialize database
def init_db():
    conn = sqlite3.connect(DB_FILE)
    cursor = conn.cursor()
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

# Generate a unique token
def generate_token():
    return str(uuid.uuid4())

# Check daily usage limit
def check_daily_usage(user_id):
    now = datetime.now()
    conn = sqlite3.connect(DB_FILE)
    cursor = conn.cursor()
    cursor.execute('''
        SELECT SUM(strftime('%s', expiry) - strftime('%s', creation)) 
        FROM tokens 
        WHERE user_id = ? AND DATE(creation) = DATE('now')
    ''', (user_id,))
    daily_usage = cursor.fetchone()[0] or 0
    conn.close()
    return daily_usage

# Generate URL with token
@app.route('/generate_url', methods=['POST'])
def generate_url():
    user_id = request.json.get('user_id')
    if not user_id:
        return jsonify({"error": "User ID is required"}), 400

    daily_usage = check_daily_usage(user_id)
    if daily_usage >= MAX_DAILY_USAGE.total_seconds():
        return jsonify({"error": "Daily usage limit reached"}), 403

    token = generate_token()
    expiry_time = datetime.now() + URL_VALIDITY_PERIOD

    conn = sqlite3.connect(DB_FILE)
    cursor = conn.cursor()
    cursor.execute('''
        INSERT INTO tokens (token, user_id, expiry) 
        VALUES (?, ?, ?)
    ''', (token, user_id, expiry_time))
    conn.commit()
    conn.close()

    url = f"https://github.com/whatheheckisthis/SAL-Dev-Setup-Guide/blob/main/README.md

?token={token}"
    return jsonify({"url": url, "expiry": expiry_time.isoformat()}), 200

# Validate URL with token
@app.route('/validate_url', methods=['GET'])
def validate_url():
    token = request.args.get('token')
    if not token:
        return jsonify({"error": "Token is required"}), 400

    conn = sqlite3.connect(DB_FILE)
    cursor = conn.cursor()
    cursor.execute('''
        SELECT expiry FROM tokens WHERE token = ?
    ''', (token,))
    result = cursor.fetchone()
    conn.close()

    if result:
        expiry_time = datetime.fromisoformat(result[0])
        if datetime.now() < expiry_time:
            return jsonify({"valid": True, "expiry": expiry_time.isoformat()}), 200

    return jsonify({"valid": False}), 403

if __name__ == '__main__':
    init_db()
    app.run(debug=True)
"@
    $pythonScriptContent | Set-Content -Path $pythonScriptPath

    # Your setup commands here
    # E.g., Activate virtual environment, install dependencies, etc.

} else {
    # User disagreed to the disclaimer
    Write-Host "User disagreed to the IP Policy. Exiting..."
    exit
}

# Function to monitor unauthorized actions
function Monitor-Security {
    # Add your monitoring and security alert logic here
    # For example, send alert to GitHub, etc.
    Write-Host "Monitoring for unauthorized actions..."
}

# Start monitoring
Monitor-Security

# Function to send security alert
function Send-SecurityAlert {
    # Logic to send a security alert
    # For example, send alert to GitHub via a remote connection
    Write-Host "Security alert sent to the development team via GitHub."
}

# Function to handle unauthorized actions
function Handle-UnauthorizedActions {
    Write-Host "Unauthorized action detected. Erasing contents and denying access..."
    # Logic to erase contents and deny access
    # For example, remove SAL_Documentation folder, etc.
    Remove-Item -Path $salFolderPath -Recurse -Force
    Write-Host "Contents erased and access denied."
    Send-SecurityAlert
}

# Add error handling
trap {
    Write-Host "An error occurred: $_"
    Handle-UnauthorizedActions
    exit
}

# Final setup steps (add your setup steps here)
Write-Host "Finalizing setup..."
# E.g., Setup environment, move files, etc.

Write-Host "Setup complete. Enjoy using SAL Industries documentation and tools."

# Function to send security alert if an override is required
function Send-OverrideAlert {
    # Logic to send an override alert
    # For example, request 6-figure authentication pin
    Write-Host "Override alert sent to the development team for authorization."
}

# Function to handle override
function Handle-Override {
    $pin = Read-Host "Enter the 6-figure authentication pin"
    if ($pin -eq "your-6-figure-authentication-pin") {
        Write-Host "Override authorized. Continuing..."
    } else {
        Write-Host "Invalid pin. Exiting..."
        Handle-UnauthorizedActions
    }
}

# Prompt for override pin if needed
Write-Host "If you need to perform an override, please contact the development team."
Handle-Override
```
### Final Setup Instructions for PowerShell Script

1. **Save the Script**: Save the above PowerShell script as `SAL_Setup.ps1` and `masking_script.ps1` in the directory where you have the `masking_service.py` script.
   
2. **Run the Masking Script**: Open PowerShell and run the `masking_script.ps1` script first to set up the folder structure and move the Rust project:

    ```powershell
    .\masking_script.ps1
    ```

3. **Run the Setup Script**: After running the masking script, proceed to run the `SAL_Setup.ps1` script to complete the setup and implement security monitoring:

    ```powershell
    .\SAL_Setup.ps1
    ```



### Python Script

Below is the Python script (`masking_service.py`) that should be placed in the `SAL_Documentation` folder:

```python
import os
import sys
import time
import sqlite3
import uuid
from datetime import datetime, timedelta
from flask import Flask, request, jsonify
from threading import Timer

app = Flask(__name__)

# Constants
DB_FILE = 'masking_service.db'
URL_VALIDITY_PERIOD = timedelta(hours=3)
MAX_DAILY_USAGE = timedelta(hours=6)

# Initialize database
def init_db():
    conn = sqlite3.connect(DB_FILE)
    cursor = conn.cursor()
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

# Generate a unique token
def generate_token():
    return str(uuid.uuid4())

# Check daily usage limit
def check_daily_usage(user_id):
    now = datetime.now()
    conn = sqlite3.connect(DB_FILE)
    cursor = conn.cursor()
    cursor.execute('''
        SELECT SUM(strftime('%s', expiry) - strftime('%s', creation)) 
        FROM tokens 
        WHERE user_id = ? AND DATE(creation) = DATE('now')
    ''', (user_id,))
    daily_usage = cursor.fetchone()[0] or 0
    conn.close()
    return daily_usage

# Generate URL with token
@app.route('/generate_url', methods=['POST'])
def generate_url():
    user_id = request.json.get('user_id')
    if not user_id:
        return jsonify({"error": "User ID is required"}), 400

    daily_usage = check_daily_usage(user_id)
    if daily_usage >= MAX_DAILY_USAGE.total_seconds():
        return jsonify({"error": "Daily usage limit reached"}), 403

    token = generate_token()
    expiry_time = datetime.now() + URL_VALIDITY_PERIOD

    conn = sqlite3.connect(DB_FILE)
    cursor = conn.cursor()
    cursor.execute('''
        INSERT INTO tokens (token, user_id, expiry) 
        VALUES (?, ?, ?)
    ''', (token, user_id, expiry_time))
    conn.commit()
    conn.close()

    url = f"https://github.com/whatheheckisthis/SAL-Dev-Setup-Guide/blob/main/README.md?token={token}"
    return jsonify({"url": url, "expiry": expiry_time.isoformat()}), 200

# Validate URL with token
@app.route('/validate_url', methods=['GET'])
def validate_url():
    token = request.args.get('token')
    if not token:
        return jsonify({"error": "Token is required"}), 400

    conn = sqlite3.connect(DB_FILE)
    cursor = conn.cursor()
    cursor.execute('''
        SELECT expiry FROM tokens WHERE token = ?
    ''', (token,))
    result = cursor.fetchone()
    conn.close()

    if result:
        expiry_time = datetime.fromisoformat(result[0])
        if datetime.now() < expiry_time:
            return jsonify({"valid": True, "expiry": expiry_time.isoformat()}), 200

    return jsonify({"valid": False}), 403

if __name__ == '__main__':
    init_db()
    app.run(debug=True)
```

### Summary

1. **Download**: Save the provided PowerShell scripts (`SAL_Setup.ps1` and `masking_script.ps1`) and the Python script (`masking_service.py`).
2. **Run Masking Script**: Execute `masking_script.ps1` to set up folders and move the Rust project.
3. **Run Setup Script**: Execute `SAL_Setup.ps1` to set up the environment, monitor security, and handle overrides.
4. **Security and IP Policy**: Ensure compliance with SAL's IP policy and handle unauthorized actions appropriately.

For any further assistance, reach out to SAL at [saldevs.team@gmail.com](mailto:saldevs.team@gmail.com).

To ensure that users with non-developer viewing permissions cannot execute commands or input any values into the script, we need to create a PowerShell script that restricts permissions and automates the setup process. This script will disable user input and enforce read-only access to the `SAL_Documentation` folder. 

Here's the updated PowerShell script:

### PowerShell Script (`SAL_Restricted_Setup.ps1`)

```powershell
# Define the paths
$sourcePath = "C:\Users\Username\Desktop\RustProject"
$destinationPath = "C:\Users\Username\SAL_Documentation\RustProject"

# Create the destination directory if it doesn't exist
if (-Not (Test-Path -Path $destinationPath)) {
    New-Item -ItemType Directory -Path $destinationPath
}

# Move the Rust project to the SAL_Documentation folder
Move-Item -Path $sourcePath -Destination $destinationPath

# Set the permissions to read-only for the SAL_Documentation folder
$acl = Get-Acl $destinationPath
$acl.SetAccessRuleProtection($true, $false) # Disable inheritance

$rule = New-Object System.Security.AccessControl.FileSystemAccessRule("Users","ReadAndExecute","ContainerInherit,ObjectInherit","None","Allow")
$acl.AddAccessRule($rule)

$denyRule = New-Object System.Security.AccessControl.FileSystemAccessRule("Users","Modify","ContainerInherit,ObjectInherit","None","Deny")
$acl.AddAccessRule($denyRule)

Set-Acl -Path $destinationPath -AclObject $acl

# Save the Python script into the SAL_Documentation folder
$pythonScriptContent = @"
import os
import sys
import time
import sqlite3
import uuid
from datetime import datetime, timedelta
from flask import Flask, request, jsonify
from threading import Timer

app = Flask(__name__)

# Constants
DB_FILE = 'masking_service.db'
URL_VALIDITY_PERIOD = timedelta(hours=3)
MAX_DAILY_USAGE = timedelta(hours=6)

# Initialize database
def init_db():
    conn = sqlite3.connect(DB_FILE)
    cursor = conn.cursor()
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

# Generate a unique token
def generate_token():
    return str(uuid.uuid4())

# Check daily usage limit
def check_daily_usage(user_id):
    now = datetime.now()
    conn = sqlite3.connect(DB_FILE)
    cursor = conn.cursor()
    cursor.execute('''
        SELECT SUM(strftime('%s', expiry) - strftime('%s', creation)) 
        FROM tokens 
        WHERE user_id = ? AND DATE(creation) = DATE('now')
    ''', (user_id,))
    daily_usage = cursor.fetchone()[0] or 0
    conn.close()
    return daily_usage

# Generate URL with token
@app.route('/generate_url', methods=['POST'])
def generate_url():
    user_id = request.json.get('user_id')
    if not user_id:
        return jsonify({"error": "User ID is required"}), 400

    daily_usage = check_daily_usage(user_id)
    if daily_usage >= MAX_DAILY_USAGE.total_seconds():
        return jsonify({"error": "Daily usage limit reached"}), 403

    token = generate_token()
    expiry_time = datetime.now() + URL_VALIDITY_PERIOD

    conn = sqlite3.connect(DB_FILE)
    cursor = conn.cursor()
    cursor.execute('''
        INSERT INTO tokens (token, user_id, expiry) 
        VALUES (?, ?, ?)
    ''', (token, user_id, expiry_time))
    conn.commit()
    conn.close()

    url = f"https://github.com/whatheheckisthis/SAL-Dev-Setup-Guide/blob/main/README.md?token={token}"
    return jsonify({"url": url, "expiry": expiry_time.isoformat()}), 200

# Validate URL with token
@app.route('/validate_url', methods=['GET'])
def validate_url():
    token = request.args.get('token')
    if not token:
        return jsonify({"error": "Token is required"}), 400

    conn = sqlite3.connect(DB_FILE)
    cursor = conn.cursor()
    cursor.execute('''
        SELECT expiry FROM tokens WHERE token = ?
    ''', (token,))
    result = cursor.fetchone()
    conn.close()

    if result:
        expiry_time = datetime.fromisoformat(result[0])
        if datetime.now() < expiry_time:
            return jsonify({"valid": True, "expiry": expiry_time.isoformat()}), 200

    return jsonify({"valid": False}), 403

if __name__ == '__main__':
    init_db()
    app.run(debug=True)
"@

$pythonScriptPath = "$destinationPath\masking_service.py"
Set-Content -Path $pythonScriptPath -Value $pythonScriptContent

# Set the permissions to read-only for the Python script
$acl = Get-Acl $pythonScriptPath
$acl.SetAccessRuleProtection($true, $false) # Disable inheritance

$rule = New-Object System.Security.AccessControl.FileSystemAccessRule("Users","ReadAndExecute","Allow")
$acl.AddAccessRule($rule)

$denyRule = New-Object System.Security.AccessControl.FileSystemAccessRule("Users","Modify","Deny")
$acl.AddAccessRule($denyRule)

Set-Acl -Path $pythonScriptPath -AclObject $acl

Write-Output "Setup complete. Permissions have been set to read-only for non-developer users."
```


1. **Save Script**: Save the above PowerShell script as `SAL_Restricted_Setup.ps1`.
2. **Run Script**: Execute the script in PowerShell:

   ```powershell
   .\SAL_Restricted_Setup.ps1
   ```
To create a system where users need to generate a unique 12-figure authentication key to access the contents of the `SAL_Documentation` folder, you can follow these steps:

1. **Create a Key Generation Service**: A Python script that runs a local Flask web service to generate and validate keys.
2. **Generate the Key**: Users can generate their unique key through the service.
3. **Access Control**: A PowerShell script that checks the key before allowing access to the folder. If the key is invalid or expired, a dialogue box will prompt users to generate a new key.

### Step 1: Key Generation Service

First, create the Python script `key_generation_service.py` to handle key generation and validation.

```python
import os
import sqlite3
import uuid
from datetime import datetime, timedelta
from flask import Flask, request, jsonify

app = Flask(__name__)

# Constants
DB_FILE = 'keys.db'
KEY_VALIDITY_PERIOD = timedelta(hours=3)

# Initialize database
def init_db():
    conn = sqlite3.connect(DB_FILE)
    cursor = conn.cursor()
    cursor.execute('''
        CREATE TABLE IF NOT EXISTS keys (
            key TEXT PRIMARY KEY,
            creation TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
            expiry TIMESTAMP NOT NULL
        )
    ''')
    conn.commit()
    conn.close()

# Generate a unique key
def generate_key():
    return str(uuid.uuid4()).replace('-', '')[:12]

# Generate key
@app.route('/generate_key', methods=['POST'])
def generate_key_route():
    key = generate_key()
    expiry_time = datetime.now() + KEY_VALIDITY_PERIOD

    conn = sqlite3.connect(DB_FILE)
    cursor = conn.cursor()
    cursor.execute('''
        INSERT INTO keys (key, expiry) 
        VALUES (?, ?)
    ''', (key, expiry_time))
    conn.commit()
    conn.close()

    return jsonify({"key": key, "expiry": expiry_time.isoformat()}), 200

# Validate key
@app.route('/validate_key', methods=['GET'])
def validate_key():
    key = request.args.get('key')
    if not key:
        return jsonify({"error": "Key is required"}), 400

    conn = sqlite3.connect(DB_FILE)
    cursor = conn.cursor()
    cursor.execute('''
        SELECT expiry FROM keys WHERE key = ?
    ''', (key,))
    result = cursor.fetchone()
    conn.close()

    if result:
        expiry_time = datetime.fromisoformat(result[0])
        if datetime.now() < expiry_time:
            return jsonify({"valid": True, "expiry": expiry_time.isoformat()}), 200

    return jsonify({"valid": False}), 403

if __name__ == '__main__':
    init_db()
    app.run(debug=True)
```

Save this script as `key_generation_service.py` and run it to start the key generation service.

### Step 2: Key Generation Interface

Create a simple interface for users to generate their key. This can be a simple HTML page with a JavaScript fetch call to the `/generate_key` endpoint. For simplicity, here is a basic example:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Generate Access Key</title>
</head>
<body>
    <h1>Generate Access Key</h1>
    <button onclick="generateKey()">Generate Key</button>
    <p id="key"></p>
    <p id="expiry"></p>

    <script>
        function generateKey() {
            fetch('/generate_key', {method: 'POST'})
                .then(response => response.json())
                .then(data => {
                    document.getElementById('key').innerText = `Key: ${data.key}`;
                    document.getElementById('expiry').innerText = `Expiry: ${data.expiry}`;
                })
                .catch(error => console.error('Error:', error));
        }
    </script>
</body>
</html>
```

Save this as `index.html` and place it in the same directory as the Python script. Users can open this HTML page in their browser to generate their keys.

### Step 3: Access Control Script

Create the PowerShell script to enforce access control based on the key.

```powershell
# Define the paths
$documentPath = "C:\Users\Username\SAL_Documentation\RustProject"
$keyFilePath = "$env:USERPROFILE\.sal_access_key"

# Function to prompt the user to generate a key
function Prompt-GenerateKey {
    Add-Type -AssemblyName PresentationFramework
    [System.Windows.MessageBox]::Show("You must generate a key to access the contents. Please visit the key generation service.", "Access Denied", "OK", "Error")
    exit
}

# Function to validate the key
function Validate-Key {
    if (-Not (Test-Path -Path $keyFilePath)) {
        Prompt-GenerateKey
    }

    $key = Get-Content -Path $keyFilePath -Raw
    $response = Invoke-RestMethod -Uri "http://127.0.0.1:5000/validate_key?key=$key" -Method Get

    if ($response.valid -eq $false) {
        Prompt-GenerateKey
    }

    return $true
}

# Check and validate key
if (Validate-Key) {
    Start-Process "explorer.exe" $documentPath
} else {
    Prompt-GenerateKey
}
```

Save this script as `access_control.ps1`. Users will need to run this script to access the `SAL_Documentation` folder. The script checks if the user has a valid key and prompts them to generate one if necessary.

1. **Run Key Generation Service**: Start the Python script to run the key generation service.
2. **Generate Key**:  open `index.html` in your browser to generate a key.
3. **Access Folder**: run `access_control.ps1` to validate your key and access the folder.

Certainly! Here's a prompt explaining the purpose of the script and guiding users on how to use it:

---

### Welcome to SAL!

To ensure secure and seamless access to the SAL Documentation, please follow these instructions carefully.

#### Why This Script is Important

This PowerShell script is essential for configuring your development environment and accessing the SAL Documentation safely. It performs several key functions:

1. **Key Validation**: It ensures that you have a valid access key to view the documentation. If you don’t have a key or it’s expired, the script will prompt you to generate a new one.
2. **Automatic Configuration**: It dynamically assigns your IP address and configures the Nginx server endpoints to match your local environment.
3. **Error Handling**: The script includes advanced error handling to address any issues automatically, ensuring that you can proceed without interruptions.
4. **Dependency Installation**: It installs necessary dependencies required for your development environment.

#### Instructions for Use

1. **Copy the Script**: Copy the entire PowerShell script provided below.

2. **Run in Terminal**:
   - Open PowerShell on your system.
   - Paste the copied script into the terminal.
   - The script will automatically save itself to the `SAL_Documentation` folder once executed.

3. **Access Documentation**:
   - After running the script, it will open the `SAL_Documentation` folder where you can access the resources you need.

#### Additional Information

- **Key Generation**: If you need to generate or validate your access key, please visit the [key generation service](http://127.0.0.1:5001/generate_key) provided by SAL.

- **Docker Repository**: For additional resources and containerization setups, check out our [Docker repository](https://github.com/whatheheckisthis/SAL-Docker).

Thank you for your cooperation and welcome to the SAL team!

---

**PowerShell Script:**

```powershell
# Define paths and constants
$documentPath = "C:\Users\Username\SAL_Documentation\RustProject"
$keyFilePath = "$env:USERPROFILE\.sal_access_key"
$nginxHost = "http://127.0.0.1:5001"
$validationEndpoint = "/validate_key?key="
$generateEndpoint = "/generate_key"
$logFilePath = "$env:USERPROFILE\sal_access_log.txt"

# Function to log errors
function Log-Error {
    param ([string]$message)
    Add-Content -Path $logFilePath -Value ("$(Get-Date): " + $message)
}

# Function to handle errors
function Handle-Error {
    param ([string]$errorMessage)
    Log-Error -message $errorMessage
    Start-Sleep -Seconds 3
}

# Function to prompt the user to generate a key
function Prompt-GenerateKey {
    Add-Type -AssemblyName PresentationFramework
    [System.Windows.MessageBox]::Show("You must generate a key to access the contents. Please visit the key generation service.", "Access Denied", "OK", "Error")
    exit
}

# Function to validate the key
function Validate-Key {
    if (-Not (Test-Path -Path $keyFilePath)) {
        Prompt-GenerateKey
    }

    $key = Get-Content -Path $keyFilePath -Raw
    try {
        $response = Invoke-RestMethod -Uri ($nginxHost + $validationEndpoint + $key) -Method Get
        if ($response.valid -eq $false) {
            Prompt-GenerateKey
        }
    } catch {
        Handle-Error -errorMessage "Validation request failed: $_"
        Prompt-GenerateKey
    }

    return $true
}

# Function to automatically configure endpoints and handle errors
function Configure-Endpoints {
    try {
        # Dynamic IP assignment and endpoint configuration
        $localIP = (Get-NetIPAddress -AddressFamily IPv4 | Where-Object {$_.IPAddress -ne "127.0.0.1"}).IPAddress
        if (-Not $localIP) {
            Handle-Error -errorMessage "Local IP address not found."
            return
        }

        # Configure Nginx endpoints
        $nginxConfigPath = "C:\nginx\conf\nginx.conf"
        if (Test-Path $nginxConfigPath) {
            $nginxConfig = Get-Content -Path $nginxConfigPath
            $nginxConfig = $nginxConfig -replace "listen 5001;", "listen $localIP:5001;"
            Set-Content -Path $nginxConfigPath -Value $nginxConfig
        } else {
            Handle-Error -errorMessage "Nginx configuration file not found."
            return
        }

        # Restart Nginx to apply changes
        Restart-Service -Name "nginx"
    } catch {
        Handle-Error -errorMessage "Configuration or restart failed: $_"
    }
}

# Download and install dependencies
function Install-Dependencies {
    $dependencies = @("dependency1", "dependency2") # List of dependencies to install

    foreach ($dependency in $dependencies) {
        try {
            Write-Output "Installing $dependency..."
            Install-Package -Name $dependency -Force
        } catch {
            Handle-Error -errorMessage "Failed to install $dependency: $_"
        }
    }
}

# Main script execution
try {
    Configure-Endpoints
    if (Validate-Key) {
        Start-Process "explorer.exe" $documentPath
    } else {
        Prompt-GenerateKey
    }
} catch {
    Handle-Error -errorMessage "Main script execution failed: $_"
}
```

---

To set up a local key generation service for the SAL_Documentation folder, you'll need an HTML page served by a local server. Here’s a plan to achieve this:

1. **Create the HTML Key Generation Page**
2. **Set Up the Local Server**
3. **Integrate with PowerShell Script**


### 1. Create the HTML Key Generation Page

Create an `index.html` file with the following content. This page will handle key generation and display relevant messages:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>SAL Key Generation Service</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            margin: 0;
            padding: 0;
            background-color: #f4f4f4;
            color: #333;
            text-align: center;
        }
        .container {
            width: 80%;
            margin: auto;
            padding: 20px;
            background: #fff;
            box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
            border-radius: 5px;
        }
        h1 {
            color: #0056b3;
        }
        button {
            background: #0056b3;
            color: #fff;
            border: none;
            padding: 10px 20px;
            cursor: pointer;
            border-radius: 5px;
        }
        button:hover {
            background: #003d7a;
        }
        .message {
            margin-top: 20px;
            font-size: 1.2em;
        }
    </style>
    <script>
        let keyGenerationAllowed = true;

        function generateKey() {
            if (!keyGenerationAllowed) {
                document.getElementById('message').innerHTML = 'Key generation limit exceeded. Please contact the development team at <a href="mailto:saldevs.team@gmail.com">saldevs.team@gmail.com</a>.';
                return;
            }

            const now = new Date();
            const expirationDate = new Date(now.getTime() + (3 * 60 * 60 * 1000));
            const key = 'KEY-' + Math.random().toString(36).substr(2, 12).toUpperCase();

            // Save the key and expiration in local storage
            localStorage.setItem('authKey', key);
            localStorage.setItem('keyExpiration', expirationDate);

            document.getElementById('message').innerHTML = `Your authentication key is: <strong>${key}</strong><br>It will expire on ${expirationDate.toLocaleTimeString()}.`;
            keyGenerationAllowed = false;

            // Re-enable key generation after 6 hours
            setTimeout(() => {
                keyGenerationAllowed = true;
            }, 6 * 60 * 60 * 1000); // 6 hours
        }

        window.onload = function() {
            const savedKey = localStorage.getItem('authKey');
            const keyExpiration = new Date(localStorage.getItem('keyExpiration'));

            if (savedKey && new Date() < keyExpiration) {
                document.getElementById('message').innerHTML = `Your current authentication key is: <strong>${savedKey}</strong><br>It will expire on ${keyExpiration.toLocaleTimeString()}.`;
                keyGenerationAllowed = false;
            }
        }
    </script>
</head>
<body>
    <div class="container">
        <h1>SAL Key Generation Service</h1>
        <p>Click the button below to generate a new authentication key.</p>
        <button onclick="generateKey()">Generate Key</button>
        <div id="message" class="message"></div>
    </div>
</body>
</html>
```

### 2. Set Up the Local Server

Use Nginx to serve the HTML file. Here’s a basic configuration for Nginx to serve your `index.html`:

**Nginx Configuration File (e.g., `nginx.conf`):**

```nginx
server {
    listen 5001;
    server_name localhost;

    location / {
        root /path/to/SAL_Documentation;
        index index.html;
    }
}
```

Replace `/path/to/SAL_Documentation` with the actual path where `index.html` is stored.

### 3. Integrate with PowerShell Script

Update the PowerShell script to open the HTML page once the script is executed. Here’s how:

```powershell
# Define paths and constants
$documentPath = "C:\Users\Username\SAL_Documentation\index.html"
$keyFilePath = "$env:USERPROFILE\.sal_access_key"
$nginxHost = "http://127.0.0.1:5001"
$logFilePath = "$env:USERPROFILE\sal_access_log.txt"

# Function to log errors
function Log-Error {
    param ([string]$message)
    Add-Content -Path $logFilePath -Value ("$(Get-Date): " + $message)
}

# Function to handle errors
function Handle-Error {
    param ([string]$errorMessage)
    Log-Error -message $errorMessage
    Start-Sleep -Seconds 3
}

# Function to validate the key
function Validate-Key {
    if (-Not (Test-Path -Path $keyFilePath)) {
        Start-Process "chrome.exe" -ArgumentList $documentPath
        exit
    }

    $key = Get-Content -Path $keyFilePath -Raw
    try {
        $response = Invoke-RestMethod -Uri ($nginxHost + "/validate_key?key=" + $key) -Method Get
        if ($response.valid -eq $false) {
            Start-Process "chrome.exe" -ArgumentList $documentPath
            exit
        }
    } catch {
        Handle-Error -errorMessage "Validation request failed: $_"
        Start-Process "chrome.exe" -ArgumentList $documentPath
        exit
    }

    return $true
}

# Function to automatically configure endpoints and handle errors
function Configure-Endpoints {
    try {
        $localIP = (Get-NetIPAddress -AddressFamily IPv4 | Where-Object {$_.IPAddress -ne "127.0.0.1"}).IPAddress
        if (-Not $localIP) {
            Handle-Error -errorMessage "Local IP address not found."
            return
        }

        $nginxConfigPath = "C:\nginx\conf\nginx.conf"
        if (Test-Path $nginxConfigPath) {
            $nginxConfig = Get-Content -Path $nginxConfigPath
            $nginxConfig = $nginxConfig -replace "listen 5001;", "listen $localIP:5001;"
            Set-Content -Path $nginxConfigPath -Value $nginxConfig
        } else {
            Handle-Error -errorMessage "Nginx configuration file not found."
            return
        }

        Restart-Service -Name "nginx"
    } catch {
        Handle-Error -errorMessage "Configuration or restart failed: $_"
    }
}

# Download and install dependencies
function Install-Dependencies {
    $dependencies = @("dependency1", "dependency2")

    foreach ($dependency in $dependencies) {
        try {
            Write-Output "Installing $dependency..."
            Install-Package -Name $dependency -Force
        } catch {
            Handle-Error -errorMessage "Failed to install $dependency: $_"
        }
    }
}

# Main script execution
try {
    Configure-Endpoints
    if (Validate-Key) {
        Start-Process "explorer.exe" "C:\Users\Username\SAL_Documentation"
    } else {
        Start-Process "chrome.exe" -ArgumentList $documentPath
    }
} catch {
    Handle-Error -errorMessage "Main script execution failed: $_"
}
```

### 4. How to Use the Service

Here’s how to configure your setup:

---

### Using the SAL Key Generation Service

To access the SAL Documentation and ensure seamless operation, follow these steps:

1. **Run the PowerShell Script**:
   - Copy and execute the provided PowerShell script in your terminal. The script will configure your environment and open the SAL Key Generation Service if needed.

2. **Generate a Key**:
   - The script will check if you have a valid key. If not, it will automatically open the local key generation service page in your default browser (Chrome, Firefox, or Edge).

3. **Access the Documentation**:
   - After obtaining a key, the script will open the SAL Documentation folder. If you have a valid key, you can access the contents directly.

4. **Key Generation Limits**:
   - You can generate a new key every 3 hours, with a maximum of 2 keys per day. If you exceed this limit, you'll see a message asking you to contact the development team at [saldevs.team@gmail.com](mailto:saldevs.team@gmail.com).

---


