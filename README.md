Setup Instructions for Windows Laptops:

The PowerShell Script
PowerShell

# Define the path to the user's home directory
$ConfigPath = "$env:USERPROFILE\.wslconfig"

# Define the settings: 8GB RAM for Docker, 20GB Swap (Disk) if your system is compatible , if not then lower the space furthur
$Content = "[wsl2]`nmemory=8GB`nswap=20GB`nlocalhostForwarding=true"

# Force create/overwrite the file
Set-Content -Path $ConfigPath -Value $Content -Force

# Confirm success
Write-Host "Success! .wslconfig created at: $ConfigPath" -ForegroundColor Green
Write-Host "Please restart Docker Desktop for changes to take effect." -ForegroundColor Yellow
How to use it:
Copy the code above.

Open PowerShell as Administrator.

Paste and hit Enter.

After running this, remember to run wsl --shutdown to apply the changes!
Restart Docker Desktop.

Build the image: docker build -t geochat-prod .

Run it: docker run --gpus all -p 7860:7860 -v ${PWD}/geochat-7B:/app/GeoChat/geochat-7B geochat-prod