# gh-runner

A GitHub Actions project that runs AI-powered Docker commands on a scheduled basis and stores outputs in the repository.

## Features

- **Scheduled Docker Execution**: Multiple workflows running Docker containers daily
- **OpenCode AI Integration**: Run OpenCode AI agent in Docker with customizable messages
- **Gemini CLI Task Runner**: Execute custom queries using Google's Gemini AI model
- **GitHub Copilot Support**: Optional integration with GitHub Copilot for enhanced AI capabilities
- **Customizable Messages**: Configure custom messages via GitHub Secrets or Variables
- **Output Persistence**: Saves output to timestamped files in repository directories
- **Automatic Commits**: Automatically commits and pushes output files to the repository
- **Manual Triggers**: Supports manual workflow execution via GitHub UI with custom inputs
- **Multiple Output Formats**: Support for text, JSON, and Markdown outputs

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

### 3. Tech News Digest Generator (Gemini Multi-Model)

**File**: `.github/workflows/gemini-cli-runner.yml`

**Schedule**: Runs daily at 9:00 AM UTC (cron: `0 9 * * *`)

**What it does**:
1. Checks out the repository
2. Implements **intelligent multi-tier model fallback strategy**
3. Generates comprehensive tech news digest covering:
   - 🚨 Critical security updates and CVE disclosures
   - 🔬 Latest research breakthroughs from academia
   - 🛠️ Production-ready technology releases
   - 🏗️ Architectural evolution and patterns
   - 📊 Industry signals and trends
   - 🔮 Emerging technologies
4. Automatically retries with high-quality models before falling back
5. Maximizes daily quota (RPD) usage on premium models
6. Saves interactive HTML digest: `tech-digest-YYYY-MM-DD.html`
7. Commits results to dedicated `tech-digest` branch
8. Provides detailed execution summary with model usage stats

**Key Features**:
- ✅ **Intelligent Model Fallback**: 6-tier cascading strategy from premium to economy models
- ✅ **Quota Maximization**: Aggressive retry logic exhausts daily quotas before downgrading
- ✅ **Quality-First Approach**: Always attempts highest quality models first
- ✅ **Rate Limit Resilience**: Handles 429/403 errors with exponential backoff
- ✅ **Premium Models**: gemini-2.5-pro (6/50 RPD) and gemini-2.5-flash (4/250 RPD)
- ✅ **Comprehensive Logging**: Tracks model used, attempts required, quality tier
- ✅ **Interactive Output**: Beautiful dark-themed HTML with collapsible sections
- ✅ **Multi-Source Intelligence**: ArXiv, CVE databases, web search, documentation
- ✅ **Manual Triggers**: Customize focus areas and items per category

**Model Tier Strategy**:
1. **Premium Tier** 🏆: gemini-2.5-pro (5 retries × 60s), gemini-2.5-flash (4 retries × 60s)
2. **High Quality** ⭐: gemini-2.0-flash (3 retries × 60s)
3. **Balanced** ⚖️: gemini-2.5-flash-lite (2 retries × 60s), gemini-2.0-flash-lite (2 retries × 60s)
4. **Economy** 💰: gemini-2.5-flash-tts (1 retry × 60s)

**Note:** All models use consistent 1-minute delays between retries to properly respect rate limits.

**See**: [MODEL_FALLBACK_STRATEGY.md](MODEL_FALLBACK_STRATEGY.md) for complete details

## Configuration

### Gemini CLI Setup

To use the Gemini CLI Task Runner workflow:

