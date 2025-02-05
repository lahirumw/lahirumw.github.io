---
layout: post
title: Semantic-Release Automates Release Workflow with GitHub Actions
subtitle: 
cover-img:
thumbnail-img: 
share-img: /assets/img/path.jpg
tags: [Github, Github Actions, Semantic Release, Release Management, Cicd Pipeline]
author: keylearn
---

This is the second story about GitHub Actions. This application guides how to integrate [semantic-release](https://github.com/semantic-release/semantic-release) with github action to automate software versioning, generate release notes and notify in Slack.

[Semantic Release](https://github.com/semantic-release/semantic-release) is a way to automate the process of versioning and releasing software packages based on the [semantic versioning specification](https://semver.org/), generating the release notes, and publishing the package. The Semantic Versioning scheme consists of three numbers, Major.Minor.Patch, and it helps developers communicate the nature of changes in a release.

The key idea behind Semantic Release is to analyze the commit history in a version control system and automatically determine the next appropriate version number based on the nature of changes made since the last release. For example:

1. When developers make significant changes that may break backward compatibility, the Major version number is incremented.

2. When new features are added in a backward-compatible manner, the Minor version number is incremented.

3. When bug fixes or minor changes are made in a backward-compatible manner, the Patch version number is incremented.

By default, semantic-release uses [Angular Commit Message Conventions](https://github.com/angular/angular/blob/main/CONTRIBUTING.md#-commit-message-format). The commit message format can be changed with the preset or config options of the @semantic-release/commit-analyzer and @semantic-release/release-notes-generator plugins.

**Commit Message Header**

<type>(<scope>): <short summary>
  │       │             │
  │       │             └─⫸ Summary in present tense. Not capitalized. No period at the end.
  │       │
  │       └─⫸ Commit Scope: animations|bazel|benchpress|common|compiler|compiler-cli|core|
  │                          elements|forms|http|language-service|localize|platform-browser|
  │                          platform-browser-dynamic|platform-server|router|service-worker|
  │                          upgrade|zone.js|packaging|changelog|docs-infra|migrations|
  │                          devtools
  │
  └─⫸ Commit Type: build|ci|docs|feat|fix|perf|refactor|test
  
Type

Must be one of the following:

build: Changes that affect the build system or external dependencies (example scopes: gulp, broccoli, npm)
ci: Changes to our CI configuration files and scripts (examples: CircleCi, SauceLabs)
docs: Documentation only changes
feat: A new feature
fix: A bug fix
perf: A code change that improves performance
refactor: A code change that neither fixes a bug nor adds a feature
test: Adding missing tests or correcting existing tests

Alright, let’s get start the implementation. First, you have to create repository in GitHub and clone the same into your local machine. Then create a release.config.js file inside the repository folder, add below content.

~~~
const config = {
  branches: ['main'],
  plugins: [
    '@semantic-release/commit-analyzer',
    '@semantic-release/release-notes-generator',
 [
      "semantic-release-slack-bot",
      {
        "notifyOnSuccess": true,
        "notifyOnFail": true,
        "slackWebhook": "{slackwebhookurl}", #paste slack incoming web hook url here.
        "branchesConfig": [
          {
            "pattern": "master",
            "notifyOnSuccess": true,
            "notifyOnFail": true
          }
        ]
      }
    ],
 [
      "@semantic-release/changelog",
      {
        "changelogFile": "docs/CHANGELOG.md"
      }
    ],
    ["@semantic-release/git", {
      "assets": ["dist/*.js", "dist/*.js.map", "docs/CHANGELOG.md"],
      "message": "chore(release): ${nextRelease.version} [skip ci]\n\n${nextRelease.notes}"
    }],
    '@semantic-release/github',
    'semantic-release-github-actions-tags'
  ]
};

module.exports = config;
~~~

Let’s break down the configuration:

`branches: ['main']`: This specifies that the release process will be triggered only for the 'main' branch.

`plugins`: This is an array of plugins that will be executed in order during the release process. Each plugin performs a specific task related to versioning, changelog generation, and notifications.

`@semantic-release/commit-analyzer`: This plugin analyzes the commit messages to determine the type of changes (patch, minor, or major) since the last release.

`@semantic-release/release-notes-generator`: This plugin generates release notes based on the commit messages and the determined release type.

`semantic-release-slack-bot`: This plugin sends notifications to a Slack channel about the release process. It is configured with the "slackWebhook" URL, which should be replaced with a real Slack incoming webhook URL.

`notifyOnSuccess`: If set to `true`, a notification will be sent to Slack when the release process is successful.

`notifyOnFail`: If set to `true`, a notification will be sent to Slack when the release process fails.

`branchesConfig`: This section provides configuration options for specific branches. In this case, there is a configuration for the "master" branch.

`@semantic-release/changelog`: This plugin updates the CHANGELOG.md file with the changes made in the new release.

`@semantic-release/git`: This plugin commits the updated CHANGELOG.md file and other specified assets to the Git repository as part of the release process. The commit message includes the next version and release notes.

`@semantic-release/github`: This plugin publishes the release to GitHub, creating a new release with the determined version and release notes.

`semantic-release-github-actions-tags`: This plugin adds git tags to the repository for the new release.

Finally, the `module.exports = config;` statement exports the configuration object so that Semantic Release can use it during the release process.

Note: For this configuration to work, the necessary packages (plugins) must be installed as dependencies in the project. Now, install them.

~~~
npm install semantic-release @semantic-release/git @semantic-release/github -D
npm install @semantic-release/changelog -D
npm install semantic-release-slack-bot -D
npm install semantic-release-github-actions-tags
~~~

Next, create a Workflow File Inside your GitHub repository, create a new directory called `.github/workflows`. In that directory, create a YAML file (e.g., `ci.yml`) to define your workflow.

~~~
# release action with semantic release
name: Release
  
on:
  workflow_dispatch:

permissions:
  contents: write
  issues: write
  pull-requests: write

jobs:
  release:
    name: Release
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Use Node.js 18.x
        uses: actions/setup-node@v3
        with:
          node-version: '18'
      - name: Install dependencies
        run: npm ci
      - name: Semantic Release
        run: npx semantic-release
        env: 
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
          SEMANTIC_RELEASE_PACKAGE: ${{ github.event.repository.name }}
~~~
		  
Let’s break down each part of the actions file above:

`name: Release`: This is the name of the GitHub Actions workflow.

`on: workflow_dispatch`: This specifies the event that triggers the workflow. In this case, the workflow can be manually triggered using the "Run workflow" button on the GitHub repository's Actions tab.

`permissions`: This section specifies the permissions required for the workflow to run.

`jobs`: This section defines the individual jobs that compose the workflow.

`release`: The name of the job is "release."

`runs-on: ubuntu-latest`: This specifies that the job will run on a machine with the latest version of Ubuntu Linux.

`steps`: This section defines the individual steps that the job will execute.

`uses: actions/checkout@v3`: This step checks out the repository code in the workflow runner.

`name: Use Node.js 18.x`: This step sets up Node.js version 18.x on the workflow runner.

`uses: actions/setup-node@v3`: This action sets up the specified Node.js version on the runner.

`name: Install dependencies`: This step installs the project dependencies using `npm ci`.

`name: Semantic Release`: This step runs the Semantic Release tool to automate the versioning and release process.

`run: npx semantic-release`: This executes the Semantic Release tool using npx. It analyzes the commit history and determines the next version number based on the semantic versioning rules.

`env`: This block is used to set environment variables for the Semantic Release step.

`GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}`: This provides the GitHub token to the Semantic Release tool. The `secrets.GITHUB_TOKEN` is a GitHub Actions secret that is automatically provided by GitHub and grants the necessary permissions for the tool to interact with the repository.

`SEMANTIC_RELEASE_PACKAGE: ${{ github.event.repository.name }}`: This sets an environment variable with the name of the GitHub repository, which can be used by Semantic Release during the release process.

Now, You have done everything to automate your repository release workflow. Let commit the code and trigger the action in GiHub. Don’t forget to commit message standard mention above.

ex: `git commit -m "feat: add semanti release to automate the release workflow"`

That’s all for the story. Thank you for reading. Enjoy!