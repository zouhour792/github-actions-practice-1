# Practice 4: GitHub Expressions & Context

## Learning Objectives
- ✅ **Understand Context Objects** - Access runtime information from different sources
- ✅ **Practice Expressions** - Use `${{ }}` syntax for dynamic logic
- ✅ **Learn Conditional Logic** - Make decisions based on context
- ✅ **Master String Functions** - Manipulate text data in workflows

## Exercise Overview
Create a workflow with **2 jobs** that explore different aspects of GitHub expressions and context objects.

---

## How to Create GitHub Secrets (Required for this Exercise)

### Step-by-Step Guide to Add Secrets

**📍 Step 1: Navigate to Repository Settings**
1. Go to your GitHub repository
2. Click **Settings** tab (top navigation bar)
3. In left sidebar, click **Secrets and variables**
4. Select **Actions**

**📍 Step 2: Add New Repository Secret**
1. Click **New repository secret** button (green button)
2. Enter **Name**: `TEST_API_KEY`
3. Enter **Value**: `my-secret-api-key-12345`
4. Click **Add secret**

**📍 Step 3: Add Another Secret**
1. Click **New repository secret** again
2. Enter **Name**: `DATABASE_URL`  
3. Enter **Value**: `postgresql://user:pass@localhost:5432/mydb`
4. Click **Add secret**

---

## Job 1: Context Objects Explorer (`context-explorer`)

**Purpose:** Learn about different context objects and their properties

### Environment Variables to Set for This Job:
```yaml
env:
  NODE_VERSION: '18'
  BUILD_ENV: 'development'
  APP_NAME: 'TestApp'
```

### Step 1: GitHub Context Information
Display basic GitHub context data:
```bash
echo "=== GitHub Context ==="
echo "Repository: ${{ github.repository }}"
echo "Branch: ${{ github.ref_name }}"
echo "Triggered by: ${{ github.actor }}"
echo "Event type: ${{ github.event_name }}"
echo "Run number: ${{ github.run_number }}"
echo "Workflow name: ${{ github.workflow }}"
```

### Step 2: Runner Environment Information
Show runner context details:
```bash
echo "=== Runner Environment ==="
echo "Operating System: ${{ runner.os }}"
echo "Architecture: ${{ runner.arch }}"
echo "Runner name: ${{ runner.name }}"
echo "Tool cache directory: ${{ runner.tool_cache }}"
echo "Temporary directory: ${{ runner.temp }}"
```

### Step 3: Environment Variables Test
Display environment context using the variables you set:
```bash
echo "=== Environment Variables ==="
echo "Node version from env: ${{ env.NODE_VERSION }}"
echo "Build environment: ${{ env.BUILD_ENV }}"
echo "Application name: ${{ env.APP_NAME }}"
echo "System PATH exists: ${{ env.PATH != '' }}"
echo "Home directory: ${{ env.HOME }}"
```

### Step 4: Secrets Context Test
Test secrets access (you need to create these secrets first):
```bash
echo "=== Secrets Availability Check ==="
echo "Has TEST_API_KEY secret: ${{ secrets.TEST_API_KEY != '' }}"
echo "Has DATABASE_URL secret: ${{ secrets.DATABASE_URL != '' }}"
echo "API key configured: ${{ secrets.TEST_API_KEY != '' && 'YES' || 'NO' }}"
echo "Database URL starts with 'postgresql': ${{ startsWith(secrets.DATABASE_URL, 'postgresql') }}"
```

**Environment block for this step:**
```yaml
env:
  TEST_API_KEY: ${{ secrets.TEST_API_KEY }}
  DATABASE_URL: ${{ secrets.DATABASE_URL }}
```

---

## Job 2: Conditional Logic & String Functions (`logic-operations`)

**Purpose:** Practice conditional expressions and string functions
**Dependency:** This job should run only after `context-explorer` completes successfully

### Step 1: Branch-based Conditional Steps

**Challenge:** Create three separate conditional steps. You need to figure out how to use `if:` statements with the right expressions.

**Requirement 1: Main Branch Detection**
- Create a step that only runs when the branch is exactly `main`
- Display: "This is the main branch - Production mode"

**Requirement 2: Feature Branch Detection**  
- Create a step that only runs when the branch name starts with `feature/`
- Display: "This is a feature branch - Development mode"

**Requirement 3: Other Branch Detection**
- Create a step that runs for any other branch (not main, not feature/)
- Display: "This is another type of branch - Testing mode"

### Step 2: Operating System Conditional Steps

**Challenge:** Create OS-specific steps that only run on certain runners

**Linux Step:** Only run if `runner.os` equals `Linux`
```bash
echo "🐧 Running on Linux - using apt package manager"
echo "Linux runner detected: ${{ runner.os }}"
```

