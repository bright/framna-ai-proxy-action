# framna-ai-proxy-action

GitHub composite action that authenticates to AWS via OIDC and assumes the
`framna-ai-proxy-prod-bedrock` IAM role. Use before any step that needs Bedrock
access (e.g. `claude-code-action` with `use_bedrock: "true"`).

## Prerequisites

1. Your repo must be in `cdk/allowed-repos.yaml` in `bright/framna-ai-proxy`.
   Open a PR there; after merge a CDK deployer runs
   `cdk deploy framna-ai-proxy-prod-core`.
2. The calling workflow/job must declare `permissions: id-token: write`.

## Usage

```yaml
permissions:
  id-token: write
  contents: write
  pull-requests: write

steps:
  - uses: actions/checkout@v4

  - uses: bright/framna-ai-proxy-action@v1
    with:
      account_id: ${{ vars.BEDROCK_ACCOUNT_ID }}

  - uses: anthropics/claude-code-action@beta
    with:
      use_bedrock: "true"
      model: us.anthropic.claude-sonnet-4-6
      direct_prompt: ${{ inputs.prompt }}
      github_token: ${{ secrets.GITHUB_TOKEN }}
```

Set `BEDROCK_ACCOUNT_ID` as a GitHub **variable** (not secret) at org or repo level.
For Framna the value is `374163054691`.

## Versioning

`v1` floats to `main`. Breaking changes (role name convention change) bump to `v2`.
