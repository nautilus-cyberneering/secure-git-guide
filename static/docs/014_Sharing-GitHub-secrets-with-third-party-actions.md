# GitHub Actions: sharing your secrets with third-party actions

One of the main advantages with GitHub Actions is that you can easily reuse actions in your workflows. You only need to search for what you need on the [marketplace](https://github.com/marketplace).
But it is also a big risk. When you start using third-party packages, you can easily get into a situation where you have to deal with a security issue. The more dependencies you have, the riskier it is.

At least, you should make sure you trust the action provider.

There is an even worse case when the action itself has a lot of dependencies. That is the case for [Megalinter](https://megalinter.github.io/).

MegaLinter is a _"tool for CI/CD workflows that analyzes consistency and quality"_. It has a lot of dependencies. When you use MegaLinter in your workflow you are sharing data not only with MegaLinter team but with hundreds of other teams with millions of lines of code. If you make a mistake and you share a secret with that action, your secrets will not be a secret anymore.

That is why we have been wondering if the MegaLinter itself could be a security problem and what could be the safest way tu run it.

- [Default installation for MegaLinter](#default-installation-for-megalinter)
- [GitHub token permissions](#github-token-permissions)
- [Setup the workflow with the minimum permissions](#setup-the-workflow-with-the-minimum-permissions)
- [Links](#links)
- [Credits](#credits)

## Default installation for MegaLinter

Supposing you created a new organization on GitHub, and a new repository and you wanted to use MegaLinter. After [installing](https://oxsecurity.github.io/megalinter/latest/installation/) it, it would have added a workflow with this step:

```yml
- name: MegaLinter
    id: ml
    uses: megalinter/megalinter/flavors/documentation@v5
    env:
        VALIDATE_ALL_CODEBASE: true
        GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

That means you are passing the `GITHUB_TOKEN` as an environment variable. MegaLinter is a docker action and it is called this way on the GitHub runners:

<!-- spell-checker: disable -->

```s
/usr/bin/docker run --name megalintermegalinterdocumentationv5_b424a0 --label 08450d --workdir /github/workspace --rm -e VALIDATE_ALL_CODEBASE -e GITHUB_TOKEN -e HOME -e GITHUB_JOB -e GITHUB_REF -e GITHUB_SHA -e GITHUB_REPOSITORY -e GITHUB_REPOSITORY_OWNER -e GITHUB_RUN_ID -e GITHUB_RUN_NUMBER -e GITHUB_RETENTION_DAYS -e GITHUB_RUN_ATTEMPT -e GITHUB_ACTOR -e GITHUB_WORKFLOW -e GITHUB_HEAD_REF -e GITHUB_BASE_REF -e GITHUB_EVENT_NAME -e GITHUB_SERVER_URL -e GITHUB_API_URL -e GITHUB_GRAPHQL_URL -e GITHUB_REF_NAME -e GITHUB_REF_PROTECTED -e GITHUB_REF_TYPE -e GITHUB_WORKSPACE -e GITHUB_ACTION -e GITHUB_EVENT_PATH -e GITHUB_ACTION_REPOSITORY -e GITHUB_ACTION_REF -e GITHUB_PATH -e GITHUB_ENV -e GITHUB_STEP_SUMMARY -e RUNNER_OS -e RUNNER_ARCH -e RUNNER_NAME -e RUNNER_TOOL_CACHE -e RUNNER_TEMP -e RUNNER_WORKSPACE -e ACTIONS_RUNTIME_URL -e ACTIONS_RUNTIME_TOKEN -e ACTIONS_CACHE_URL -e GITHUB_ACTIONS=true -e CI=true -v "/var/run/docker.sock":"/var/run/docker.sock" -v "/home/runner/work/_temp/_github_home":"/github/home" -v "/home/runner/work/_temp/_github_workflow":"/github/workflow" -v "/home/runner/work/_temp/_runner_file_commands":"/github/file_commands" -v "/home/runner/work/github-actions-secrets/github-actions-secrets":"/github/workspace" megalinter/megalinter-documentation:v5  "-v" "/var/run/docker.sock:/var/run/docker.sock:rw"
```

<!-- spell-checker: enable -->

You can check it on any of the [workflow executions](https://github.com/Nautilus-Cyberneering/github-actions-secrets/actions/workflows/mega-linter.yml) in [this repo](https://github.com/Nautilus-Cyberneering/github-actions-secrets).

As you can see there is an environment variable `-e GITHUB_TOKEN`. MegaLinter has 97 packages at the moment. That means there are a lot of dependencies that have access to the `GITHUB_TOKEN`.

## GitHub token permissions

The `GITHUB_TOKEN` is a [GitHub Action Token Secret](https://docs.github.com/en/actions/security-guides/automatic-token-authentication) that is automatically generated and is used to authenticate the GitHub API.

**But, what could MegaLinter and its dependencies do with that token?**

Currently, the [default permissions for all workflows](https://github.blog/changelog/2021-04-20-github-actions-control-permissions-for-github_token/) in any organization are:

```yml
permissions:
  actions: read|write
  checks: read|write
  contents: read|write
  deployments: read|write
  issues: read|write
  packages: read|write
  pull-requests: read|write
  repository-projects: read|write
  security-events: read|write
  statuses: read|write
```

If you do not overwrite those permissions for the MegaLinter workflow, the MegalInter will have full write access to the API when the workflow is executed by a maintainer in a local branch. For forked repositories, GitHub automatically changes the [permissions](https://docs.github.com/en/actions/security-guides/automatic-token-authentication#permissions-for-the-github_token) of the `GITHUB_TOKEN` to only "read". But you still have read access to all the data. In most cases, you probably do not need it.

So with that default configuration, any of the MegaLinter packages could use the token to create a new branch, create or modify a new workflow and export all your secrets.

**Could the MegaLinter or any other action export the secrets without modifying a workflow?**

Given that the MegaLinter or in general any other third-party action you are suing has access to the `GITHUB_TOKEN` with "contents: write" permission, what could that action do? could the action export the secrets without modifying a workflow just using the token?

The answer is No, it cannot. Even it the action has access to the `GITHUB_TOKEN` there is no way to directly get the secrets from the API. The only way to get the secrets is by modifying a workflow and export or print them.

Fir instance, MegaLinter could release a new minor version of their package with malicious code. They could update one of the embedded packages in the docker image and that package could contain malicious code to obtain secrets using the `GITHUB_TOKEN`. Since you normally use a major version for the action `megalinter/megalinter/flavors/documentation@v5` you would use the newer version on the next workflow execution.

Actions cannot obtain the secrets (including `GITHUB_TOKEN`) if you do not pass them explicitly. See the article [How GitHub Actions can get access to secrets](./013_How-github-actions-can-get-access-to-secrets.md). If the action has access to the `GITHUB_TOKEN` because you explicitly pass it, it could modify a workflow to export the secrets. The basic security rule for GitHub Actions is: if you can modify a branch you can get all the secrets linked to that branch which includes not only environment secrets but also repository and organization secrets.

## Setup the workflow with the minimum permissions

According to the [Principle of least privilege](https://en.wikipedia.org/wiki/Principle_of_least_privilege), we should grant MegaLinter only the permissions it needs. Depending on whether you want MegalInter to create comments on your PRs or auto-fix things you might need to give extra permissions to it.

There are some courses and articles explaining how to implement secure workflows with GitHub Actions. See the [links](#links) below.

Things you could do to make your GitHub organization more secure:

1. Change the default token permissions for the organization. You can [change the default permissions](https://docs.github.com/en/organizations/managing-organization-settings/disabling-or-limiting-github-actions-for-your-organization#configuring-the-default-github_token-permissions) granted to the `GITHUB_TOKEN` to read-only.

2. Remove all permissions on the workflow for the token. You can do it by adding the following to the `.github/workflows/mega-linter.yml` file: `permissions: {}`.

3. Gran each workflow only the permissions it needs.

Depending on what you want the MegaLinter to do you will need to grant some specific permissions. For example `pull-requests: write` if you want it to write comments on pull requests or `contents: write` if you want it to auto-fix your code and push the changes. As we mentioned before, the risky permission is `contents: write`.

> NOTICE: you do not even need to give `contents: read` permission to the `GITHUB_TOKEN` in order to checkout the code if the repo is a public repo.

But, what happens if you want to give MegaLinter `contents: write` permission? is there a way to avoid giving access to those 97 packages to your secrets?

## Solution 1: Using CODEOWNERS configuration

One of the solutions provided by GitHub is the [CODEOWNERS](https://docs.github.com/en/repositories/managing-your-repositorys-settings-and-features/customizing-your-repository/about-code-owners) file.

If actions can only get secrets from arguments and we are only passing the `GITHUB_TOKEN` to the action, the only way to get the secrets is by modifying a workflow to export the secrets. The CODEOWNERS file allows the repo admins to specify who should review changes on certain files. You could configure a team that has access to the `.\github\workflows` directory but this would not work because:

- CODEOWNERS only works on pull requests. And in that case, `contents: write` permission is not granted anyway.
- We are talking about branches created on the same repo by developers who already have write access. Even if the file requires a review by the user, the user is allowed to modify and review those files.

So there is no option to limit the write access to the workflow files.

## Solution 2: Using branch protection rules and environment secrets

You could avoid using repository level secrets. If all your secrets are [environment secrets](https://docs.github.com/en/actions/security-guides/encrypted-secrets#creating-encrypted-secrets-for-an-environment) the MegaLinter action would not have access to those secrets. Only the jobs linked to the environment could have access to those secrets.

We think this is a good practice anyway because normally secrets are related to one environment. But in some cases, having a repository secret makes sense, so this solution is not possible always.

As a general good practice you should try using the lowest scope possible for your secrets.

Repository secrets can be useful for secrets that are used in nearly every or every job within the repository, but we don't recommend them for anything that has write on a system or read to sensitive information.

## Solution 3: Using a different repository to run MegaLinter

If you want to run the MegaLinter, for example, on every push to the `main` branch, you could create a MegaLinter repository that has a workflow that runs MegaLinter for a different repo. It would be a kind of MegaLinter worker.

Although it might work, it does not seem to be an easy solution, both to implement and use.

Besides, the problem was we wanted to give MegaLinter write permissions to auto-fix and push errors and in this case, we still would need write access to the remote origin repo.

### Conclusion

There is no way to give MegaLinter write permission without trusting its 97 packages not to steal your secrets. Either you disable write permissions and fix things manually or you stick to a concrete docker image (hash) and you review carefully all the package updates.

Maybe the best approach is a combination of all the previous solutions adding branch protection rules and environment secrets.

- A `CODEOWNERS` file preventing Actions from both opening and approving a PR to modify Actions.
- A `GITHUB_TOKEN` with reduced privileges either by changing the defaults at the repository level or explicitly defining your required permissions at the workflow level.
- Environment protections isolating sensitive credentials from general purpose linters and unit test jobs. The MegaLiner could be run only for `develop` branch linked to `develop` environment without any sensitive secret.

Even with that configuration if you are a maintainer and you run MegaLinter with the "push" event MegaLinter could get access the all secrets. So, you should:

- Disable the MegaLinter workflow for the `push` event.
- Try to always use PRs from forks even by maintainers.

or:

- Disable auto-fix feature.

Disabling auto-fix feature to avoid giving MegaLinter write access is not a big problem since you can run MegaLinter locally before pushing your changes.

If you have an alternative solution, please do not hesitate to [open a new discussion](https://github.com/Nautilus-Cyberneering/secure-git-guide/discussions) on this repo.

You might argue that that's the same problem you have when you trust all your node dependencies. For example, you might be using some development dependencies on your tests. If you run tests when a developer pushes a new commit to the `main` branch, you are giving those dependencies access to those secrets. Maybe that's another example of things you could be doing wrongly.

In general, we found the environment secrets solution to be the best solution. You should only use organization or repository secrets for secrets that could be potentially captured by third-party development tools. And use those secrets with your custom actions, actions you completely trust or actions you review.

## Links

- [GitHub Course - Securing your workflows](https://lab.github.com/githubtraining/securing-your-workflows).
- [Accessing GH context in actions](https://github.community/t/accessing-gh-context-in-actions/206203).
- [Original discussion about this article](https://github.com/Nautilus-Cyberneering/github-actions-secrets/pull/4).

## Credits

Thank to [Constantin Bosse](https://github.com/cgbosse) and [Stephen Hosom](https://github.com/hosom) who carefully review the [original version of this article](https://github.com/Nautilus-Cyberneering/github-actions-secrets/pull/4).

## MegaLinter team comment

> Thanks a lot for this great article, its content is very interesting :)
>
> About trusting all the linters and their dependencies, I agree that risk zero does not exist, but since [MegaLinter](https://oxsecurity.github.io/megalinter/latest/) is part of [ox.security](https://www.ox.security), its own sources are watched by Ox services to detect security issues, including supply chain attacks
>
> ![image](https://user-images.githubusercontent.com/17500430/198858440-3c8c7a3d-60c8-4035-a8af-b97c96e385a8.png)
>
> [Nicolas Vuillamy](https://github.com/nvuillam)

[Back to home](./index.md)