**macOS Step:** Only run if `runner.os` equals `macOS`  
```bash
echo "🍎 Running on macOS - using homebrew package manager"
echo "macOS runner detected: ${{ runner.os }}"
```

**Windows Step:** Only run if `runner.os` equals `Windows`
```bash
echo "🪟 Running on Windows - using chocolatey package manager"  
echo "Windows runner detected: ${{ runner.os }}"
```

### Step 3: String Manipulation Functions

Transform and manipulate string data from context:
```bash
echo "=== String Transformations ==="
echo "Original ref: ${{ github.ref }}"
echo "Clean branch name: ${{ replace(github.ref, 'refs/heads/', '') }}"
echo "Uppercase branch: ${{ upper(github.ref_name) }}"
echo "Lowercase branch: ${{ lower(github.ref_name) }}"
echo "Branch contains 'main': ${{ contains(github.ref_name, 'main') }}"
echo "Branch starts with 'feature': ${{ startsWith(github.ref_name, 'feature/') }}"

echo "=== Conditional Logic ==="
echo "Environment type: ${{ github.ref_name == 'main' && 'production' || 'development' }}"
echo "Runner type: ${{ runner.os == 'Linux' && 'unix' || 'other' }}"
echo "Branch category: ${{ startsWith(github.ref_name, 'feature/') && 'feature' || 'stable' }}"
```

---

## Instructions for Students

### 1. Create the Workflow File
- **Location**: `.github/workflows/practice4-expressions.yml`
- **Name**: `Expressions & Context`
- **Trigger**: `push` and `workflow_dispatch` (for manual testing)

### 2. Workflow Structure Required
```yaml
name: Expressions & Context
on: 
  push:
  workflow_dispatch:

jobs:
  context-explorer:
    runs-on: ubuntu-latest
    env:
      NODE_VERSION: '18'
      BUILD_ENV: 'development'
      APP_NAME: 'TestApp'
    steps:
      # Your Step 1: GitHub Context Information
      # Your Step 2: Runner Environment Information  
      # Your Step 3: Environment Variables Test
      # Your Step 4: Secrets Context Test

  logic-operations:
    needs: context-explorer
    runs-on: ubuntu-latest
    steps:
      # Your Step 1: Branch-based Conditional Steps (3 separate conditional steps)
      # Your Step 2: OS Conditional Steps (3 separate conditional steps)
      # Your Step 3: String Manipulation Functions
```


### 3. Testing Your Workflow

**Test 1: Push to Main Branch**
- Expected: "This is the main branch" message appears
- Environment should show "production"

**Test 2: Create and Push to Feature Branch**  
- Create branch: `feature/test-expressions`
- Expected: "This is a feature branch" message appears
- Branch category should show "feature"

**Test 3: Create and Push to Other Branch**
- Create branch: `bugfix/test-fix`
- Expected: "This is another type of branch" message appears
- Environment should show "development"

**Test 4: Manual Trigger**
- Use workflow_dispatch to test without pushing
---

## Success Criteria

### Job 1: Context Explorer
✅ **GitHub context** displays repository and workflow information  
✅ **Runner context** shows OS and environment details  
✅ **Environment variables** from job env block appear correctly  
✅ **Secrets availability** checks work (requires creating secrets first)

### Job 2: Logic Operations  
✅ **Branch conditionals** show different messages based on branch name  
✅ **OS conditionals** show appropriate message for runner type  
✅ **String manipulations** transform text correctly  
✅ **Job dependency** works (Job 2 waits for Job 1)

### Advanced Success
✅ **Ternary operations** work for dynamic value selection  
✅ **Complex conditions** combine multiple context objects  

---

## Common Expressions Reference

**Context Access:**
```yaml
${{ github.repository }}      # owner/repo-name
${{ github.ref_name }}        # branch name (clean)
${{ runner.os }}              # Linux, Windows, macOS
${{ env.VARIABLE_NAME }}      # environment variables
${{ secrets.SECRET_NAME }}    # access secrets
```

**Conditional Logic:**
```yaml
if: github.ref_name == 'main'                    # Exact match
if: startsWith(github.ref_name, 'feature/')      # Prefix check
if: runner.os == 'Linux'                         # OS check
if: secrets.API_KEY != ''                        # Secret exists
```

**String Functions:**
```yaml
${{ upper(github.ref_name) }}                    # Uppercase
${{ lower(github.ref_name) }}                    # Lowercase
${{ replace(github.ref, 'refs/heads/', '') }}    # Replace text
${{ contains(github.repository, 'test') }}       # Check contains
```

**Ternary Operator:**
```yaml
${{ github.ref_name == 'main' && 'prod' || 'dev' }}        # condition && true || false
${{ runner.os == 'Linux' && 'unix' || 'other' }}           # OS-based selection
```

Good luck! This comprehensive exercise will give you hands-on experience with all the essential GitHub Actions expressions and context objects.