1. Get a Gemini API key from [Google AI Studio](https://makersuite.google.com/app/apikey)
2. Go to your repository's **Settings** → **Secrets and variables** → **Actions**
3. Click **New repository secret**
4. Name: `GEMINI_API_KEY` (or `GOOGLE_API_KEY`)
5. Value: Your Gemini API key
6. Click **Add secret**

**Default Query**: "Analyze this repository structure and provide a summary"

You can customize the query via:
- **GitHub Secret/Variable**: `GEMINI_QUERY`
- **Manual Trigger**: Use workflow_dispatch with custom query input

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

#### Gemini CLI Query

You can customize the query sent to Gemini:

**Via GitHub Secrets/Variables**:
- Name: `GEMINI_QUERY`
- Value: Your query (default: "Analyze this repository structure and provide a summary")

**Via Manual Workflow Trigger**:
1. Go to **Actions** tab → **Gemini CLI Task Runner**
2. Click **Run workflow**
3. Enter your custom query in the "query" input field
4. Select output format (text, json, or markdown)
5. Click **Run workflow**

**Example Queries**:
- "Analyze this repository and provide insights about its structure and purpose"
- "Review the GitHub Actions workflows and suggest improvements"
- "Examine recent commits and summarize changes"
- "Identify potential security issues in the codebase"
- "Generate documentation for this project"

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

### Gemini CLI Task Runner (with custom query support)
1. Go to the **Actions** tab in your repository
2. Select **Gemini CLI Task Runner** workflow
3. Click **Run workflow**
4. **Required**: Enter your task query (e.g., "Review the workflows and suggest optimizations")
5. **Optional**: Select output format (text, json, or markdown - default: markdown)
6. Select the branch and click **Run workflow**

## Output Files

### Hello World Output
Stored in `docker-outputs/hello-world-YYYY-MM-DD_HH-MM-SS.txt`

Contains:
- Execution date and time
- Custom message (if configured)
- Complete Docker hello-world output

### OpenCode Output
Stored in `docker-outputs/opencode-output-YYYY-MM-DD_HH-MM-SS.txt`

Contains:
- Execution date and time
- Custom message sent to OpenCode
- GitHub Copilot status
- Environment information (Runner OS, Workflow, Repository)
- OpenCode Docker execution output
- System information from container
- Available environment variables (sanitized)
- Workspace contents

### Gemini CLI Output
Stored in `gemini-outputs/` directory:

**Result File**: `gemini-outputs/gemini-result-YYYY-MM-DD_HH-MM-SS.[format]`

Contains:
- Task execution date and time
- Query sent to Gemini
- Complete Gemini Pro model response
- Formatted according to selected output type (text/json/markdown)

**Log File**: `gemini-outputs/gemini-log-YYYY-MM-DD_HH-MM-SS.txt`

Contains:
- Execution timestamps
- Query details
- Docker container setup logs
- Gemini CLI installation logs
- Execution summary and exit codes

**Context File**: `gemini-context/repo-context.txt`

Contains:
- Repository metadata (name, branch, run number)
- Directory structure (limited depth)
- Recent commit history (last 10 commits)

## Requirements

### For All Workflows
- GitHub Actions enabled for the repository
- Default `GITHUB_TOKEN` has write permissions (automatic in most cases)

### For OpenCode Workflow (Additional)
- At least one AI provider API key configured (Anthropic/OpenAI/OpenCode Zen)
- Optional: GitHub Copilot token for enhanced capabilities
- Docker support in GitHub Actions runners (automatically available)

### For Gemini CLI Workflow (Additional)
- Gemini API key configured (GEMINI_API_KEY or GOOGLE_API_KEY)
- Docker support in GitHub Actions runners (automatically available)
- Python 3.11+ container support (handled automatically)

## Viewing Results

### In Repository
Check output directories for timestamped files:
- `docker-outputs/hello-world-*.txt` - Hello World workflow outputs
- `docker-outputs/opencode-output-*.txt` - OpenCode workflow outputs
- `gemini-outputs/gemini-result-*.[format]` - Gemini task results
- `gemini-outputs/gemini-log-*.txt` - Gemini execution logs
- `gemini-context/repo-context.txt` - Repository context for Gemini

### In Actions Tab
1. View workflow runs and execution logs
2. Check GitHub Actions summaries (OpenCode and Gemini workflows provide detailed status)
3. Download artifacts:
   - `docker-output-*` - Hello World outputs (30-day retention)
   - `opencode-output-*` - OpenCode outputs (30-day retention)
   - `agents-config-*` - AGENTS.md configuration (7-day retention)
   - `gemini-result-*` - Gemini task results (30-day retention)
   - `gemini-log-*` - Gemini execution logs (30-day retention)
   - `gemini-context-*` - Repository context (7-day retention)

### Commit History
Each run creates automatic commits:
- Hello World: `"Add Docker hello-world output for YYYY-MM-DD"`
- OpenCode: `"Add OpenCode run output for YYYY-MM-DD"`
- Gemini CLI: `"Add Gemini CLI task results for YYYY-MM-DD"`

## Troubleshooting

### Gemini CLI Workflow Issues

**Problem**: Workflow fails with API authentication error  
**Solution**: Ensure Gemini API key is configured:
- `GEMINI_API_KEY` (preferred)
- `GOOGLE_API_KEY` (fallback)
- Get key from [Google AI Studio](https://makersuite.google.com/app/apikey)

**Problem**: Python package installation fails  
**Solution**: 
- Check Actions logs for specific error messages
- Verify network connectivity to PyPI
- Container should automatically install `google-generativeai`

**Problem**: Query produces unexpected results  
**Solution**:
- Review the query syntax and clarity
- Check repository context in `gemini-context/repo-context.txt`
- Try different output formats (text, json, markdown)
- Verify Gemini API quota hasn't been exceeded

**Problem**: Output file not generated  
**Solution**:
- Check execution logs in `gemini-outputs/gemini-log-*.txt`
- Verify Gemini API key has proper permissions
- Review Python script errors in Actions logs

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

### Using Gemini for Code Analysis

The Gemini CLI workflow can be particularly useful for:

**Code Review Tasks:**
```
Query: "Review the GitHub Actions workflows in this repository and suggest security improvements"
Format: markdown
```

**Documentation Generation:**
```
Query: "Generate comprehensive documentation for this project based on the code structure and README"
Format: markdown
```

**Architecture Analysis:**
```
Query: "Analyze the project architecture and suggest improvements for scalability"
Format: text
```

**Dependency Analysis:**
```
Query: "Review all dependencies and identify potential security vulnerabilities or outdated packages"
Format: json
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