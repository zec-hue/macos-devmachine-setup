# Machine Setup
The following is a `.sh` script that you can reuse to setup a new machine.

This script is valid for application and framework versions as-of September 2025

```sh
#!/bin/bash
set -e

echo "🔧 Starting dev environment bootstrap for macOS (LTS versions only)..."

# Ensure Homebrew is installed
if ! command -v brew &> /dev/null; then
  echo "🍺 Installing Homebrew..."
  /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
fi

echo "🍺 Updating Homebrew..."
brew update

# Core language runtimes
echo "📦 Installing Python 3.12.9 via pyenv..."
brew install pyenv
pyenv install 3.12.9
pyenv global 3.12.9

echo "☕ Installing Java 25 (LTS)..."
brew install openjdk@25
sudo ln -sfn /opt/homebrew/opt/openjdk@25/libexec/openjdk.jdk /Library/Java/JavaVirtualMachines/openjdk-25.jdk

echo "🟣 Installing .NET 8 (LTS)..."
brew install --cask dotnet-sdk
# Optionally pin to .NET 8 if multiple versions are installed
export DOTNET_ROOT="/usr/local/share/dotnet"
export PATH="$DOTNET_ROOT:$PATH"

echo "🟢 Installing Node.js 20 (LTS) via nvm..."
brew install nvm
mkdir -p ~/.nvm
echo 'export NVM_DIR="$HOME/.nvm"' >> ~/.zshrc
echo '[ -s "/opt/homebrew/opt/nvm/nvm.sh" ] && \. "/opt/homebrew/opt/nvm/nvm.sh"' >> ~/.zshrc
source ~/.zshrc
nvm install 20
nvm use 20
nvm alias default 20

# Shell environment tools
echo "🧠 Installing direnv and syncing shell config..."
brew install direnv
echo 'eval "$(direnv hook bash)"' >> ~/.bashrc
echo 'eval "$(direnv hook zsh)"' >> ~/.zshrc

# Cloud and infrastructure
echo "☁️ Installing Azure CLI and Bicep CLI..."
brew install azure-cli
az bicep install

echo "☸️ Installing kubectl v1.34 (LTS)..."
brew install kubectl
# Optional: pin version if needed
# brew install https://raw.githubusercontent.com/Homebrew/homebrew-core/HEAD/Formula/k/kubectl.rb

echo "📦 Installing Helm v3.19 (LTS)..."
brew install helm

# Frontend tooling
echo "🅰️ Installing Angular CLI 20 (latest LTS)..."
npm install -g @angular/cli@20

# Git and GitHub
echo "🔐 Installing Git, GnuPG, and GitHub CLI..."
brew install git
brew install gnupg
brew install gh
brew install git-credential-manager

# Configure Git
git config --global --get-all credential.helper
git config --global --unset-all credential.helper
git config --global credential.helper manager
git config --global --get credential.helper

# Containers
echo "🐳 Installing Docker Desktop..."
brew install --cask docker
open /Applications/Docker.app

# Final checks
echo "✅ Verifying installations..."
echo "Python: $(python3 --version)"
echo "Java: $(/usr/libexec/java_home -v 25)"
echo ".NET: $(dotnet --version)"
echo "Node: $(node -v)"
echo "Angular CLI: $(ng version | grep 'Angular CLI')"
echo "Azure CLI: $(az version | grep azure-cli)"
echo "Bicep: $(az bicep version)"
echo "kubectl: $(kubectl version --client --short)"
echo "Helm: $(helm version --short)"
echo "Git: $(git --version)"
echo "GPG: $(gpg --version | head -n 1)"
echo "GitHub CLI: $(gh --version | head -n 1)"
echo "Docker: $(docker --version)"

echo "🎉 Dev environment setup complete (LTS versions only)!"
```
