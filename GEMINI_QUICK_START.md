# Gemini CLI Task Runner - Quick Start Guide

## Overview

The Gemini CLI Task Runner is a GitHub Action that uses Google's Gemini Pro AI model to execute custom queries against your repository. It runs in a containerized environment and automatically commits results back to your repository.

## Prerequisites

1. **Gemini API Key**: Get one from [Google AI Studio](https://makersuite.google.com/app/apikey)
2. **GitHub Repository**: With Actions enabled
3. **Repository Permissions**: Contents write permission (automatically granted)

## Setup (5 minutes)

### Step 1: Add API Key to GitHub Secrets

1. Go to your repository on GitHub
2. Click **Settings** → **Secrets and variables** → **Actions**
3. Click **New repository secret**
4. Name: `GEMINI_API_KEY`
5. Value: Paste your Gemini API key
6. Click **Add secret**

### Step 2: Verify Workflow File

The workflow file should be at: `.github/workflows/gemini-cli-runner.yml`

This file was automatically created and includes:
- Scheduled runs (daily at 9:00 AM UTC)
- Manual trigger with custom inputs
- Multiple output format support
- Automatic result commits

### Step 3: Test the Workflow

1. Go to **Actions** tab in your repository
2. Select **Gemini CLI Task Runner**
3. Click **Run workflow**
4. Enter a test query: `"Analyze this repository and provide insights"`
5. Select output format: `markdown`
6. Click **Run workflow**

## Usage

### Running Manual Tasks

**From GitHub UI:**
1. Navigate to **Actions** → **Gemini CLI Task Runner**
2. Click **Run workflow**
3. Enter your query in the "query" field
4. Select output format (text, json, or markdown)
5. Click **Run workflow**

**Scheduled Runs:**
- Runs automatically every day at 9:00 AM UTC
- Uses default query: "Analyze this repository structure and provide a summary"
- Can customize via `GEMINI_QUERY` secret/variable

### Example Queries

#### Code Review
```
Query: "Review the GitHub Actions workflows and identify potential improvements or security issues"
Format: markdown
```

#### Documentation
```
Query: "Generate a comprehensive project overview including architecture, key components, and usage instructions"
Format: markdown
```

#### Security Analysis
```
Query: "Analyze the codebase for security vulnerabilities, hardcoded secrets, or unsafe practices"
Format: text
```

#### Architecture Analysis
```
Query: "Examine the project structure and suggest architectural improvements for better maintainability"
Format: text
```

#### Dependency Review
```
Query: "List all dependencies, their purposes, and identify any that are outdated or have known vulnerabilities"
Format: json
```

#### Commit Analysis
```
Query: "Analyze the last 10 commits and summarize the development activity and key changes"
Format: markdown
```

## Output Locations

### Results File
- **Path**: `gemini-outputs/gemini-result-YYYY-MM-DD_HH-MM-SS.[format]`
- **Contains**: Complete Gemini response formatted according to selected type
- **Retention**: Committed to repository + 30-day artifact

### Execution Log
- **Path**: `gemini-outputs/gemini-log-YYYY-MM-DD_HH-MM-SS.txt`
- **Contains**: Detailed execution logs, timestamps, exit codes
- **Retention**: Committed to repository + 30-day artifact

### Context File
- **Path**: `gemini-context/repo-context.txt`
- **Contains**: Repository metadata, structure, recent commits
- **Retention**: Committed to repository + 7-day artifact

## Viewing Results

### In Repository
Check the `gemini-outputs/` directory for timestamped result files.

### In Actions Tab
1. Click on the workflow run
2. View **Summary** for result preview
3. Download artifacts for complete output
4. Check logs for execution details

### Via Artifacts
Download from Actions run:
- `gemini-result-*` - Task results
- `gemini-log-*` - Execution logs
- `gemini-context-*` - Repository context

## Advanced Configuration

### Custom Default Query

Set a custom default query for scheduled runs:

**Via GitHub Secrets:**
1. Go to **Settings** → **Secrets and variables** → **Actions**
2. Add secret: `GEMINI_QUERY`
3. Value: Your default query

**Via GitHub Variables:**
1. Go to **Settings** → **Secrets and variables** → **Actions** → **Variables**
2. Add variable: `GEMINI_QUERY`
3. Value: Your default query

### Change Schedule

Edit `.github/workflows/gemini-cli-runner.yml`:

```yaml
on:
  schedule:
    - cron: '0 9 * * *'  # Current: 9 AM UTC daily
```

**Examples:**
- `0 12 * * *` - Noon UTC daily
- `0 9 * * 1` - 9 AM UTC every Monday
- `0 9 * * 1,3,5` - 9 AM UTC Monday, Wednesday, Friday

### Modify Output Format

The workflow supports three output formats:

1. **markdown** (default) - Best for documentation and reports
2. **text** - Plain text, good for simple outputs
3. **json** - Structured data, useful for parsing

Change via workflow_dispatch input or set `OUTPUT_FORMAT` variable.

## Troubleshooting

### Workflow fails with authentication error
**Solution**: Verify `GEMINI_API_KEY` is correctly set in repository secrets.

### No output file generated
**Solution**: 
1. Check `gemini-outputs/gemini-log-*.txt` for errors
2. Verify API key has proper permissions
3. Check Gemini API quota hasn't been exceeded

### Query produces unexpected results
**Solution**:
1. Review query clarity and specificity
2. Check repository context in `gemini-context/repo-context.txt`
3. Try different output formats
4. Ensure query is relevant to repository contents

### Python package installation fails
**Solution**: Check Actions logs - the workflow automatically installs `google-generativeai` package.

## Best Practices

### Writing Effective Queries

1. **Be Specific**: Clear, focused queries get better results
   - ❌ "Check the code"
   - ✅ "Review GitHub Actions workflows for security best practices"

2. **Provide Context**: Help Gemini understand your goals
   - ❌ "Improve this"
   - ✅ "Suggest performance optimizations for the data processing pipeline"

3. **Set Expectations**: Clarify what you want
   - ❌ "Look at the database"
   - ✅ "Analyze database schema and suggest normalization improvements"

4. **Use Structured Output**: For parseable results, request specific formats
   - ✅ "List all API endpoints in JSON format with descriptions"

### Security Considerations

1. **Protect API Keys**: Always use GitHub Secrets, never commit keys
2. **Review Results**: Check Gemini outputs before relying on them
3. **Monitor Usage**: Track API usage to avoid unexpected costs
4. **Limit Exposure**: Don't query sensitive data or credentials

### Cost Management

1. **Targeted Queries**: Focused queries use fewer tokens
2. **Schedule Wisely**: Adjust frequency based on needs
3. **Monitor Quotas**: Check Google AI Studio for usage
4. **Use Manual Triggers**: Run on-demand instead of scheduled for testing

## Common Use Cases

### Daily Code Quality Checks
Schedule daily runs with queries about code quality, test coverage, or documentation completeness.

### Pre-Release Reviews
Manually trigger comprehensive analysis before releases to identify potential issues.

### Onboarding Documentation
Generate up-to-date project overviews for new team members.

### Security Audits
Regular security analysis to identify vulnerabilities or compliance issues.

### Technical Debt Tracking
Periodic analysis of code quality and refactoring opportunities.

## Support & Resources

- **Gemini API Documentation**: [Google AI Studio](https://ai.google.dev/)
- **GitHub Actions Docs**: [docs.github.com/actions](https://docs.github.com/en/actions)
- **Repository Issues**: Open an issue for bugs or feature requests
- **Workflow Logs**: Check Actions tab for detailed execution logs

## Quick Reference Commands

### Check Workflow Status
```bash
gh workflow view "Gemini CLI Task Runner"
```

### Trigger Workflow via CLI
```bash
gh workflow run gemini-cli-runner.yml \
  -f query="Your query here" \
  -f output_format="markdown"
```

### List Recent Runs
```bash
gh run list --workflow=gemini-cli-runner.yml --limit 5
```

### Download Latest Artifact
```bash
gh run download --name gemini-result-latest
```

---

**Ready to start?** Go to the Actions tab and run your first Gemini task!
