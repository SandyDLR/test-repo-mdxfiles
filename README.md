# Transifex GitHub MDX dynamic-path test

Test repository for the Transifex GitHub integration and `MARKDOWN_JSX` format.

## Questions

1. Does `source_files_expression: content/<dir>/index.mdx` match one-level routes only or nested routes too?
2. Does each resource sync using `file_format: MARKDOWN_JSX`?
3. Do generated Japanese translations preserve the route paths under `content/ja/<dir>/index.mdx`?
4. Do translation updates create/update pull requests without bypassing CODEOWNERS reviews?

## Setup

1. Create a new test GitHub repository and upload these files, including `.github/CODEOWNERS` and `transifex.yml`.
2. Replace `@YOUR_GITHUB_USERNAME` in `.github/CODEOWNERS` with an eligible actual reviewer (or your organization's review team).
3. In GitHub Settings > Branches (or Rulesets), protect `main`: require pull requests, at least one approval, and **Require review from Code Owners**. Ensure the integration is not allowed to bypass rules.
4. Create a Transifex file-based test project with source language English and target language Japanese, then install/link the GitHub integration.
5. Select this repository and its `main` branch. Use the configuration from `transifex.yml` and click **Test configuration** before saving.
6. Record which source files Transifex detects, then save and sync. Confirm the resource parser is `MARKDOWN_JSX`.
7. Translate at least one resource in Japanese to the chosen threshold. In the integration's Sync Content step select **Create a Pull Request**, not direct commit. For the first test use 100% translated (or a custom lower threshold for speed).
8. Check the PR's file paths, whether a CODEOWNER is requested, and whether merging is blocked until approval. Edit a translation and confirm whether the existing PR is updated.

## Expected candidates (to verify)

| Source | Expected destination if `<dir>` supports recursion |
|---|---|
| `content/getting-started/index.mdx` | `content/ja/getting-started/index.mdx` |
| `content/installation/index.mdx` | `content/ja/installation/index.mdx` |
| `content/guides/advanced/index.mdx` | `content/ja/guides/advanced/index.mdx` |
| `content/products/platform/security/index.mdx` | `content/ja/products/platform/security/index.mdx` |

## Interpretation

- All four detected with correct mapped paths: recursive matching is supported in this configuration.
- Only the two one-level routes detected: `<dir>` does not cover these nested routes in this configuration.
- No files detected: inspect YAML acceptance and format support in the integration's Test configuration step; do not infer anything about recursion yet.
- Some files detected but paths flattened or misplaced: path capture/substitution needs separate investigation.

## If recursion fails

Consider explicit `file` filters for each route as a reliable fallback, then test alternative dynamic expressions with support guidance. Do not assume `<dir>` is recursive without verification.

## Notes

- `MARKDOWN_JSX` is the documented MDX i18n format identifier.
- GitHub CODEOWNERS alone requests reviewers; branch protection or rulesets make approval mandatory.
- Transifex offers push thresholds of 100% translated/reviewed/proofread or custom, and can update an existing open PR. Depending on threshold changes it may close/reopen PRs.
- Japanese locale codes may differ by project; configure `language_mapping` if the repository's path requires a different code.
