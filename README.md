# 🚀 GoCheckAll — Automated Go Multi-Project Code Checker for Windows

> **Check all your Go projects in one command**  
> Catch bugs early before `go build` — perfect for Go + VS Code beginners

[![Platform](https://img.shields.io/badge/platform-Windows-blue?logo=windows)](https://github.com)
[![Go](https://img.shields.io/badge/Go-1.21+-00ADD8?logo=go)](https://go.dev/dl/)
[![License](https://img.shields.io/badge/license-MIT-green)](LICENSE)
[![VS Code](https://img.shields.io/badge/VS%20Code-ready-007ACC?logo=visualstudiocode)](https://code.visualstudio.com)

---

## 📋 Table of Contents

- [What is GoCheckAll?](#-what-is-gocheckall)
- [Features](#-features)
- [System Requirements](#-system-requirements)
- [Install Go](#-install-go)
- [Install VS Code](#-install-vs-code)
- [Install GoCheckAll](#-install-gocheckall)
- [How to Use](#-how-to-use)
- [Understanding the Output](#-understanding-the-output)
- [VS Code Task Integration](#-vs-code-task-integration)
- [Common Errors & Fixes](#-common-errors--fixes)
- [Related Tools & Resources](#-related-tools--resources)
- [FAQ](#-faq)
- [Author](#-author)

---

## 🎯 What is GoCheckAll?

**GoCheckAll** is a Windows command-line tool that automatically checks one or multiple Go projects **in parallel** (multi-threaded). It detects common mistakes that Go beginners often make — before you even try to compile.

### Why GoCheckAll?

When working with multiple small Go projects (DLL libraries, CLI tools, demos...), manually running `go vet ./...` in each folder is slow and tedious. GoCheckAll scans everything with a **single command**.

```bash
GoCheckAll.exe D:\GoProjects
```

That's it. All projects checked. Report generated.  
Log file opens automatically if errors are found.

---

## ✨ Features

### 🔍 Static Analysis — no compilation needed

| Check | Description |
|-------|-------------|
| ✅ **go.mod check** | Verifies `go.mod` exists in each project |
| ✅ **Package check** | Lists and counts `package` declarations per file |
| ✅ **Duplicate functions** | Detects functions/methods with the same name across files |
| ✅ **Duplicate exports** | Detects duplicate `//export` directives (CGo / DLL) |

### 🛠️ Official Go Tools — run automatically

| Tool | Description |
|------|-------------|
| 🔧 **go vet** | Official Go static analysis — catches suspicious code |
| 🔍 **staticcheck** | Advanced linter, auto-detected if installed |
| 🏗️ **build amd64** | Test build for Windows 64-bit |
| 🏗️ **build 386** | Test build for Windows 32-bit |

### ⚡ Parallel Execution

- Checks **multiple projects simultaneously** with multi-threading
- Configurable thread limit (default: 4)
- Summary report: **FAIL first, OK after** — errors are easy to spot
- Auto-opens detailed log file when errors are found
- **Works standalone** — just drop the `.exe` and run, no installation required

---

## 💻 System Requirements

| Component | Requirement |
|-----------|-------------|
| 🖥️ OS | Windows 10 / 11 (64-bit) |
| 🐹 Go | **1.21** or later |
| 📝 VS Code | Optional (recommended) |
| 🔨 GCC / MSYS2 | Only for CGo / DLL projects |

---

## 🐹 Install Go

### Step 1 — Download

Official download page: **https://go.dev/dl/**

| Version | File |
|---------|------|
| Windows 64-bit *(recommended)* | `go1.xx.x.windows-amd64.msi` |
| Windows 32-bit | `go1.xx.x.windows-386.msi` |

### Step 2 — Install

Run the `.msi` → Next → Next → Finish.  
Go automatically adds itself to your system `PATH`.

### Step 3 — Verify installation

Open **Command Prompt** or **PowerShell**:

```bash
go version
# Expected output: go version go1.xx.x windows/amd64

go env GOPATH
# Usually: C:\Users\YourName\go
```

### Step 4 — Install staticcheck *(recommended)*

```bash
go install honnef.co/go/tools/cmd/staticcheck@latest
```

`staticcheck.exe` will appear at `%USERPROFILE%\go\bin\`.  
GoCheckAll finds it automatically — no extra config needed.

### Step 5 — Install GCC *(only for CGo / DLL projects)*

Download **MSYS2**: **https://www.msys2.org/**

After installing, open the **MSYS2 UCRT64 terminal** and run:

```bash
# GCC 64-bit (amd64 builds)
pacman -S mingw-w64-ucrt-x86_64-gcc

# GCC 32-bit (386 builds)
pacman -S mingw-w64-i686-gcc
```

GoCheckAll looks for GCC at these default paths:
- 64-bit: `C:\msys64\ucrt64\bin`
- 32-bit: `C:\msys64\mingw32\bin`

> 💡 **Pure Go projects do not need GCC.**

---

## 📝 Install VS Code

### Step 1 — Download

**https://code.visualstudio.com/**

### Step 2 — Install the Go extension

`Ctrl+Shift+X` → search **"Go"** → install by **Go Team at Google**

Or from terminal:
```bash
code --install-extension golang.go
```

### Step 3 — Install Go tools

`Ctrl+Shift+P` → type `Go: Install/Update Tools` → select all → OK

VS Code installs: `gopls`, `dlv`, `staticcheck`, `goimports`, and more.

### Step 4 — Recommended settings

Create `.vscode/settings.json` in your workspace:

```json
{
    "go.useLanguageServer": true,
    "go.lintTool": "staticcheck",
    "go.formatTool": "goimports",
    "editor.formatOnSave": true,
    "[go]": {
        "editor.defaultFormatter": "golang.go"
    }
}
```

---

## 📦 Install GoCheckAll

### Download

1. Go to the **[Releases](../../releases)** page
2. Download `GoCheckAll.exe`
3. No installer needed — just place the `.exe` where you want it

### Recommended folder structure

Place `GoCheckAll.exe` inside `.vscode\` of your workspace so VS Code tasks can reach it:

```
MyWorkspace\
├── 📂 .vscode\
│   ├── ⚙️  GoCheckAll.exe    ← place here
│   └── 📄 tasks.json
│
├── 📂 project-one\
│   ├── 📄 go.mod
│   └── 📄 main.go
│
├── 📂 project-two\
│   ├── 📄 go.mod
│   └── 📄 main.go
│
└── 📂 project-three\
    ├── 📄 go.mod
    └── 📄 main.go
```

Run once → all three projects checked simultaneously.

---

## 🚀 How to Use

### Syntax

```
GoCheckAll.exe [directory] [max_threads]
```

| Argument | Description | Default |
|----------|-------------|---------|
| `directory` | Root folder to scan for Go projects | Auto-detected from EXE location |
| `max_threads` | Max number of parallel checks | `4` |

### Examples

```bash
# Auto-detect: walks up parent folders from EXE to find go.mod files
GoCheckAll.exe

# Check all Go projects inside a specific directory
GoCheckAll.exe D:\GoProjects

# Same, but only 2 threads (good for slower machines)
GoCheckAll.exe D:\GoProjects 2

# Large workspace — use 8 threads
GoCheckAll.exe D:\Up_Github_Developer 8

# Check just one project
GoCheckAll.exe D:\GoProjects\myproject 1
```

### What happens step by step

```
① Scans the target directory for sub-folders containing go.mod
② Starts one check thread per project (up to max_threads limit)
③ Each thread runs 8 checks in sequence:
     1. go.mod exists?
     2. Package declarations valid?
     3. Duplicate function names?
     4. Duplicate //export names?
     5. go vet ./...
     6. staticcheck ./... (if installed)
     7. go build (amd64)
     8. go build (386)
④ Waits for all threads to finish
⑤ Prints summary — FAIL projects first, OK projects after
⑥ If any project failed → opens detailed log file automatically
```

---

## 📊 Understanding the Output

### Log prefix legend

| Prefix | Meaning |
|--------|---------|
| `[INFO]` | General information |
| `[OK]  ` | ✅ Check passed |
| `[WARN]` | ⚠️ Warning — execution continues |
| `[FAIL]` | ❌ Error — needs to be fixed |

### Example — everything OK

```
=== GO MULTI PROJECT CHECKER ===
Root    : D:\GoProjects
go.exe  : C:\Program Files\Go\bin\go.exe
Threads : 4

Tim thay: 3 project(s)
  [1] myapi    (D:\GoProjects\myapi)
  [2] mylib    (D:\GoProjects\mylib)
  [3] mytool   (D:\GoProjects\mytool)

Bat dau kiem tra song song...

=====================================
         BAO CAO TONG HOP
=====================================

[OK]   myapi    (1.2s)
[OK]   mylib    (890ms)
[OK]   mytool   (1.1s)

=====================================
OK  : 3  |  FAIL: 0  |  Tong: 3
KET QUA: TAT CA OK
=====================================
```

### Example — project has errors

```
=====================================
         BAO CAO TONG HOP
=====================================

[FAIL] mylib  (450ms)
       [FAIL] Trung ham "Init" trong config.go (lan dau thay o: main.go)
       [FAIL] Exit code 1
       b.go:3:8: missing go.sum entry; run: go mod tidy

[OK]   myapi    (1.1s)
[OK]   mytool   (980ms)

=====================================
OK  : 2  |  FAIL: 1  |  Tong: 3
KET QUA: CO 1 PROJECT LOI
=====================================

Log chi tiet: C:\Users\You\AppData\Local\Temp\GoMultiProject.log
```

The detailed log opens automatically in your default text editor.

---

## 🔌 VS Code Task Integration

Run GoCheckAll with `Ctrl+Shift+B` by adding it as a VS Code build task.

Create `.vscode/tasks.json`:

```json
{
    "version": "2.0.0",
    "tasks": [
        {
            "label": "🔍 GoCheckAll — Check all projects",
            "type": "shell",
            "command": "chcp 65001 && ${workspaceFolder}\\.vscode\\GoCheckAll.exe",
            "group": {
                "kind": "build",
                "isDefault": true
            },
            "presentation": {
                "echo": false,
                "reveal": "always",
                "panel": "shared",
                "clear": true
            },
            "problemMatcher": []
        },
        {
            "label": "🔍 GoCheckAll — Check current folder only",
            "type": "shell",
            "command": "chcp 65001 && ${workspaceFolder}\\.vscode\\GoCheckAll.exe ${workspaceFolder} 1",
            "group": "build",
            "presentation": {
                "echo": false,
                "reveal": "always",
                "panel": "shared",
                "clear": true
            },
            "problemMatcher": []
        },
        {
            "label": "🏗️ go build",
            "type": "shell",
            "command": "go build ./...",
            "options": { "cwd": "${workspaceFolder}" },
            "group": "build",
            "problemMatcher": ["$go"]
        },
        {
            "label": "🧹 go mod tidy",
            "type": "shell",
            "command": "go mod tidy",
            "options": { "cwd": "${workspaceFolder}" },
            "group": "build",
            "problemMatcher": []
        }
    ]
}
```

| Shortcut | Action |
|----------|--------|
| `Ctrl+Shift+B` | Run default task (GoCheckAll — all projects) |
| `Ctrl+Shift+P` → `Tasks: Run Task` | Pick any task from the list |

---

## 🐛 Common Errors & Fixes

### ❌ Duplicate function name

```
[FAIL] Trung ham "Init" trong config.go (lan dau thay o: main.go)
```

**Cause:** Two files in the same package declare a function with the same name.  
**Fix:** Rename one of the functions, or move it to a separate package.

---

### ❌ Duplicate CGo export

```
[FAIL] Export trung: "MyFunc" xuat hien 2 lan
```

**Cause:** Two files both have `//export MyFunc`.  
**Fix:** Keep `//export` in only one file per exported name.

---

### ❌ Missing go.sum entry

```
missing go.sum entry for go.mod file; to add it:
go mod download github.com/xxx/yyy
```

**Fix:**
```bash
cd your-project-folder
go mod tidy
```

---

### ❌ Multiple packages in one directory

```
found packages main (a.go) and mypkg (b.go) in D:\...
```

**Cause:** Go does not allow two different packages in the same directory (exception: `_test` suffix).  
**Fix:** Move one package's files into a subdirectory.

---

### ⚠️ staticcheck not found

```
[WARN] Khong tim thay staticcheck.exe
```

**Fix:**
```bash
go install honnef.co/go/tools/cmd/staticcheck@latest
```

---

### ⚠️ GCC not found *(CGo / DLL projects only)*

```
[FAIL] gcc: error ...
```

**Fix:** Install MSYS2 + GCC → see [Install Go → Step 5](#step-5--install-gcc-only-for-cgo--dll-projects).

---

## 🔗 Related Tools & Resources

### 🐹 Go Language

| Resource | Link |
|----------|------|
| 🏠 Official site | https://go.dev |
| 📥 Download Go | https://go.dev/dl/ |
| 🎓 A Tour of Go | https://go.dev/tour/ |
| 📖 Standard library docs | https://pkg.go.dev |
| 💡 Go by Example | https://gobyexample.com |
| 📘 Effective Go | https://go.dev/doc/effective_go |
| 🎥 Go YouTube channel | https://www.youtube.com/@golang |

### 📝 VS Code

| Resource | Link |
|----------|------|
| 🏠 Download VS Code | https://code.visualstudio.com/ |
| 🔌 Go Extension | https://marketplace.visualstudio.com/items?itemName=golang.go |
| 📖 Go in VS Code guide | https://code.visualstudio.com/docs/languages/go |
| 📖 Tasks in VS Code | https://code.visualstudio.com/docs/editor/tasks |

### 🔧 Go Analysis Tools

| Tool | Description | Link |
|------|-------------|------|
| `go vet` | Built-in static analysis | Included with Go |
| `staticcheck` | Advanced linter (used by GoCheckAll) | https://staticcheck.dev |
| `golangci-lint` | Multi-linter aggregator | https://golangci-lint.run |
| `gopls` | Language server for VS Code | Included in Go extension |
| `delve` | Go debugger | https://github.com/go-delve/delve |

### 🔨 CGo & DLL Building

| Resource | Link |
|----------|------|
| 🏠 MSYS2 (GCC for Windows) | https://www.msys2.org/ |
| 📖 CGo documentation | https://pkg.go.dev/cmd/cgo |
| 📖 Go build modes (DLL) | https://pkg.go.dev/cmd/go#hdr-Build_modes |

### 📦 Go Modules

| Resource | Link |
|----------|------|
| 📖 Modules reference | https://go.dev/ref/mod |
| 🔍 Find Go packages | https://pkg.go.dev |
| 📊 Go Report Card | https://goreportcard.com |

---

## ❓ FAQ

**Q: Does GoCheckAll work on Linux or macOS?**

> Not currently. It is a Windows-only `.exe`. The Go tools it invokes (`go vet`, `staticcheck`) are cross-platform, but the GoCheckAll launcher itself requires Windows.

**Q: Do I need GCC to use GoCheckAll?**

> Only if your Go project uses **CGo** (calling C code from Go) or builds a **DLL / shared library**. Pure Go projects do not need GCC.

**Q: Can I use GoCheckAll in CI/CD pipelines?**

> Yes. GoCheckAll returns standard exit codes:
> - `exit 0` → all projects passed ✅
> - `exit 1` → at least one project has errors ❌

**Q: Where is the detailed log file saved?**

> `%TEMP%\GoMultiProject.log` — it opens automatically when errors are found. On most Windows machines this is `C:\Users\YourName\AppData\Local\Temp\`.

**Q: How do I check only one specific project?**

> Pass the project path directly with thread count 1:
> ```bash
> GoCheckAll.exe D:\GoProjects\myproject 1
> ```

**Q: GoCheckAll found 0 projects — why?**

> Make sure the target directory (or its direct sub-folders) contains a `go.mod` file. GoCheckAll scans only one level deep. If your `go.mod` is nested deeper, pass the exact folder as argument.

---

## 📜 License

MIT License — free to use, modify, and distribute.

---

## 👨‍💻 Author

**Kieu Manh**

- 📧 Email: kieumanh366377@gmail.com
- 🐙 GitHub: *(this repo)*

> Built while learning Go and VS Code.  
> If GoCheckAll saved you time, please give it a ⭐ **Star** — it really helps!

---

## 🤝 Contributing

Contributions are welcome:

1. Fork this repo
2. Create a branch: `git checkout -b feature/your-feature`
3. Commit your changes: `git commit -m "Add: your feature description"`
4. Push: `git push origin feature/your-feature`
5. Open a Pull Request

---

<div align="center">

**⭐ If GoCheckAll saved you time, please Star this repo! ⭐**

Made with ❤️ by Kieu Manh — Vietnam 🇻🇳

</div>
