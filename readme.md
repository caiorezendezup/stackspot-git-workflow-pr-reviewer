# LLM Prompt Action

This GitHub Action calls the StackSpot LLM API using OAuth authentication. It is designed to integrate LLM-powered prompts into your CI/CD workflows.

## Inputs

| Name            | Description                                 | Required |
|-----------------|---------------------------------------------|----------|
| tenant          | StackSpot tenant identifier                  | Yes      |
| client_id       | OAuth client ID                              | Yes      |
| client_secret   | OAuth client secret                          | Yes      |
| conversation_id | Conversation identifier (unique per run)     | Yes      |
| agent_id        | Agent identifier (from StackSpot Agents URL) | Yes      |
| user_prompt     | User prompt for the LLM                      | Yes      |

## Outputs

| Name     | Description         |
|----------|---------------------|
| response | LLM response text   |

## How to Get Your StackSpot Credentials

1. Go to [https://ai.stackspot.com](https://ai.stackspot.com) and create your account.
2. Click your profile (top right corner) → **Access Token** (left menu).
3. **Save the following:**
   - tenant
   - client_id
   - client_secret
4. Go back to the main page → **Contents** (open + options) → **Agents**.
5. **Save the agent id** from the URL (pattern: `https://ai.stackspot.com/agents/{agent_id}?tabIndex=0`).

- For `conversation_id`, use a unique string for each run unless you want to keep track of a conversation.
- For `user_prompt`, provide the prompt you want to send to the LLM.

## Example Usage

```yaml
jobs:
  llm-prompt:
    runs-on: ubuntu-latest
    steps:
      - uses: caiorezendezup/stackspot-git-workflow-pr-reviewer/.github/actions/llm-prompt@main
        id: llm
        with:
          tenant: ${{ secrets.STACKSPOT_TENANT }}
          client_id: ${{ secrets.STACKSPOT_CLIENT_ID }}
          client_secret: ${{ secrets.STACKSPOT_CLIENT_SECRET }}
          conversation_id: ${{ github.run_id }}-${{ github.job }}
          agent_id: ${{ secrets.STACKSPOT_AGENT_ID }}
          user_prompt: "Summarize the changes in this PR."
      - name: Show LLM Response
        run: echo "${{ steps.llm.outputs.response }}"
```

## Notes

- All sensitive values should be stored as GitHub Secrets.
- The action will fail if any required input is missing or empty.
- The `response` output contains the full text returned by the LLM.
