# BurpSuite Professional 2026 - Universal Activation Tool

![BurpSuite](https://img.shields.io/badge/BurpSuite-Professional-orange)
![Java](https://img.shields.io/badge/Java-21.0.9-blue)
![Platform](https://img.shields.io/badge/Platform-Windows%20%7C%20Linux%20%7C%20macOS-green)
![License](https://img.shields.io/badge/License-Educational-red)

A comprehensive activation tool for BurpSuite Professional 2026 that works across all major operating systems. This repository contains the keygen utility with advanced Java Instrumentation capabilities and detailed installation instructions.

## ◆ Features

- ✓ **Cross-Platform Compatibility**: Works on Windows, Linux, and macOS
- ✓ **Advanced Runtime Manipulation**: Uses Java Instrumentation API
- ✓ **Memory-Only Operations**: No disk file modifications
- ✓ **Dynamic Bytecode Engineering**: Real-time class transformation
- ✓ **GUI-Based Interface**: User-friendly keygen application
- ✓ **Version Agnostic**: Works across multiple BurpSuite versions
- ✓ **Auto-Version Detection**: Fetches latest version from PortSwigger API, checks your local jar, and prompts download only when newer version exists
- ✓ **Copy Link Button**: One-click copy of the download URL to clipboard (falls back when clicking the label fails)
- ✓ **Auto-Run Mode**: Launch BurpSuite directly from the keygen with CLI flags

## ◆ Internal Working Mechanism

This tool functions as a **Java Instrumentation Agent**, leveraging the JVM's dynamic attach mechanism to perform runtime bytecode manipulation. Rather than modifying the application's physical files on disk, it operates entirely in memory, intercepting classes as they are loaded by the ClassLoader.

### 1. Dynamic Instrumentation Architecture

The core of the application relies on the `java.lang.instrument` package. When launched, the tool registers a custom **ClassFileTransformer**.

- **Entry Point**: The agent utilizes the `premain` entry point, which initializes before the target application's `main` method.
- **Interception**: Every time the JVM attempts to load a class (whether a core Java class or a third-party library), the agent intercepts the raw byte array.
- **Analysis**: The agent inspects the bytecode structure (using the ASM framework) to determine if the class matches specific heuristic patterns—such as code complexity, specific instruction signatures, or inheritance hierarchies—rather than relying solely on obfuscated names which change between versions.

### 2. Runtime Bytecode Manipulation

Once a target class is identified, the agent uses the **ASM Bytecode Engineering Library** to parse the class into a tree of nodes (Code Analysis). It then applies algorithmic patches:

#### A. Cryptographic Key Substitution

The tool creates a "Man-in-the-Middle" attack on the application's internal mathematical logic.

- **Logic**: It identifies core mathematical libraries responsible for handling cryptographic primitives (such as modular exponentiation).
- **Substitution**: By injecting a check at the start of these mathematical operations, the agent detects usage of the vendor's public key. Upon detection, it hot-swaps the vendor's key with a custom-generated public key in real-time.
- **Result**: This forces the application to verify signatures against the user's custom key pair, allowing the Keygen to generate valid licenses without needing to reverse the underlying private key.

#### B. Control Flow Graph (CFG) Rewriting

To bypass integrity checks and anti-tamper mechanisms, the tool modifies the Control Flow Graph of the target methods.

- **Exception Suppression**: The agent scans for instructions that instantiate fatal exceptions (often used to crash the application upon tamper detection). It rewrites the preceding `Jump` instructions (GOTO/IF) to bypass the exception throw entirely, effectively "stepping over" the crash logic.
- **Logic Redirection**: For complex verification routines, the agent clears the original instruction list and inserts a "trampoline" (hook). This redirects the execution flow to a custom internal filter that forces a boolean `true` (success) state, regardless of the actual check result.

#### C. HTTP Response Interception

The tool extends its reach to internal HTTP clients used by plugins and extensions.

- **Hooking**: It locates specific methods responsible for building HTTP responses.
- **Modification**: Before the response is returned to the calling plugin, the agent passes the data through a filter. This filter checks for negative server responses (e.g., "License Invalid") and rewrites the body and status code to mimic a successful server authorization.

### 3. Workflow Summary

1. **Initialization**: The Agent loads and establishes the Transformer.
2. **Bytecode Analysis**: As classes load, they are scanned for signature matches.
3. **Patching**:
   - **Math Libs** → Patched for Key Swap.
   - **Network Libs** → Patched for Response Mocking.
   - **Core Logic** → Patched for Integrity Bypass.
4. **Execution**: The modified bytecode is returned to the JVM, and the application runs transparently with the altered logic.

---

### ▸ Version Detection & Copy Link

#### Auto-Version Detection
When the keygen launches, it:
1. Fetches the latest BurpSuite release version from PortSwigger's API at `https://portswigger.net/burp/releases/data?pageSize=5`
2. Parses the JSON response for `"BuildCategoryId":"desktop"` and `"Version":"X.Y.Z"`
3. Scans the local directory (where `nixon.jar` resides) for `burpsuite_*.jar` files (picks the most recently modified)
4. Compares: if your local jar's filename already contains the latest version → shows green **"already the latest version"**
5. If a newer version exists → shows blue **"Latest version: X.Y.Z. Click to download"** with a **Copy Link** button

#### Copy Link Button
- Added as a fallback when clicking the blue label fails to open the browser
- Positioned right beside the "Click to download" text at `x=460, y=5`
- Uses `java.awt.datatransfer.Clipboard` to copy the full download URL:
  ```
  https://portswigger.net/burp/releases/download?product=desktop&type=Jar&version=X.Y.Z
  ```
- Implemented via bytecode injection (ASM) into `KeygenForm.main()` — no modification to original click handler
- Uses `ActionListener` (not `MouseAdapter`) to prevent event leaking to the underlying label

---

### ⚙ CLI Arguments

The keygen supports the following command-line flags:

| Flag | Long Flag | Description |
|------|-----------|-------------|
| `-a` | `-auto` | Enable auto-run mode (launches BurpSuite automatically on keygen start) |
| `-a 0` | `-auto 0` | Disable auto-run mode |
| `-i` | `-ignore` | Enable ignore update mode (skip version check on auto-run) |
| `-i 0` | `-ignore 0` | Disable ignore update mode |
| `-n` | `-name` | Set a custom license name (overrides system username) |

Flags are persisted to `.config.ini` in the same directory as `nixon.jar`.

### ▸ Configuration File

The keygen stores its settings in a `.config.ini` file located in the same directory as `nixon.jar`:

```ini
auto_run=0    # 0 = disabled, 1 = auto-launch BurpSuite
ignore=0      # 0 = check for updates, 1 = skip version check
```

## ▸ Prerequisites

- **Operating System:** Windows 10/11, Linux (any distribution), or macOS 10.14+
- **Java Version:** OpenJDK 21.0.9+ (recommended)
- **Privileges:** Administrator/sudo access (for Java installation)
- **Environment:** GUI/Desktop environment for the keygen interface
- **Storage:** Minimum 500 MB free space
- **Memory:** At least 1 GB RAM recommended

## → Quick Start

### Step 1: Clone the Repository

```bash
git clone https://github.com/Nixon-H/burpsuite-professional-keygen.git
cd burpsuite-professional-keygen
```

### Step 2: Install Java 21

**Choose your operating system below:**

- **[Windows Installation](#-windows-installation)**
- **[Linux Installation](#-linux-installation)**
- **[macOS Installation](#-macos-installation)**

### Step 3: Run the Keygen

After installing Java 21, proceed to the [Running the Keygen](#-running-the-keygen) section.

---

## ❖ Windows Installation

### Step 1: Download OpenJDK 21

1. Visit the official OpenJDK download page: **[https://jdk.java.net/archive/](https://jdk.java.net/archive/)**
2. Scroll down to the **"Builds"** section
3. Download the **Windows/x64** ZIP archive
   - File to Download: `openjdk-21.0.9`
   - File size: ~188 MB

### Step 2: Extract the Archive

1. Navigate to your Downloads folder
2. Right-click the downloaded ZIP file
3. Select **"Extract All..."**
4. Choose destination: `C:\Program Files\Java\`
5. After extraction, you should have: `C:\Program Files\Java\jdk-21.0.9\`

**Alternative extraction locations:**
- `C:\Java\jdk-21.0.9\`
- `C:\Users\YourUser\Java\jdk-21.0.9\`

### Step 3: Set Environment Variables

#### Method 1: Using System Properties (Recommended)

1. **Open System Properties:**
   - Press `Win + X` keys together
   - Select **"System"** from the menu
   - Click **"Advanced system settings"** on the right
   - Click the **"Environment Variables..."** button at the bottom

2. **Create JAVA_HOME variable:**
   - Under **"System variables"** section, click **"New..."**
   - **Variable name:** `JAVA_HOME`
   - **Variable value:** `C:\Program Files\Java\jdk-21.0.9` (your installation path)
   - Click **"OK"**

3. **Update PATH variable:**
   - Under **"System variables"**, find and select **"Path"**
   - Click **"Edit..."**
   - Click **"New"**
   - Add: `%JAVA_HOME%\bin`
   - Click **"OK"** on all windows to save

#### Method 2: Using Command Prompt (Advanced)

Open Command Prompt as Administrator and run:

```cmd
setx JAVA_HOME "C:\Program Files\Java\jdk-21.0.9" /M
setx PATH "%PATH%;%JAVA_HOME%\bin" /M
```

### Step 4: Verify Installation

1. **Close and reopen** Command Prompt or PowerShell (important!)
2. Run the verification command:

```cmd
java -version
```

**Expected output:**
```
openjdk version "21.0.9" 2025-10-21
OpenJDK Runtime Environment (build 21.0.9+10)
OpenJDK 64-Bit Server VM (build 21.0.9+10, mixed mode, sharing)
```

✓ **Success!** Java 21 is now installed and configured.

### Step 5: Navigate to Keygen Directory

```cmd
cd path\to\burpsuite-professional-keygen
```

Example:
```cmd
cd C:\Users\YourUser\Downloads\burpsuite-professional-keygen
```

### Step 6: Launch the Keygen

**Method 1: Using Command Line**
```cmd
java -jar nixon.jar
```

**Method 2: Double-Click**
- Simply double-click `nixon.jar` in File Explorer
- If prompted, select "Java(TM) Platform SE binary"

---

## ❖ Linux Installation

### Step 1: Update Package Repository

Open your terminal and update the package index:

```bash
sudo apt update
```

**For other distributions:**
- **Fedora/RHEL:** `sudo dnf update`
- **Arch Linux:** `sudo pacman -Sy`
- **openSUSE:** `sudo zypper refresh`

### Step 2: Install OpenJDK 21 JDK

**For Debian/Ubuntu/Kali:**
```bash
sudo apt install openjdk-21-jdk -y
```

**For Fedora/RHEL/CentOS:**
```bash
sudo dnf install java-21-openjdk-devel -y
```

**For Arch Linux:**
```bash
sudo pacman -S jdk21-openjdk
```

**For openSUSE:**
```bash
sudo zypper install java-21-openjdk-devel
```

**Installation details:**
- Download size: ~200-300 MB
- Installation location: `/usr/lib/jvm/java-21-openjdk-amd64/`
- Includes runtime, development tools, and GUI libraries

**Why the full JDK package?**
- Contains complete Java Runtime Environment (JRE)
- Includes GUI libraries (JavaFX, Swing) for keygen interface
- Provides Java Instrumentation API support
- Ensures full bytecode manipulation capabilities

### Step 3: Configure Java 21 as Default

If you have multiple Java versions installed:

```bash
sudo update-alternatives --config java
```

**You'll see output like this:**
```
There are 3 choices for the alternative java (providing /usr/bin/java).

  Selection    Path                                         Priority   Status
------------------------------------------------------------
* 0            /usr/lib/jvm/java-17-openjdk-amd64/bin/java   1711      auto mode
  1            /usr/lib/jvm/java-17-openjdk-amd64/bin/java   1711      manual mode
  2            /usr/lib/jvm/java-21-openjdk-amd64/bin/java   2111      manual mode

Press <enter> to keep the current choice[*], or type selection number:
```

**Action required:**
1. Find the line containing `java-21-openjdk`
2. Note its selection number (in example above: `2`)
3. Type that number and press **Enter**

**Optional:** Configure the Java compiler:
```bash
sudo update-alternatives --config javac
```

### Step 4: Verify Installation

```bash
java -version
```

**Expected output:**
```
openjdk version "21.0.9" 2025-10-21
OpenJDK Runtime Environment (build 21.0.9+10-Debian-1deb13u1)
OpenJDK 64-Bit Server VM (build 21.0.9+10-Debian-1deb13u1, mixed mode, sharing)
```

**Verify these details:**
- ✓ Version: `21.0.9` or higher
- ✓ 64-Bit Server VM
- ✓ Build includes your distribution identifier

### Step 5: Set File Permissions

Navigate to the keygen directory and grant execute permissions:

```bash
cd burpsuite-professional-keygen
chmod +x nixon.jar
```

### Step 6: Launch the Keygen

```bash
java -jar nixon.jar
```

**If running on Wayland:**
```bash
GDK_BACKEND=x11 java -jar nixon.jar
```

---

## ❖ macOS Installation

### Option A: Using Homebrew (Recommended)

#### Step 1: Install Homebrew (if not already installed)

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

#### Step 2: Install OpenJDK 21

```bash
brew install openjdk@21
```

#### Step 3: Create Symbolic Link

```bash
sudo ln -sfn /opt/homebrew/opt/openjdk@21/libexec/openjdk.jdk /Library/Java/JavaVirtualMachines/openjdk-21.jdk
```

**For Intel Macs:**
```bash
sudo ln -sfn /usr/local/opt/openjdk@21/libexec/openjdk.jdk /Library/Java/JavaVirtualMachines/openjdk-21.jdk
```

#### Step 4: Update Shell Profile

Add to your shell configuration file (`~/.zshrc` or `~/.bash_profile`):

```bash
export JAVA_HOME=$(/usr/libexec/java_home -v 21)
export PATH="$JAVA_HOME/bin:$PATH"
```

Apply changes:
```bash
source ~/.zshrc  # or source ~/.bash_profile
```

### Option B: Manual Installation

#### Step 1: Download OpenJDK 21

1. Visit: **[https://jdk.java.net/archive/](https://jdk.java.net/archive/)**
2. Download the appropriate file:
   - **Apple Silicon (M1/M2/M3):** macOS/AArch64 tar.gz
   - **Intel Macs:** macOS/x64 tar.gz

#### Step 2: Extract and Install

```bash
# Navigate to Downloads
cd ~/Downloads

# Extract the archive
tar -xzf openjdk-21.0.9_macos-*.tar.gz

# Move to Java installations directory
sudo mv jdk-21.0.9.jdk /Library/Java/JavaVirtualMachines/
```

#### Step 3: Set JAVA_HOME

Add to `~/.zshrc` or `~/.bash_profile`:

```bash
export JAVA_HOME=/Library/Java/JavaVirtualMachines/jdk-21.0.9.jdk/Contents/Home
export PATH="$JAVA_HOME/bin:$PATH"
```

Apply changes:
```bash
source ~/.zshrc
```

### Verify Installation (Both Methods)

```bash
java -version
```

**Expected output:**
```
openjdk version "21.0.9" 2025-10-21
OpenJDK Runtime Environment (build 21.0.9+10)
OpenJDK 64-Bit Server VM (build 21.0.9+10, mixed mode, sharing)
```

### Set File Permissions

```bash
cd burpsuite-professional-keygen
chmod +x nixon.jar
```

### Launch the Keygen

```bash
java -jar nixon.jar
```

**Alternative (if double-click doesn't work):**
- Right-click `nixon.jar` → Open With → Jar Launcher

---

## ▶ Running the Keygen

Once Java 21 is properly installed on your system, launching the keygen is straightforward:

### Universal Command (All Platforms)

```bash
java -jar nixon.jar
```

### With Auto-Run (Launches BurpSuite automatically)

```bash
java -jar nixon.jar -a
```

### With Custom License Name

```bash
java -jar nixon.jar -n "YourName"
```

### What Happens When You Run It

1. **JVM Initialization**: The Java Virtual Machine starts
2. **Agent Loading**: The Instrumentation Agent registers with the JVM
3. **Transformer Registration**: ClassFileTransformer hooks into the class loading mechanism
4. **GUI Launch**: The keygen interface window appears
5. **Version Check**: Fetches latest BurpSuite version from PortSwigger API
6. **Status Display**: Shows either "already the latest version" (green) or download prompt (blue with Copy Link button)
7. **Ready State**: Bytecode manipulation capabilities are now active

### Platform-Specific Notes

**Windows:**
- You can also double-click `nixon.jar` if file associations are configured
- Right-click → "Open with" → "Java(TM) Platform SE binary"
- May need to allow through Windows Firewall/Defender

**Linux:**
- Ensure you're running in a GUI environment (not SSH without X11)
- For Wayland users: `GDK_BACKEND=x11 java -jar nixon.jar`
- Some distros may require additional X11 libraries

**macOS:**
- Grant necessary permissions when prompted
- May need to allow in System Preferences → Security & Privacy
- For Gatekeeper issues: Right-click → Open (instead of double-click)

---

## ▸ Video Tutorial - IMPORTANT!

**‼ CRITICAL: After successfully launching nixon.jar, you MUST follow the complete video tutorial below:**

### 🎥 [Click Here to Watch the Complete Activation Tutorial](YOUR_VIDEO_LINK_HERE)

**The video demonstrates:**
- ◦ Complete keygen interface walkthrough
- ◦ Step-by-step license generation process
- ◦ BurpSuite Professional activation procedure
- ◦ License key application and verification
- ◦ Understanding the Instrumentation Agent workflow
- ◦ Troubleshooting common activation issues
- ◦ Post-activation configuration and testing
- ◦ Verifying successful bypass of integrity checks

**‼ Do not proceed without watching this tutorial!** Improper usage may result in:
- Activation failures
- Application crashes
- Invalid license generation
- Bytecode manipulation errors

---

## ⚙ Troubleshooting

### ❖ Windows-Specific Issues

#### ✗ "'java' is not recognized as an internal or external command"

**Cause:** Java is not in your system PATH or JAVA_HOME is not set correctly.

**Solution:**
1. Verify Java installation exists in `C:\Program Files\Java\jdk-21.0.9\`
2. Re-configure environment variables:
   - `JAVA_HOME` = `C:\Program Files\Java\jdk-21.0.9`
   - Add `%JAVA_HOME%\bin` to PATH
3. **Important:** Close and reopen Command Prompt/PowerShell
4. Test: `java -version`

#### ✗ Windows Defender blocks nixon.jar

**Solution:**
1. Open Windows Security
2. Go to "Virus & threat protection"
3. Click "Manage settings"
4. Add an exclusion for the repository folder
5. Retry launching the JAR

#### ✗ Double-clicking nixon.jar opens WinRAR/7-Zip

**Cause:** File association is incorrect.

**Solution:**
1. Right-click `nixon.jar`
2. Select "Open with" → "Choose another app"
3. Select "Java(TM) Platform SE binary"
4. Check "Always use this app"
5. Click OK

### ❖ Linux-Specific Issues

#### ✗ "command not found: java"

**Solution:**
```bash
# Debian/Ubuntu/Kali
sudo apt update && sudo apt install openjdk-21-jdk -y

# Fedora/RHEL
sudo dnf install java-21-openjdk-devel -y

# Arch
sudo pacman -S jdk21-openjdk
```

#### ✗ GUI window doesn't appear

**Solution 1: Check display environment**
```bash
echo $DISPLAY
# Should output something like :0 or :1

# If empty, set it:
export DISPLAY=:0
java -jar nixon.jar
```

**Solution 2: Install missing X11 libraries**
```bash
# Debian/Ubuntu
sudo apt install libxrender1 libxtst6 libxi6 libxext6 -y

# Fedora
sudo dnf install libXrender libXtst libXi libXext -y
```

**Solution 3: For Wayland users**
```bash
GDK_BACKEND=x11 java -jar nixon.jar
```

#### ✗ "Permission denied" error

**Solution:**
```bash
# Fix file permissions
chmod 644 nixon.jar
chmod +x nixon.jar

# Verify
ls -la nixon.jar
# Should show: -rwxr-xr-x
```

#### ✗ Wrong Java version active

**Solution:**
```bash
# Reconfigure alternatives
sudo update-alternatives --config java
# Select java-21-openjdk option

# Verify
java -version
```

### ❖ macOS-Specific Issues

#### ✗ "java: command not found"

**Solution:**

**If using Homebrew:**
```bash
brew install openjdk@21
# Follow the linking instructions from brew output
```

**If installed manually:**
```bash
# Add to ~/.zshrc
export JAVA_HOME=/Library/Java/JavaVirtualMachines/jdk-21.0.9.jdk/Contents/Home
export PATH="$JAVA_HOME/bin:$PATH"

source ~/.zshrc
```

#### ✗ Gatekeeper prevents opening

**Cause:** macOS blocks unsigned applications.

**Solution:**
1. Right-click (or Control-click) `nixon.jar`
2. Select "Open" from context menu
3. Click "Open" in the dialog
4. Grant necessary permissions

**Alternative:**
```bash
# Remove quarantine attribute
xattr -d com.apple.quarantine nixon.jar
java -jar nixon.jar
```

#### ✗ Wrong Java version on macOS

**Solution:**
```bash
# List all Java versions
/usr/libexec/java_home -V

# Set Java 21 temporarily
export JAVA_HOME=$(/usr/libexec/java_home -v 21)

# Verify
java -version

# Make permanent: add to ~/.zshrc
echo 'export JAVA_HOME=$(/usr/libexec/java_home -v 21)' >> ~/.zshrc
```

#### ✗ "Damaged and can't be opened" error

**Solution:**
```bash
# Clear extended attributes
xattr -cr nixon.jar

# Or remove specific quarantine flag
xattr -d com.apple.quarantine nixon.jar
```

### ⚙️ Universal Issues (All Platforms)

#### ✗ "UnsupportedClassVersionError"

**Cause:** JAR compiled for Java 21, but you're running an older version.

**Solution:**
```bash
# Check your version
java -version

# If not 21.x, reinstall Java 21 following your platform's guide above
```

#### ✗ "A JNI error has occurred"

**Cause:** Java version mismatch or corrupted JAR.

**Solution:**
1. Verify Java version: `java -version` (must be 21.x)
2. Check JAR integrity:
   ```bash
   # Linux/macOS
   md5sum nixon.jar
   
   # Windows (PowerShell)
   Get-FileHash nixon.jar -Algorithm MD5
   ```
3. Re-clone repository if hash doesn't match

#### ✗ GUI appears but freezes/crashes

**Solution:**
```bash
# Run with increased memory allocation
java -Xmx1024m -jar nixon.jar

# Run with verbose output to see errors
java -jar nixon.jar 2>&1 | tee error.log
```

#### ✗ "Could not find or load main class"

**Cause:** Corrupted or incomplete JAR file.

**Solution:**
1. Check file size (should be ~30+ KB)
2. Re-download:
   ```bash
   cd ..
   rm -rf burpsuite-professional-keygen
   git clone https://github.com/Nixon-H/burpsuite-professional-keygen.git
   ```

#### ✗ "Click to download" shows even when I'm on the latest version

**Cause:** The version detection compares your local `burpsuite_*.jar` filename against the latest version from the PortSwigger API. If there's no matching `burpsuite_*.jar` in the same directory as `nixon.jar`, the check falls through.

**Solution:**
1. Ensure your downloaded BurpSuite jar is in the same directory as `nixon.jar`
2. The jar must match the glob pattern `burpsuite_*.jar` (e.g., `burpsuite_desktop_v2026.4.3.jar`)
3. Partially downloaded files (`.fdmdownload`, `.part`) will not be detected
4. Once detected, the label will show green "already the latest version (X.Y.Z)"

---

## ▸ Advanced Usage Tips

### Running with Custom JVM Options

**Increase memory allocation:**
```bash
java -Xmx1024m -jar nixon.jar
```

**Enable verbose class loading (see Instrumentation in action):**
```bash
java -verbose:class -jar nixon.jar
```

**Combine multiple options:**
```bash
java -Xmx1024m -verbose:class -jar nixon.jar
```

### CLI Auto-Run Examples

**Always run without version prompt:**
```bash
java -jar nixon.jar -a -i
```

**Run with custom name and auto-launch:**
```bash
java -jar nixon.jar -n "YourLicenseName" -a
```

### Logging and Debugging

**Save all output to a file:**

**Linux/macOS:**
```bash
java -jar nixon.jar 2>&1 | tee keygen-output.log
```

**Windows (PowerShell):**
```powershell
java -jar nixon.jar 2>&1 | Tee-Object -FilePath keygen-output.log
```

### Checking Your Java Installation

**Find Java executable path:**

**Linux/macOS:**
```bash
which java
readlink -f $(which java)
```

**Windows (Command Prompt):**
```cmd
where java
```

**Windows (PowerShell):**
```powershell
(Get-Command java).Path
```

**List all installed Java versions:**

**Linux:**
```bash
update-alternatives --list java
```

**macOS:**
```bash
/usr/libexec/java_home -V
```

**Windows:**
```powershell
dir "C:\Program Files\Java"
```

---

## ▸ Repository Structure

```
burpsuite-professional-keygen/
├── nixon.jar                    # Main keygen with Instrumentation Agent (v2.0)
├── README.md                    # This comprehensive guide
├── archive/
│   └── nixon_depreciated_archive_v1.0.jar  # Original v1.0 release (legacy)
```

---

## ⚙️ System Requirements

| Component | Windows | Linux | macOS |
|-----------|---------|-------|-------|
| **OS Version** | Windows 10/11 | Any modern distro | macOS 10.14+ |
| **Java** | OpenJDK 21.0.9+ | OpenJDK 21.0.9+ | OpenJDK 21.0.9+ |
| **Architecture** | x86_64 (64-bit) | x86_64 (64-bit) | x86_64 / ARM64 (Apple Silicon) |
| **Display** | GUI required | X11 or Wayland | Aqua (native) |
| **RAM** | 1 GB+ recommended | 1 GB+ recommended | 1 GB+ recommended |
| **Storage** | 500 MB free | 500 MB free | 500 MB free |

---

## ▸ Important Notes

- ‼ **Java 21 Recommended** - Designed specifically for Java 21 with full Instrumentation API support
- ‼ **Memory-Only Operations** - All bytecode modifications occur in RAM; no disk files are altered
- ‼ **Cross-Platform** - Works identically on Windows, Linux, and macOS
- ‼ **Educational Purpose** - This tool is for security research and educational purposes only
- 🔄 Keep your Java installation updated for security patches
- ▸ Some antivirus software may flag the tool - this is expected for bytecode manipulation utilities

---

## ▸ Security & Technical Details

### ASM Framework
This tool leverages the **ASM Bytecode Engineering Library**, a powerful framework for analyzing, modifying, and generating Java bytecode. The agent operates at the bytecode level, making it version-agnostic and resilient to obfuscation.

### Java Instrumentation API
The `java.lang.instrument` package provides the foundation for this tool's capabilities:
- **premain** method for agent initialization before application starts
- **ClassFileTransformer** for intercepting and modifying class loading
- **Retransformation** capabilities for runtime class modification
- **Dynamic attach** mechanism for hooking into running JVMs

### Heuristic Pattern Matching
Instead of hardcoding class names (which change between versions), the tool uses sophisticated heuristics:
- **Code complexity analysis** - Identifies methods by instruction count and structure
- **Instruction signature matching** - Recognizes specific bytecode patterns
- **Inheritance hierarchy inspection** - Traces class relationships
- **Method call graph analysis** - Maps inter-method dependencies
- **Constant pool examination** - Analyzes embedded strings and references

### Why This Approach Works
Traditional patching methods fail when:
- Class names are obfuscated
- Code is frequently updated
- Integrity checks detect file modifications

This tool overcomes these limitations by:
- Operating in memory (no file system traces)
- Using pattern matching (version independent)
- Hooking at JVM level (before integrity checks)

---

## ▸ Related Resources

- [BurpSuite Official Documentation](https://portswigger.net/burp/documentation)
- [OpenJDK 21 Release Notes](https://openjdk.org/projects/jdk/21/)
- [Java Instrumentation API Guide](https://docs.oracle.com/en/java/javase/21/docs/api/java.instrument/java/lang/instrument/package-summary.html)
- [ASM Bytecode Framework](https://asm.ow2.io/)
- [JVM Specification](https://docs.oracle.com/javase/specs/jvms/se21/html/index.html)

---

## ▸ Contributing

Contributions, issues, and feature requests are welcome!

1. Fork the repository
2. Create your feature branch: `git checkout -b feature/AmazingFeature`
3. Commit your changes: `git commit -m 'Add some AmazingFeature'`
4. Push to the branch: `git push origin feature/AmazingFeature`
5. Open a Pull Request

**Areas where contributions are especially welcome:**
- Platform-specific bug fixes
- Enhanced heuristic patterns
- Additional bytecode manipulation techniques
- Documentation improvements
- Translation to other languages

---

## ▸ License

This project is licensed under the Educational Use License - see the [LICENSE](LICENSE) file for details.

---

## ‼ Disclaimer

**This repository is for educational purposes and security research only.** It demonstrates the capabilities of the Java Instrumentation API and ASM library for runtime analysis and bytecode manipulation.

The authors and contributors of this repository:

- ✗ Do not endorse software piracy or license violations
- ✗ Are not liable for any misuse of this tool
- ✗ Do not provide support for illegal activities
- ✓ Recommend purchasing official licenses for commercial use
- ✓ Encourage responsible security research and ethical hacking
- ✓ Provide this tool "as-is" without any warranties

**Use only on software you own or have explicit permission to audit.**

Users are responsible for ensuring compliance with all applicable laws and software licenses in their jurisdiction. Unauthorized use of this tool may violate:
- Software licensing agreements
- Computer fraud and abuse laws
- Digital Millennium Copyright Act (DMCA)
- International copyright treaties

**Use at your own risk and responsibility.**

---

## ▸ Support

If you encounter issues:

1. **Check the troubleshooting section** for your specific platform above
2. **Review existing issues**: [GitHub Issues](https://github.com/Nixon-H/burpsuite-professional-keygen/issues)
3. **Open a new issue** with:
   - Operating system and version
   - Java version: `java -version`
   - Complete error message
   - Steps to reproduce
   - Screenshots (if applicable)

**Before opening an issue:**
- Ensure you've followed the installation guide for your platform
- Verify Java 21 is correctly installed
- Check that your system meets minimum requirements

---

## ★ Star History

If this tool helped you understand Java Instrumentation, bytecode manipulation, and reverse engineering concepts, please consider giving it a ⭐ star!

**Star the repo to:**
- Show appreciation for the work
- Help others discover this educational resource
- Support continued development and documentation

---

**Made with ♥ for the security research and reverse engineering community**

*Last updated: June 2026*
