# How GitHub Actions can get access to secrets

You can find the source code for the examples in this [repository](https://github.com/Nautilus-Cyberneering/github-actions-secrets).

- [Example using an embedded docker action](#example-using-an-embedded-docker-action)
- [Example using an embedded TypeScript action](#example-using-an-embedded-typescript-action)
- [Default permissions for GitHub token](#default-permissions-for-github-token)
- [Good practices handling secrets](#good-practices-handling-secrets)
- [Other questions](#other-questions)
- [Links](#links)
- [Credits](#credits)

[GitHub Actions](https://github.com/features/actions) has [contexts](https://docs.github.com/en/actions/learn-github-actions/contexts). Contexts are data structures where GitHub stores the information needed by workflows. There is one special context called [secrets context](https://docs.github.com/en/actions/learn-github-actions/contexts#secrets-context).

That context contains your secrets and a special secret called `GITHUB_TOKEN`, automatically added by GitHub. The permissions of the `GITHUB_TOKEN` depend on your organization's default configuration for the token and the event that triggered the workflow.

There are three ways to pass secrets to actions:

1. [Action inputs in the step](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#jobsjob_idstepswith).
2. [Arguments in the step](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#jobsjob_idstepswithargs). Only for docker actions.
3. [Environment variables](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#jobsjob_idstepsenv).

According to GitHub documentation, an action could have access to the `GITHUB_TOKEN` even if you do not explicitly pass the token in one of the previous ways.

> Important: An action can access the `GITHUB_TOKEN` through the `github.token` context even if the workflow does not explicitly pass the GITHUB_TOKEN to the action. As a good security practice, you should always make sure that actions only have the minimum access they require by limiting the permissions granted to the `GITHUB_TOKEN`. For more information, see "Permissions for the GITHUB_TOKEN."

You can read that message [here](https://docs.github.com/en/actions/security-guides/automatic-token-authentication#using-the-github_token-in-a-workflow).

As far as we know that's only possible because the action can also get a secret from its `action.yml` configuration file.

For example, in the GitHub [actions/checkout@v2](https://github.com/actions/checkout) action, you can pass the token as an implicit input, but if you do not pass it, the actions will take it from the context and set it as a default value. You can see how the [default value is taken from the context](https://github.com/actions/checkout/blob/2541b1294d2704b0964813337f33b291d3f8596b/action.yml#L24).

```yml
  token:
    description: >
        ...
    default: ${{ github.token }}
```

That is something not well documented. You can use contexts not only in the workflow `yml` files but also in the `action.yml` files.

You do not have access to all contexts in all places. See [this table](https://docs.github.com/en/actions/learn-github-actions/contexts#context-availability) to know what contexts are available and when.

We have create a demo repository for this article with some examples to understand the GitHub Actions lifecycle.

## Example using an embedded docker action

We have created an [embedded docker action](https://github.com/Nautilus-Cyberneering/github-actions-secrets/tree/main/.github/actions/env). Then we have added a [workflow tu run the action](https://github.com/Nautilus-Cyberneering/github-actions-secrets/blob/main/.github/workflows/test-env-action.yml). And finally, the action [prints all the environment variables](https://github.com/Nautilus-Cyberneering/github-actions-secrets/actions/workflows/test-env-action.yml) that it receives.

- [The embedded docker action](https://github.com/Nautilus-Cyberneering/github-actions-secrets/tree/main/.github/actions/env).
- [The workflow using the action](https://github.com/Nautilus-Cyberneering/github-actions-secrets/blob/main/.github/workflows/test-env-action.yml).
- [The output of the action](https://github.com/Nautilus-Cyberneering/github-actions-secrets/actions/workflows/test-env-action.yml).

This is the output:

```s
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
HOSTNAME=ae6e9d0aeb16
GITHUB_GRAPHQL_URL=https://api.github.com/graphql
GITHUB_REF_NAME=main
GITHUB_REF_PROTECTED=false
GITHUB_STEP_SUMMARY=/github/file_commands/step_summary_9f775e7c-222f-474c-af92-9e61206d7ce4
GITHUB_BASE_REF=
GITHUB_SERVER_URL=https://github.com
GITHUB_API_URL=https://api.github.com
GITHUB_ACTION_REPOSITORY=
RUNNER_ARCH=X64
RUNNER_WORKSPACE=/home/runner/work/github-actions-secrets
GITHUB_ACTIONS=true
GITHUB_JOB=print-env-vars
GITHUB_REF=refs/heads/main
GITHUB_ACTOR=josecelano
GITHUB_ACTION=__self
GITHUB_REPOSITORY_OWNER=Nautilus-Cyberneering
GITHUB_HEAD_REF=
GITHUB_WORKSPACE=/github/workspace
GITHUB_PATH=/github/file_commands/add_path_9f775e7c-222f-474c-af92-9e61206d7ce4
RUNNER_OS=Linux
RUNNER_TEMP=/home/runner/work/_temp
GITHUB_REPOSITORY=Nautilus-Cyberneering/github-actions-secrets
GITHUB_ACTION_REF=
GITHUB_ENV=/github/file_commands/set_env_9f775e7c-222f-474c-af92-9e61206d7ce4
RUNNER_TOOL_CACHE=/opt/hostedtoolcache
ACTIONS_RUNTIME_URL=https://pipelines.actions.githubusercontent.com/J2bBGbKRuIqd1wfytSShy42Isw56QMlCsoBc38NVOsni9X2pHC/
CI=true
HOME=/github/home
GITHUB_SHA=87de66d88ac9362ee029a768589d808f4fdad0a2
GITHUB_RUN_ID=2397754211
GITHUB_RUN_NUMBER=9
GITHUB_RUN_ATTEMPT=1
GITHUB_WORKFLOW=Print env vars in docker
ACTIONS_RUNTIME_TOKEN=***
GITHUB_EVENT_NAME=push
GITHUB_REF_TYPE=branch
GITHUB_EVENT_PATH=/github/workflow/event.json
RUNNER_NAME=Hosted Agent
GITHUB_RETENTION_DAYS=90
ACTIONS_CACHE_URL=https://artifactcache.actions.githubusercontent.com/J2bBGbKRuIqd1wfytSShy42Isw56QMlCsoBc38NVOsni9X2pHC/
```

We added some secrets to the repo ans as you can see there are none of them including the GitHub token.

## Example using an embedded TypeScript action

We have also create an [embedded TypeScript action](https://github.com/Nautilus-Cyberneering/github-actions-secrets/tree/main/.github/actions/typescript-print-env) to print all the environment variables.

This is the [workflow output](https://github.com/Nautilus-Cyberneering/github-actions-secrets/actions/workflows/test-typescript-print-env-action.yml):

/*spell-checker: disable*/

```s
CI=true
PIPX_HOME=/opt/pipx
DOTNET_NOLOGO=1
ImageVersion=20220605.1
GOROOT_1_17_X64=/opt/hostedtoolcache/go/1.17.11/x64
RUNNER_USER=runner
JAVA_HOME_8_X64=/usr/lib/jvm/temurin-8-jdk-amd64
HOME=/home/runner
SWIFT_PATH=/usr/share/swift/usr/bin
DOTNET_MULTILEVEL_LOOKUP=0
DEPLOYMENT_BASEPATH=/opt/runner
DEBIAN_FRONTEND=noninteractive
CONDA=/usr/share/miniconda
ANT_HOME=/usr/share/ant
LANG=C.UTF-8
AZURE_EXTENSION_DIR=/opt/az/azcliextensions
JAVA_HOME=/usr/lib/jvm/temurin-11-jdk-amd64
INVOCATION_ID=5d2ec45523744def866f5782b6bd2fd4
RUNNER_TOOL_CACHE=/opt/hostedtoolcache
GRAALVM_11_ROOT=/usr/local/graalvm/graalvm-ce-java11-22.1.0
LEIN_JAR=/usr/local/lib/lein/self-installs/leiningen-2.9.8-standalone.jar
ANDROID_NDK_HOME=/usr/local/lib/android/sdk/ndk-bundle
USER=runner
ImageOS=ubuntu20
POWERSHELL_DISTRIBUTION_CHANNEL=GitHub-Actions-ubuntu20
JAVA_HOME_17_X64=/usr/lib/jvm/temurin-17-jdk-amd64
GRADLE_HOME=/usr/share/gradle-7.4.2
VCPKG_INSTALLATION_ROOT=/usr/local/share/vcpkg
BOOTSTRAP_HASKELL_NONINTERACTIVE=1
PIPX_BIN_DIR=/opt/pipx_bin
RUNNER_TRACKING_ID=github_681ec59d-a26f-4c69-86a5-4b87f29a6f29
GOROOT_1_16_X64=/opt/hostedtoolcache/go/1.16.15/x64
GOROOT_1_18_X64=/opt/hostedtoolcache/go/1.18.3/x64
ANDROID_NDK_ROOT=/usr/local/lib/android/sdk/ndk-bundle
SELENIUM_JAR_PATH=/usr/share/java/selenium-server.jar
CHROME_BIN=/usr/bin/google-chrome
AGENT_TOOLSDIRECTORY=/opt/hostedtoolcache
ANDROID_SDK_ROOT=/usr/local/lib/android/sdk
ACCEPT_EULA=Y
JAVA_HOME_11_X64=/usr/lib/jvm/temurin-11-jdk-amd64
SGX_AESM_ADDR=1
ANDROID_NDK_LATEST_HOME=/usr/local/lib/android/sdk/ndk/24.0.8215888
GECKOWEBDRIVER=/usr/local/share/gecko_driver
NVM_DIR=/home/runner/.nvm
ANDROID_HOME=/usr/local/lib/android/sdk
XDG_CONFIG_HOME=/home/runner/.config
HOMEBREW_PREFIX=/home/linuxbrew/.linuxbrew
RUNNER_PERFLOG=/home/runner/perflog
HOMEBREW_NO_AUTO_UPDATE=1
JOURNAL_STREAM=8:22886
LEIN_HOME=/usr/local/lib/lein
CHROMEWEBDRIVER=/usr/local/share/chrome_driver
DOTNET_SKIP_FIRST_TIME_EXPERIENCE=1
PATH=/home/linuxbrew/.linuxbrew/bin:/home/linuxbrew/.linuxbrew/sbin:/home/runner/.local/bin:/opt/pipx_bin:/home/runner/.cargo/bin:/home/runner/.config/composer/vendor/bin:/usr/local/.ghcup/bin:/home/runner/.dotnet/tools:/snap/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin
PERFLOG_LOCATION_SETTING=RUNNER_PERFLOG
HOMEBREW_CELLAR=/home/linuxbrew/.linuxbrew/Cellar
HOMEBREW_REPOSITORY=/home/linuxbrew/.linuxbrew/Homebrew
GITHUB_ACTIONS=true
HOMEBREW_CLEANUP_PERIODIC_FULL_DAYS=3650
GITHUB_JOB=print-env-vars
GITHUB_REF=refs/heads/main
GITHUB_SHA=21d8327fb47121b483af88dc8df2dcde235a1167
GITHUB_REPOSITORY=Nautilus-Cyberneering/github-actions-secrets
GITHUB_REPOSITORY_OWNER=Nautilus-Cyberneering
GITHUB_RUN_ID=2496310873
GITHUB_RUN_NUMBER=7
GITHUB_RETENTION_DAYS=90
GITHUB_RUN_ATTEMPT=1
GITHUB_ACTOR=josecelano
GITHUB_WORKFLOW=Print env vars in TypeScript action
GITHUB_HEAD_REF=
GITHUB_BASE_REF=
GITHUB_EVENT_NAME=push
GITHUB_SERVER_URL=https://github.com
GITHUB_API_URL=https://api.github.com
GITHUB_GRAPHQL_URL=https://api.github.com/graphql
GITHUB_REF_NAME=main
GITHUB_REF_PROTECTED=false
GITHUB_REF_TYPE=branch
GITHUB_WORKSPACE=/home/runner/work/github-actions-secrets/github-actions-secrets
GITHUB_ACTION=__self
GITHUB_EVENT_PATH=/home/runner/work/_temp/_github_workflow/event.json
GITHUB_ACTION_REPOSITORY=
GITHUB_ACTION_REF=
GITHUB_PATH=/home/runner/work/_temp/_runner_file_commands/add_path_b3b81f35-7f4b-4f9c-9d56-c2ad7b770fc5
GITHUB_ENV=/home/runner/work/_temp/_runner_file_commands/set_env_b3b81f35-7f4b-4f9c-9d56-c2ad7b770fc5
GITHUB_STEP_SUMMARY=/home/runner/work/_temp/_runner_file_commands/step_summary_b3b81f35-7f4b-4f9c-9d56-c2ad7b770fc5
RUNNER_OS=Linux
RUNNER_ARCH=X64
RUNNER_NAME=GitHub Actions 3
RUNNER_TEMP=/home/runner/work/_temp
RUNNER_WORKSPACE=/home/runner/work/github-actions-secrets
ACTIONS_RUNTIME_URL=https://pipelines.actions.githubusercontent.com/J2bBGbKRuIqd1wfytSShy42Isw56QMlCsoBc38NVOsni9X2pHC/
ACTIONS_RUNTIME_TOKEN=***
ACTIONS_CACHE_URL=https://artifactcache.actions.githubusercontent.com/J2bBGbKRuIqd1wfytSShy42Isw56QMlCsoBc38NVOsni9X2pHC/
```

/*spell-checker: enable*/

## Default permissions for GitHub token

The `GITHUB_TOKEN` secret is added automatically to all workflows executions. You can remove all the permissions. By default it has all the permissions you define at the organization level.

You can [set the default permissions for the organization or repository](https://github.blog/changelog/2021-04-20-github-actions-control-permissions-for-github_token/).

> WARNING: by default `GITHUB_TOKEN` has all the permissions!

## Good practices handling secrets

Given that actions do not have direct access to secrets, the only way to give access to them is by explicitly passing them. But you have to be careful because there could ways to do that without you even knowing it. You can read [here](https://docs.github.com/en/actions/security-guides/security-hardening-for-github-actions#accessing-secrets) different ways to accidentally pass secrets to actions.

For example, if you define an environment variable at the workflow level like this:

```s
env:
  GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

All the actions in your workflow will have access to the token.

Some good practices proposed by [Stephen Hosom](https://github.com/hosom) are:

- Environments are a great way to isolate secrets. Combine environments with environment rules that dictate which branches that they can be accessed from for the best isolation of your secrets.

- As a general rule, try to use the lower scope for secrets: environment, repository, organization.

- Repository secrets can be useful for secrets that are used in nearly every or every job within the repository, but we don't recommend them for anything that has write on a system or read to sensitive information.

- Restricting write access where possible is GOOD. This isn't always possible, so there are mitigating controls you can use. Using a CODEOWNERS file prevents unauthorized edits to workflows. This isn't one size fits all. Some workflows can be triggered based on branches and won't require a request reviewer to approve.

- Restricting an environment to only run off of a specific branch is the biggest defense you have. This means that even if your workflow includes a write token and CODEOWNERS can be bypassed because the action can execute from a branch, it still won't have access to your secrets.

And other things to consider:

- If you have a job linked to an environment you should act as if all actions in that job could have access to all the environment secrets. So do not mix actions with different levels of access to your secrets.

## Other questions

**Do actions only have have access to the secrets when you pass the secrets to them via inputs, arguments, environment vars or input default values in action.yml?**

Yes, they do. But the only secret accessible via default values in an `action.yml` is the `GITHUB_TOKEN`, all other secrets must be passed explicitly.

The one exception is reusable workflows that can pass all secrets, rather than specifying individual ones using the `secrets: inherit` option: <https://docs.github.com/en/actions/using-workflows/reusing-workflows#passing-inputs-and-secrets-to-a-reusable-workflow>.

**Is there a way to completely disable the GITHUB_TOKEN in a workflow?**

No, there is not.

**Is there a way to completely disable access to secrets in a workflow?**

As you can see in this [workflow example](https://github.com/Nautilus-Cyberneering/github-actions-secrets/blob/main/.github/workflows/disable-github-token.yml), even if you disable the permissions for the `GITHUB_TOKEN` you are still able to get the secrets because you get them from the context, not using the `GITHUB_TOKEN`.

You can check any of the [workflow executions](https://github.com/Nautilus-Cyberneering/github-actions-secrets/actions/workflows/disable-github-token.yml). In the "Setup Job" you will find a line like this:

```s
GITHUB_TOKEN Permissions
  Metadata: read
```

With the permissions assigned to the `GITHUB_TOKEN`.

**Are contexts stored on disk in the GitHub runners?**

As far as we know, they are not. But something we miss from the documentation it's a better explanation of the lifecycle of the runners and how it is the communication between GitHub and runners. We assume that the context for jobs is used only at the Github's servers. The secrets are shared with the runner only before executing a job and they are not stored on the disk.

The documentation says the secrets are deleted from memory when the job is done.

> Although GitHub Actions scrubs secrets from memory that are not referenced in the workflow (or an included action), the GITHUB_TOKEN and any referenced secrets can be harvested by a determined attacker.

*It's best to assume that a malicious action has access to any secrets or information that the runner has for that a job - regardless of if the secrets are on disk or in memory. Actions are not sandboxed within a workflow job, the security boundary is only between jobs/runs.*

See [accessing secrets](https://docs.github.com/en/actions/security-guides/security-hardening-for-github-actions#accessing-secrets).

## Links

- [GitHub Course - Securing your workflows](https://lab.github.com/githubtraining/securing-your-workflows).
- [Accessing GH context in actions](https://github.community/t/accessing-gh-context-in-actions/206203).
- [Original discussion about this article](https://github.com/Nautilus-Cyberneering/github-actions-secrets/pull/4).

## Credits

Thank to [Constantin Bosse](https://github.com/cgbosse) and [Stephen Hosom](https://github.com/hosom) who carefully review the [original version of this article](https://github.com/Nautilus-Cyberneering/github-actions-secrets/pull/4).

[Back to home](./index.md)
