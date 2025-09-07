# Practice 5: Manual Workflow Triggers with Inputs

## Learning Objectives
- ✅ **Understand Manual Triggers** - Use `workflow_dispatch` to trigger workflows manually
- ✅ **Practice Workflow Inputs** - Accept parameters from users when triggering workflows
- ✅ **Learn Input Types** - Work with different input types (choice, string, boolean)
- ✅ **Access Input Values** - Use `github.event.inputs.*` to access user-provided values

## Exercise Overview
Create a manual deployment workflow that allows users to trigger deployments with custom parameters, similar to real-world deployment scenarios.

---

## Exercise: Build a Manual Deployment Workflow

### Scenario: Production Deployment Control
**Build a workflow that gives deployment teams control over when and how to deploy**

Your mission is to create a workflow that:
1. **Only runs when manually triggered** (not on push/PR)
2. **Accepts deployment parameters** from the user
3. **Validates and uses those parameters** in deployment steps
4. **Provides deployment feedback** showing what was deployed where

---

## Required Workflow Inputs

### Input 1: Target Environment
- **Type**: `choice` (dropdown selection)
- **Description**: "Choose target environment"
- **Required**: `true`
- **Default**: `staging`
- **Options**: `staging`, `production`, `development`

### Input 2: Application Version
- **Type**: `string` (text input)
- **Description**: "Version or tag to deploy (e.g., v1.2.3)"
- **Required**: `true`
- **Default**: `latest`

### Input 3: Skip Tests
- **Type**: `boolean` (checkbox)
- **Description**: "Skip testing phase (use with caution)"
- **Required**: `false`
- **Default**: `false`

### Input 4: Deployment Strategy
- **Type**: `choice` (dropdown selection)
- **Description**: "Deployment strategy"
- **Required**: `true`
- **Default**: `rolling`
- **Options**: `rolling`, `blue-green`, `canary`

### Input 5: Notification Channel
- **Type**: `string` (text input)
- **Description**: "Slack channel for notifications (optional)"
- **Required**: `false`
- **Default**: `#deployments`

---

## Workflow Structure Required

```yaml
name: Manual Deployment Controller
on:
  workflow_dispatch:
    inputs:
      # Your 5 inputs here (environment, version, skip_tests, strategy, channel)

jobs:
  validate-inputs:
    runs-on: ubuntu-latest
    steps:
      # Your validation steps here

  deploy:
    needs: validate-inputs
    runs-on: ubuntu-latest
    steps:
      # Your deployment steps here
      
  notify:
    needs: deploy
    runs-on: ubuntu-latest
    if: always()  # Run even if deployment fails
    steps:
      # Your notification steps here
```

---

## Job 1: Validate Inputs (`validate-inputs`)

**Purpose:** Validate user inputs and show what will be deployed

### Step 1: Display Input Summary
Show all user inputs in a formatted way:
```bash
echo "=== Deployment Summary ==="
echo "🎯 Target Environment: [USER_ENVIRONMENT_INPUT]"
echo "📦 Version: [USER_VERSION_INPUT]"
echo "🧪 Skip Tests: [USER_SKIP_TESTS_INPUT]"
echo "🚀 Strategy: [USER_STRATEGY_INPUT]"
echo "📢 Notification Channel: [USER_CHANNEL_INPUT]"
echo "👤 Triggered by: [USER_WHO_TRIGGERED]"
echo "⏰ Deployment Time: $(date)"
```

### Step 2: Environment-Specific Validation
Create conditional validation based on environment choice:
- **Production deployments**: Show warning message about production deployment
- **Staging deployments**: Show info about staging deployment
- **Development deployments**: Show debug info

### Step 3: Version Validation
Validate the version format and show deployment details:
```bash
echo "=== Version Validation ==="
echo "Deploying version: [USER_VERSION_INPUT]"
# Add simple validation logic
if [[ "[USER_VERSION_INPUT]" == "latest" ]]; then
  echo "⚠️  Using latest version - consider using specific version tags"
else
  echo "✅ Using specific version: [USER_VERSION_INPUT]"
fi
```

---

## Job 2: Deploy (`deploy`)

**Purpose:** Simulate deployment process using the user inputs

### Step 1: Pre-deployment Checks
Check deployment readiness:
```bash
echo "=== Pre-deployment Checks ==="
echo "Environment: [USER_ENVIRONMENT_INPUT]"
echo "Version: [USER_VERSION_INPUT]"
echo "Strategy: [USER_STRATEGY_INPUT]"
echo "Tests will be skipped: [USER_SKIP_TESTS_INPUT]"
```

