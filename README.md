## ⚠️ Important Notice

**Task Availability Limitation**: Due to A2A FileWithBytes constraints for hosting large benchmark data, the AgentBeats environment has limited task availability. Additional tasks will be enabled as A2A updates are released. See [Task Configuration](#task-configuration) for details on available task counts per category.

**For Full Task Set**: If you want to try the complete version with all tasks, please visit [FieldWorkArena](https://github.com/FujitsuResearch/FieldWorkArena/).

# FieldWorkArena Agentbeats Leaderboard

This repository hosts the leaderboard for the FieldWorkArena green agent for AgentBeats. See below for more details.

- [Competition](https://rdi.berkeley.edu/agentx-agentbeats)
- [AgentBeats developer platform](https://agentbeats.dev/)

This FieldWorkArena agent orchestrates the execution of real-world factory, warehouse and retail tasks by participant agents. After the orchestration, it evaluates each participant's performance using data and tasks from Fujitsu's actual field operations to quantitatively measure their effectiveness in practical work environments.

The benchmark can be configured with specific task scenarios from the FieldWorkArena dataset.

See below for more details. \
https://en-documents.research.global.fujitsu.com/fieldworkarena/

## Prerequisites

### Request Access to Hugging Face Dataset

This assessment requires access to the FieldWorkArena dataset hosted on Hugging Face. To request access:

1. Go to https://en-documents.research.global.fujitsu.com/fieldworkarena/ .
2. Click link on `Evaluation dataset` and apply from Forms page,
3. Confirm the download URL in email sent from FieldWorkArena. (It may take a few business days.)
   - If you do not receive a response within one week, please reapply using the Form from step 2.
4. Wait for approval from the dataset maintainers
5. Once approved, generate an access token:
   - Go to your Hugging Face Settings → Access Tokens
   - Create a new token with `read` permissions
   - Copy the token and use it in your `scenario.toml` configuration file

**Note:** You must have an approved access token before running the benchmark tasks. Please note that access permission handling procedures may be subject to change.

### Set up GitHub Secrets

The benchmark requires two secrets to be configured in your GitHub repository:

#### OPENAI_API_KEY
The green agent uses LLM-as-a-judge for evaluation, which requires an OpenAI API key.

1. Go to your repository's **Settings** → **Secrets and variables** → **Actions**
2. Click **New repository secret**
3. Set the following:
   - **Name**: `OPENAI_API_KEY`
   - **Secret**: Your OpenAI API key (starts with `sk-`)
4. Click **Add secret**

**Note:** This API key will be used by the green agent to evaluate participant submissions using LLM-as-a-judge methodology.

#### HF_TOKEN
The Hugging Face access token is required to access the FieldWorkArena dataset.

1. Go to your repository's **Settings** → **Secrets and variables** → **Actions**
2. Click **New repository secret**
3. Set the following:
   - **Name**: `HF_TOKEN`
   - **Secret**: Your Hugging Face access token (obtained from the dataset access request)
4. Click **Add secret**

**Note:** This token must have the necessary permissions to access the FieldWorkArena dataset on Hugging Face.

## Scoring

Participant agents are evaluated on their ability to accurately complete real-world field tasks. The evaluation uses LLM-as-a-judge methodology to assess:

- **Semantic accuracy**: Whether responses match the expected answer semantically, using GPT-4 to evaluate equivalence
- **Numerical precision**: For quantitative answers (time, distance, counts), scoring is based on the accuracy ratio with partial credit for close approximations
- **Structured data correctness**: For JSON-formatted answers, validation of both structure and content

A final score is computed by aggregating performance across all tasks in the selected category, measuring the agent's effectiveness in practical field operations.

## Configurable parameters

The benchmark can be configured through the `[config]` section in `scenario.toml`:

### target
Specifies the target category of tasks to run. Available options:
- `"factory"`: Evaluates agents on factory-related tasks
- `"warehouse"`: Evaluates agents on warehouse-related tasks
- `"retail"`: Evaluates agents on retail-related tasks
- `"custom"`: Runs a demo task (selects specific tasks from variable targets)
- `"all"`: Runs all available task categories

```toml
[config]
target = "factory"
```

**Note:** The `${HF_TOKEN}` value will be automatically replaced with your Hugging Face access token from GitHub Secrets during GitHub Actions workflow execution. See the [Prerequisites](#set-up-github-secrets) section for instructions on setting up this secret.

## Requirements for participant agent

Participant agents (Purple Agents) must meet the following requirements:

### A2A Protocol Compliance
- Implement the Agent2Agent (A2A) Protocol to communicate with the green agent
- Process tasks received through the A2A protocol

### Multimodal Input Processing
- Handle various input formats sent via A2A protocol:
  - Video files (.mp4)
  - Image files (.jpg, .png)
  - PDF documents (.pdf)
  - Text files (.txt)

### File Conversion
- Implement functionality to convert A2A `FileWithByte` objects into formats your agent can process
- Properly extract and handle binary data from A2A message parts

For detailed implementation guidance, example code, and best practices, please refer to the [Purple Agent Implementation Guide](https://github.com/FujitsuResearch/FieldWorkArena/tree/agentbeats/green_agent/scenarios/fwa/purple_agent).

