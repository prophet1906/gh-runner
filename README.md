# gh-runner

A GitHub Actions project that runs Docker commands on a scheduled basis and stores outputs in the repository.

## Features

- **Scheduled Docker Execution**: Multiple workflows running Docker containers daily at 8:00 AM UTC
- **OpenCode AI Integration**: Run OpenCode AI agent in Docker with customizable messages
- **GitHub Copilot Support**: Optional integration with GitHub Copilot for enhanced AI capabilities
- **Customizable Messages**: Configure custom messages via GitHub Secrets or Variables
- **Output Persistence**: Saves Docker output to timestamped files in `docker-outputs/` directory
- **Automatic Commits**: Automatically commits and pushes output files to the repository
- **Manual Triggers**: Supports manual workflow execution via GitHub UI

## Workflow Details

### 1. Daily Docker Hello World Workflow

**File**: `.github/workflows/daily-docker-run.yml`

**Schedule**: Runs every day at 8:00 AM UTC (cron: `0 8 * * *`)

**What it does**:
1. Checks out the repository
2. Runs Docker hello-world command
3. Captures output to a timestamped file: `docker-outputs/hello-world-YYYY-MM-DD_HH-MM-SS.txt`
4. Commits and pushes the output file back to the repository
5. Uploads output as a workflow artifact (30-day retention)

### 2. Daily OpenCode AI Run Workflow

**File**: `.github/workflows/daily-opencode-run.yml`

**Schedule**: Runs every day at 8:00 AM UTC (cron: `0 8 * * *`)

**What it does**:
1. Checks out the repository
2. Sets up OpenCode authentication with available AI providers (Anthropic/OpenAI/OpenCode Zen)
3. Optionally integrates with GitHub Copilot if token is provided
4. Creates/updates AGENTS.md configuration file
5. Runs OpenCode in Docker container with custom message (default: "hello, env, var")
6. Displays environment information and system details
7. Captures complete output to: `docker-outputs/opencode-output-YYYY-MM-DD_HH-MM-SS.txt`
8. Generates GitHub Actions summary with configuration status
9. Commits and pushes output files and configuration
10. Uploads artifacts (output + AGENTS.md) for review

**Key Features**:
- ✅ **Multi-Provider Support**: Works with Anthropic Claude, OpenAI, or OpenCode Zen
- ✅ **GitHub Copilot Integration**: Optional enhanced AI capabilities
- ✅ **Secure Credential Handling**: Uses GitHub Secrets for API keys
- ✅ **Environment Inspection**: Shows runner environment and available variables
- ✅ **Manual Triggers**: Support for custom messages via workflow_dispatch

## Configuration

### OpenCode AI Provider Setup

To use the OpenCode workflow, you need to configure at least one AI provider. Choose from:

