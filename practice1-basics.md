# Practice 1: GitHub Actions Basics

Create a workflow with **3 simple jobs** that demonstrate core concepts:

### Job 1: Say Hello (`hello-job`)
**Purpose:** Learn basic workflow syntax and steps
**Steps:**
1. **Simple greeting**:
   ```bash
   echo "Hello, GitHub Actions!"
   ```
2. **Show current date**:
   ```bash
   date
   ```
3. **List current directory**:
   ```bash
   ls -la
   ```

### Job 2: Check System (`system-job`)
**Purpose:** Practice multi-line commands and basic system operations
**Steps:**
1. **Show system information** (multi-line command):
   ```bash
   echo "=== System Info ==="
   echo "Operating System: $(uname -s)"
   echo "Current User: $(whoami)"
   echo "Working Directory: $(pwd)"
   ```
2. **Create a simple file**:
   ```bash
   echo "This is a test file" > test.txt
   cat test.txt
   ```

### Job 3: Summary Report (`summary-job`)
**Purpose:** Understand job dependencies with `needs:`
**Dependencies:** Must wait for both `hello-job` and `system-job` to complete
**Steps:**
1. **Wait message**:
   ```bash
   echo "Both previous jobs completed successfully!"
   ```
2. **Final summary** (multi-line command):
   ```bash
   echo "=== Workflow Summary ==="
   echo "Repository: ${{ github.repository }}"
   echo "Branch: ${{ github.ref_name }}"
   echo "Triggered by: ${{ github.actor }}"
   echo "All jobs completed at: $(date)"
   ```