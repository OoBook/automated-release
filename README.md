# Automated Release

[![Main](https://img.shields.io/github/actions/workflow/status/oobook/automated-release/main.yml?label=build&logo=github-actions)](https://github.com/oobook/automated-release/actions?workflow=main)
[![GitHub release (latest SemVer)](https://img.shields.io/github/v/release/oobook/automated-release?label=release&logo=GitHub)](https://github.com/oobook/automated-release/releases)
[![GitHub release (latest SemVer)](https://img.shields.io/github/release-date/oobook/automated-release?label=release%20date&logo=GitHub)](https://github.com/oobook/automated-release/releases)
![GitHub License](https://img.shields.io/github/license/oobook/automated-release)

A GitHub action that creates a new release according to the [Conventional Commits](https://www.conventionalcommits.org/en/v1.0.0/#specification).

## What's it do?

This action will automatically create a new release for your repository when a tag is sent as parameter or a tag push event but it must conform on the Semantic versioning.

## Inputs

| Name | Description | Obligatory | Default
| --- | --- | --- | --- |
| `create` | Run mode of the action | optional | false |
| `gh-token` | A GitHub token with `repo` scope. This is used to create release | required | |
| `tag` | It's required to use in events except the tag push | optional | |
| `create` | It's required to use in events except the tag push | optional | true |


## Outputs

| Name | Description |
| --- | --- | 
| `body` | The generated release body with the commits |
| `release-id` | If release is created, it gives the release ref id |
| `release-url` | If release is created, it gives the release html url |

## Usage
```yaml
name: Release

on:
  push:
    tags:
      - v*

permissions:
  contents: write

jobs:
  tag:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: oobook/automated-release@v1
        id: release-generation
        with:
          gh-token: ${{ github.token }}
```
          
```yaml
name: Release

on:
  push:
    branches:
      - main
    tags-ignore:
      - v*

permissions:
  contents: write
  
jobs:
  test:
    runs-on: ubuntu-latest
    name: Test
    steps:
      - name: Checkout
        uses: actions/checkout@v4
      - name: Tag
      - uses: oobook/automated-tag
        id: tag-generation
        with:
          gh_token: ${{ secrets.GITHUB_TOKEN }}
      - name: Release
        uses: oobook/automated-release@v1
        with:
          gh_token: ${{ secrets.GITHUB_TOKEN }}
          tag: "${{ steps.tag-generation.outputs.tag }}"
          prerelease: true
```