#### Option 1: Anthropic Claude (Recommended)
1. Get an API key from [Anthropic Console](https://console.anthropic.com/)
2. Go to your repository's **Settings** → **Secrets and variables** → **Actions**
3. Click **New repository secret**
4. Name: `ANTHROPIC_API_KEY`
5. Value: Your Anthropic API key
6. Click **Add secret**

#### Option 2: OpenAI
1. Get an API key from [OpenAI Platform](https://platform.openai.com/)
2. Add as repository secret with name: `OPENAI_API_KEY`

#### Option 3: OpenCode Zen (Curated Models)
1. Sign up at [opencode.ai/auth](https://opencode.ai/auth)
2. Add billing details and copy your API key
3. Add as repository secret with name: `OPENCODE_ZEN_API_KEY`

### GitHub Copilot Integration (Optional)

To enable GitHub Copilot with OpenCode:
1. Generate a GitHub token with Copilot access
2. Add as repository secret with name: `GH_COPILOT_TOKEN`

**Note**: GitHub Copilot integration enhances OpenCode's capabilities but is not required for basic functionality.

### Basic Configuration

### Customizing Messages

#### Hello World Workflow Message

You can customize the message displayed during Docker hello-world execution:

**Via GitHub Secrets** (Recommended for sensitive data):
1. Go to your repository's **Settings** → **Secrets and variables** → **Actions**
2. Click **New repository secret**
3. Name: `HELLO_MESSAGE`
4. Value: Your custom message (e.g., "Hello from my automated workflow!")
5. Click **Add secret**

**Via GitHub Variables** (Recommended for non-sensitive data):
1. Go to **Settings** → **Secrets and variables** → **Actions** → **Variables** tab
2. Name: `HELLO_MESSAGE`
3. Value: Your custom message

**Default**: `"Hello from GitHub Actions!"`

#### OpenCode Workflow Message

You can customize the message sent to OpenCode:

**Via GitHub Secrets/Variables**:
- Name: `OPENCODE_MESSAGE`
- Value: Your message (default: "hello, env, var")

**Via Manual Workflow Trigger**:
1. Go to **Actions** tab → **Daily OpenCode Run**
2. Click **Run workflow**
3. Enter your custom message in the input field
4. Click **Run workflow**

This allows you to test different prompts without changing secrets.

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

### Hello World Workflow
1. Go to the **Actions** tab in your repository
2. Select **Daily Docker Hello World** workflow
3. Click **Run workflow**
4. Select the branch and click **Run workflow**

### OpenCode Workflow (with custom message support)
1. Go to the **Actions** tab in your repository
2. Select **Daily OpenCode Run** workflow
3. Click **Run workflow**
4. **Optional**: Enter a custom message (e.g., "Analyze the repository structure", "Show me available tools")
5. Select the branch and click **Run workflow**

## Output Files

Output files are stored in the `docker-outputs/` directory:

### Hello World Output
```
docker-outputs/hello-world-YYYY-MM-DD_HH-MM-SS.txt
```

Contains:
- Execution date and time
- Custom message (if configured)
- Complete Docker hello-world output

### OpenCode Output
```
docker-outputs/opencode-output-YYYY-MM-DD_HH-MM-SS.txt
```

Contains:
- Execution date and time
- Custom message sent to OpenCode
- GitHub Copilot status
- Environment information (Runner OS, Workflow, Repository)
- OpenCode Docker execution output
- System information from container
- Available environment variables (sanitized)
- Workspace contents

## Requirements

### For All Workflows
- GitHub Actions enabled for the repository
- Default `GITHUB_TOKEN` has write permissions (automatic in most cases)

### For OpenCode Workflow (Additional)
- At least one AI provider API key configured (Anthropic/OpenAI/OpenCode Zen)
- Optional: GitHub Copilot token for enhanced capabilities
- Docker support in GitHub Actions runners (automatically available)

## Viewing Results

### In Repository
Check the `docker-outputs/` directory for timestamped output files:
- `hello-world-*.txt` - Hello World workflow outputs
- `opencode-output-*.txt` - OpenCode workflow outputs

### In Actions Tab
1. View workflow runs and execution logs
2. Check GitHub Actions summaries (OpenCode workflow provides detailed status)
3. Download artifacts:
   - `docker-output-*` - Hello World outputs (30-day retention)
   - `opencode-output-*` - OpenCode outputs (30-day retention)
   - `agents-config-*` - AGENTS.md configuration (7-day retention)

### Commit History
Each run creates automatic commits:
- Hello World: `"Add Docker hello-world output for YYYY-MM-DD"`
- OpenCode: `"Add OpenCode run output for YYYY-MM-DD"`

## Troubleshooting

### OpenCode Workflow Issues

**Problem**: Workflow fails with authentication error  
**Solution**: Ensure at least one AI provider API key is configured:
- `ANTHROPIC_API_KEY` (recommended)
- `OPENAI_API_KEY`
- `OPENCODE_ZEN_API_KEY`

**Problem**: GitHub Copilot not working  
**Solution**: 
1. Verify `GH_COPILOT_TOKEN` is set correctly
2. Check token has required permissions
3. Copilot is optional - workflow will run without it

**Problem**: Docker container fails to start  
**Solution**: 
- Check Actions logs for specific error messages
- Verify Docker image `ghcr.io/sst/opencode:latest` is accessible
- GitHub Actions runners have Docker pre-installed

**Problem**: No output files generated  
**Solution**:
- Check if workflow completed successfully
- Review Actions logs for error messages
- Verify repository permissions allow commits

## Advanced Usage

### Custom AGENTS.md Configuration

The OpenCode workflow automatically creates an `AGENTS.md` file if it doesn't exist. You can customize this file to:
- Define specific instructions for OpenCode
- Set project-specific patterns and preferences
- Configure automated responses

Example custom AGENTS.md:
```markdown
# OpenCode Agent Configuration

## Project Context
This is a GitHub Actions automation project.

## Instructions
- When analyzing code, focus on workflow syntax and best practices
- Provide clear, actionable suggestions
- Always explain security implications
- Be concise in automated responses

## Preferences
- Use YAML for GitHub Actions
- Follow GitHub Actions best practices
- Prefer official actions from marketplace
```

### Scheduling Variations

Both workflows use the same schedule by default. To stagger them:

**Run OpenCode at different time:**
```yaml
# In .github/workflows/daily-opencode-run.yml
schedule:
  - cron: '0 14 * * *'  # 2:00 PM UTC
```

**Run OpenCode on specific days:**
```yaml
schedule:
  - cron: '0 8 * * 1,3,5'  # Monday, Wednesday, Friday at 8 AM UTC
```

## Security Best Practices

1. **Never commit API keys** - Always use GitHub Secrets
2. **Review OpenCode outputs** before relying on automated changes
3. **Limit workflow permissions** - Use minimal required permissions
4. **Rotate API keys regularly** - Update secrets periodically
5. **Monitor usage** - Check AI provider billing for unexpected usage
6. **Review artifacts** - Ensure no sensitive data in output files

## Contributing

Feel free to open issues or submit pull requests to improve the workflows!

## License

MIT