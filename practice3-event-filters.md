# Practice 3: Event Filters

Create a workflow that only runs on specific branches and ignores documentation changes to optimize CI/CD performance.

---

## Exercise: Selective Branch and Path Filtering

### Scenario
You want to create a workflow that only runs when code changes are made to important branches, but you don't want it to run when only documentation is updated. This helps save CI/CD resources and reduces noise.

### Requirements

**Branch Filtering:**
- `dev` - Development branch for active development
- `release/*` - Any release branch (e.g., release/v1.0, release/v2.1)

**Path Filtering:**
- **Ignore:** Changes only in the `docs/` folder
- **Run:** For all other file changes

**Event Configuration:**
- `push` - When code is pushed to filtered branches

### Job Configuration

**Single Job:** `test-filters`
- **Runner:** `ubuntu-latest`
- **Purpose:** Demonstrate that the workflow runs only under the right conditions

### Steps to Include

1. **Simple success message**:
   ```bash
   echo "Event filters working! Triggered on ${{ github.ref_name }} branch"
   ```

---

## Expected Workflow File Structure

Your workflow should be saved as `.github/workflows/3-event-filters.yml`

### Key Filter Concepts to Implement:

**Branch Filters:**
```yaml
branches:
  - dev
  - 'release/*'
```

**Path Exclusion:**
```yaml
paths-ignore:
  - 'docs/**'
```

### Testing Your Workflow

**Should Trigger:**
- Push to `dev` branch with code changes
- Push to `release/v1.0` branch with any non-docs changes  

**Should NOT Trigger:**
- Push to `main` or `master` branch
- Push to `feature/xyz` branch
- Push to `dev` with only `docs/` changes
- Push to any branch with only documentation updates

---

## Success Criteria

✅ Workflow only runs on `dev` and `release/*` branches  
✅ Workflow ignores changes that only affect `docs/` folder  
✅ Workflow shows branch and event information when triggered  
✅ Workflow demonstrates efficient resource usage through filtering

---

## Learning Notes

**Branch Patterns:**
- Use `*` for wildcard matching in branch names
- `release/*` matches `release/v1.0`, `release/feature-x`, etc.

**Path Filtering:**
- `paths-ignore` prevents workflow from running
- `paths` (without ignore) only runs for specified paths
- Use `**` to match any subdirectory depth

**Performance Benefits:**
- Reduces unnecessary CI/CD runs
- Saves compute resources and costs
- Decreases notification noise for teams
