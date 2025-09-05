# Practice 2: Basic Workflow Events

Create a simple workflow that responds to multiple GitHub events and tells you which event triggered it.

---

## Exercise: Event Name Detector

### Scenario
You want to create a workflow that can be triggered in different ways and shows you exactly which event caused it to run. This is useful for understanding GitHub Actions triggers and debugging workflows.

### Requirements

**Events to Handle:**
- `workflow_dispatch` - Manual trigger (you click "Run workflow" button)
- `push` - When code is pushed to any branch
- `pull_request` - When pull requests are created, updated, or closed

### Job Configuration

**Single Job:** `echo`
- **Runner:** `ubuntu-latest`
- **Purpose:** Display which event triggered the workflow

### Step to Implement

**Step 1: Display Trigger Name**
Create a step that shows which event triggered the workflow using the GitHub context variable `${{ github.event_name }}`:

```bash
echo "I've been triggered by ${{ github.event_name }} event"
```
---

## Testing Instructions

### Test Each Event Type:

**1. Manual Trigger (workflow_dispatch):**
- Go to GitHub Actions tab in your repository
- Find your workflow
- Click "Run workflow" button
- Expected output: "I've been triggered by workflow_dispatch event"

**2. Push Event:**
- Make any change to your repository
- Commit and push to any branch
- Expected output: "I've been triggered by push event"

**3. Pull Request Event:**
- Create a new branch
- Make changes and open a pull request
- Expected output: "I've been triggered by pull_request event"