### Step 2: Conditional Testing
Create a conditional step that only runs if tests are NOT skipped:
- **Condition**: Skip tests input is `false`
- **Action**: Run simulated tests
```bash
echo "🧪 Running tests for version [USER_VERSION_INPUT]..."
echo "✅ All tests passed!"
```

### Step 3: Environment-Specific Deployment
Create different deployment steps based on environment:
- **Production**: Show production deployment with extra confirmations
- **Staging**: Show staging deployment process
- **Development**: Show development deployment

### Step 4: Strategy-Based Deployment
Show different deployment messages based on selected strategy:
```bash
echo "=== Deploying with [USER_STRATEGY_INPUT] strategy ==="
# Add strategy-specific logic here
```

---

## Job 3: Notify (`notify`)

**Purpose:** Send deployment notifications (always runs, even on failure)

### Step 1: Deployment Status Check
Determine if deployment succeeded or failed:
```bash
echo "=== Deployment Status ==="
echo "Environment: [USER_ENVIRONMENT_INPUT]"
echo "Version: [USER_VERSION_INPUT]"
echo "Status: [CHECK_IF_PREVIOUS_JOB_SUCCEEDED]"
```

### Step 2: Notification Message
Create notification based on deployment outcome:
- **Success**: Show success message with deployment details
- **Failure**: Show failure message with troubleshooting info

### Step 3: Channel Notification
Show where notification would be sent:
```bash
echo "📢 Sending notification to: [USER_CHANNEL_INPUT]"
echo "🔗 Deployment details: ${{ github.server_url }}/${{ github.repository }}/actions/runs/${{ github.run_id }}"
```

---

## Instructions for Students

### 1. Create the Workflow File
- **Location**: `.github/workflows/5-manual-deployment-strategy.yml`
- **Name**: `Manual Deployment Controller`
- **Trigger**: `workflow_dispatch` only (no push/PR triggers)

### 4. Conditional Logic
Implement conditional steps that:
- Run different logic based on environment choice
- Skip or run tests based on boolean input
- Show different messages based on strategy choice

### 5. Testing Your Workflow

**Test 1: Manual Trigger - Production**
1. Go to GitHub Actions tab
2. Click "Manual Deployment Controller"
3. Click "Run workflow"
4. Select:
   - Environment: `production`
   - Version: `v2.1.0`
   - Skip Tests: `false`
   - Strategy: `blue-green`
   - Channel: `#prod-deployments`
5. Expected: Production warnings, tests run, blue-green deployment

**Test 2: Manual Trigger - Development**
1. Same process but select:
   - Environment: `development`
   - Version: `latest`
   - Skip Tests: `true`
   - Strategy: `rolling`
   - Channel: `#dev-team`
5. Expected: Development deployment, tests skipped, rolling strategy

**Test 3: Failure Simulation**
- Trigger with invalid inputs to test error handling
- Check that notification job always runs

---

## Bonus Challenges (Advanced)

1. **Add input validation** - Reject invalid version formats
2. **Environment protection** - Add extra confirmation for production deployments
3. **Matrix deployment** - Deploy to multiple regions based on input
4. **Rollback capability** - Add option to rollback previous deployment
5. **Integration with secrets** - Use different secrets based on environment

---

## Common Input Access Patterns

**Accessing Inputs:**
```yaml
${{ github.event.inputs.environment }}      # Choice input
${{ github.event.inputs.version }}          # String input  
${{ github.event.inputs.skip_tests }}       # Boolean input (true/false)
```

**Conditional Logic Examples:**
```yaml
# Environment-based conditions
if: github.event.inputs.environment == 'production'

# Boolean input conditions  
if: github.event.inputs.skip_tests == 'false'

# String input conditions
if: github.event.inputs.version != 'latest'

# Multiple conditions
if: github.event.inputs.environment == 'production' && github.event.inputs.skip_tests == 'false'
```

**Input Validation:**
```yaml
# Check if input exists
if: github.event.inputs.version != ''

# Ternary operator with inputs
echo "Mode: ${{ github.event.inputs.environment == 'production' && 'PROD' || 'DEV' }}"
```

---

## Real-World Application

This exercise simulates real deployment workflows where:
- **Operations teams** need control over when deployments happen
- **Different environments** require different deployment strategies
- **Version control** is critical for tracking what's deployed where
- **Testing** can be skipped in emergency situations (with proper approvals)
- **Notifications** keep teams informed of deployment status

Master this pattern and you'll be ready to build production-grade deployment workflows!

**Expected Completion Time**: 45-60 minutes
