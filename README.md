# gh-runner

A GitHub Actions project that runs Docker commands on a scheduled basis and stores outputs in the repository.

## Features

- **Scheduled Docker Execution**: Runs `docker run hello-world` daily at 8:00 AM UTC
- **Customizable Messages**: Configure custom messages via GitHub Secrets or Variables
- **Output Persistence**: Saves Docker output to timestamped files in `docker-outputs/` directory
- **Automatic Commits**: Automatically commits and pushes output files to the repository
- **Manual Triggers**: Supports manual workflow execution via GitHub UI

## Workflow Details

### Daily Docker Run Workflow

**File**: `.github/workflows/daily-docker-run.yml`

**Schedule**: Runs every day at 8:00 AM UTC (cron: `0 8 * * *`)

**What it does**:
1. Checks out the repository
2. Runs Docker hello-world command
3. Captures output to a timestamped file: `docker-outputs/hello-world-YYYY-MM-DD_HH-MM-SS.txt`
4. Commits and pushes the output file back to the repository
5. Uploads output as a workflow artifact (30-day retention)

## Configuration

### Customizing the Hello World Message

You can customize the message displayed during Docker execution using either:

#### Option 1: GitHub Secrets (Recommended for sensitive data)
1. Go to your repository's **Settings** → **Secrets and variables** → **Actions**
2. Click **New repository secret**
3. Name: `HELLO_MESSAGE`
4. Value: Your custom message (e.g., "Hello from my automated workflow!")
5. Click **Add secret**

#### Option 2: GitHub Variables (Recommended for non-sensitive data)
1. Go to your repository's **Settings** → **Secrets and variables** → **Actions**
2. Click the **Variables** tab
3. Click **New repository variable**
4. Name: `HELLO_MESSAGE`
5. Value: Your custom message
6. Click **Add variable**

**Default**: If neither secret nor variable is set, it defaults to `"Hello from GitHub Actions!"`

### Changing the Schedule

Edit `.github/workflows/daily-docker-run.yml` and modify the cron expression:

```yaml
on:
  schedule:
    - cron: '0 8 * * *'  # minute hour day month weekday
```

**Examples**:
- `0 8 * * *` - 8:00 AM UTC daily (current setting)
- `0 12 * * *` - 12:00 PM UTC daily
- `30 14 * * 1` - 2:30 PM UTC every Monday
- `0 0 * * 0` - Midnight UTC every Sunday

**Note**: All times are in UTC timezone.

## Manual Execution

You can manually trigger the workflow:
1. Go to the **Actions** tab in your repository
2. Select **Daily Docker Hello World** workflow
3. Click **Run workflow**
4. Select the branch and click **Run workflow**

## Output Files

Output files are stored in the `docker-outputs/` directory with the format:
```
docker-outputs/hello-world-YYYY-MM-DD_HH-MM-SS.txt
```

Each file contains:
- Execution date and time
- Custom message (if configured)
- Complete Docker hello-world output

## Requirements

- GitHub Actions enabled for the repository
- Default `GITHUB_TOKEN` has write permissions (automatic in most cases)

## Viewing Results

1. **In Repository**: Check the `docker-outputs/` directory for timestamped output files
2. **In Actions Tab**: View workflow runs, logs, and download artifacts
3. **Commit History**: Each run creates a commit with the message "Add Docker hello-world output for YYYY-MM-DD"