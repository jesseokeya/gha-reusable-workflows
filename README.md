# Workflows
A repository for template Github Action workflows for common tasks across my personal projects

## File Structure
```
.
├── .github
│   ├── pull_request_template.md
│   └── workflows
│       ├── ci-golang.yml
│       ├── codeql-go.yml
│       ├── codeql-javascript.yml
│       ├── gcp-push-artifact-registry.yml
│       ├── gcp-push-container-registry.yml
│       └── send-metrics-to-datadog.yml
├── .gitignore
├── CODEOWNERS
├── LICENSE
└── README.md
```

## Reusable Workflows
This repository introduces DRY Github Action workflows that can be used across repositories.

## Tags
This repo uses [annotated tags](https://git-scm.com/book/en/v2/Git-Basics-Tagging#_annotated_tags), which typically include a message indicating why the tag change occurred.

It is recommended to create a minor tag when adding any new functionality, but is required to add a major tag when a breaking change is added, as per [semantic versioning](https://semver.org/).
A tag can be attached to the branch being worked on, and once merged will apply to the merge in `main` branch.
To add a tag on branch (e.g. after changes have been merged to `main`):
```
git tag -a v1.0 -m "added a breaking change that does X and Y"
```
If the tag has already been associated with a different commit and you would like to move it to a new commit (force), use the following:
```
git tag -fa v1.0 -m "added a breaking change that does X and Y"
```
> The above command will  force the tag `v1.0` to be moved to a new commit. You will need to manually delete the tag from github (must have admin permissions to do so)
> You cannot force push over an existing tag in github

Tags are not pushed to `origin` by default so when pushing ensure to include `--tags`:
```
git push origin HEAD --tags
```


## Usage
These workflows can be easily imported by any repository by pointing the `uses` parameter of the `job` to a workflow here. Example for a repository:
```
name: Test & Deploy

on: push

jobs:
  test:
    uses: jesseokeya/workflows/.github/workflows/ci-golang.yml@main
    with:
      default_branch: master
    secrets: inherit
```

## Testing
New/existing workflows on different branches can be tested by pointing caller workflows to specific versions. This can be done by updating the `uses` parameter mentioned above. For example, to test the `ci-golang.yml` workflow on the `test-101` branch of this repository:
```
...

jobs:
  my_job_name:
    uses: jesseokeya/workflows/.github/workflows/ci-golang.yml@test-101

...
```
We can reference reusable workflow files using one of the following syntaxes:

1. For reusable workflows in public repositories:

```
{owner}/{repo}/.github/workflows/{filename}@{ref}
```
2. For reusable workflows in the same repository:
```
./.github/workflows/{filename}
```

`{ref}` can be any of:
- SHA
- a release tag, or
- a branch name

Using the commit SHA is the safest for stability and security. For more information, see ["Security hardening for GitHub Actions."](https://docs.github.com/en/actions/learn-github-actions/security-hardening-for-github-actions#reusing-third-party-workflows) If you use the second syntax option (without `{owner}/{repo} and @{ref}`) the called workflow is from the same commit as the caller workflow.