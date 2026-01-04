<!--
SPDX-FileCopyrightText: 2020-2022 Mineiros GmbH <hello@mineiros.io>
SPDX-FileCopyrightText: 2025 Radek Janik <cyberwassp@gmail.com>

SPDX-License-Identifier: Apache-2.0
-->

# Module Reference

This document contains the complete argument reference for the terraform-github-repository module.

## Table of Contents

- [Main Resource Configuration](#main-resource-configuration)
- [Extended Resource Configuration](#extended-resource-configuration)
  - [Repository Creation Configuration](#repository-creation-configuration)
  - [Teams Configuration](#teams-configuration)
  - [Collaborator Configuration](#collaborator-configuration)
  - [Branches Configuration](#branches-configuration)
  - [Deploy Keys Configuration](#deploy-keys-configuration)
  - [Branch Protections v3 Configuration](#branch-protections-v3-configuration)
  - [Branch Protections v4 Configuration](#branch-protections-v4-configuration)
  - [Issue Labels Configuration](#issue-labels-configuration)
  - [Projects Configuration](#projects-configuration)
  - [Webhooks Configuration](#webhooks-configuration)
  - [Secrets Configuration](#secrets-configuration)
  - [Autolink References Configuration](#autolink-references-configuration)
  - [App Installations](#app-installations)
- [Module Configuration](#module-configuration)
- [Module Outputs](#module-outputs)

## Main Resource Configuration

### `name`

- **Required**: Yes
- **Type**: `string`

The name of the repository.

---

### `defaults`

- **Type**: `object(defaults)`
- **Default**: `{}`

> **DEPRECATED**: This variable will be removed in future releases. It was needed in times when Terraform Module for each was not available to provide default values for multiple repositories. Please convert your code accordingly to stay compatible with future releases.

---

### `pages`

- **Type**: `object(pages)`
- **Default**: `{}`

A object of settings to configure GitHub Pages in this repository.

| Attribute | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `branch` | `string` | Yes | - | The repository branch used to publish the site's source files. |
| `path` | `string` | No | - | The repository directory from which the site publishes. |
| `cname` | `string` | No | - | The custom domain for the repository. This can only be set after the repository has been created. |

---

### `allow_merge_commit`

- **Type**: `bool`
- **Default**: `true`

Set to `false` to disable merge commits on the repository. If you set this to `false` you have to enable either `allow_squash_merge` or `allow_rebase_merge`.

---

### `allow_squash_merge`

- **Type**: `bool`
- **Default**: `false`

Set to `true` to enable squash merges on the repository.

---

### `allow_rebase_merge`

- **Type**: `bool`
- **Default**: `false`

Set to `true` to enable rebase merges on the repository.

---

### `allow_auto_merge`

- **Type**: `bool`
- **Default**: `false`

Set to `true` to allow [auto-merging](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/incorporating-changes-from-a-pull-request/automatically-merging-a-pull-request) pull requests on the repository. If you enable auto-merge for a pull request, the pull request will merge automatically when all required reviews are met and status checks have passed.

---

### `description`

- **Type**: `string`
- **Default**: `""`

A description of the repository.

---

### `delete_branch_on_merge`

- **Type**: `bool`
- **Default**: `true`

Set to `false` to disable the automatic deletion of head branches after pull requests are merged.

---

### `homepage_url`

- **Type**: `string`
- **Default**: `""`

URL of a page describing the project.

---

### `private`

- **Type**: `bool`

> **DEPRECATED**: Please use `visibility` instead and update your code. This parameter will be removed in a future version.

---

### `visibility`

- **Type**: `string`
- **Default**: `"private"`

Can be `public` or `private`. If your organization is associated with an enterprise account using GitHub Enterprise Cloud or GitHub Enterprise Server 2.20+, `visibility` can also be `internal`. The `visibility` parameter overrides the deprecated `private` parameter.

---

### `has_issues`

- **Type**: `bool`
- **Default**: `false`

Set to `true` to enable the GitHub Issues features on the repository.

---

### `has_projects`

- **Type**: `bool`
- **Default**: `false`

Set to `true` to enable the GitHub Projects features on the repository.

---

### `has_wiki`

- **Type**: `bool`
- **Default**: `false`

Set to `true` to enable the GitHub Wiki features on the repository.

---

### `has_downloads`

- **Type**: `bool`
- **Default**: `false`

Set to `true` to enable the (deprecated) downloads features on the repository.

---

### `is_template`

- **Type**: `bool`
- **Default**: `false`

Set to `true` to tell GitHub that this is a template repository.

---

### `default_branch`

- **Type**: `string`
- **Default**: `""`

The name of the default branch of the repository.

> **NOTE**: The configured default branch must exist in the repository. If the branch doesn't exist yet, or if you are creating a new repository, please add the desired default branch to the `branches` variable, which will cause Terraform to create it for you.

---

### `archived`

- **Type**: `bool`
- **Default**: `false`

Specifies if the repository should be archived.

> **NOTE**: Currently, the API does not support unarchiving.

---

### `topics`

- **Type**: `list(string)`
- **Default**: `[]`

The list of topics of the repository.

---

### `extra_topics`

- **Type**: `list(string)`
- **Default**: `[]`

A list of additional topics of the repository. Those topics will be added to the list of `topics`. This is useful if `default.topics` are used and the list should be extended with more topics.

---

### `vulnerability_alerts`

- **Type**: `bool`

Set to `false` to disable security alerts for vulnerable dependencies. Enabling requires alerts to be enabled on the owner level.

---

### `archive_on_destroy`

- **Type**: `bool`
- **Default**: `true`

Set to `false` to not archive the repository instead of deleting on destroy.

---

## Extended Resource Configuration

### Repository Creation Configuration

The following four arguments can only be set at repository creation and changes will be ignored for repository updates and will not show a diff in plan or apply phase.

#### `auto_init`

- **Type**: `bool`
- **Default**: `true`

Set to `false` to not produce an initial commit in the repository.

---

#### `gitignore_template`

- **Type**: `string`
- **Default**: `""`

Use the name of the template without the extension.

---

#### `license_template`

- **Type**: `string`
- **Default**: `""`

Use the name of the template without the extension.

---

#### `template`

- **Type**: `object(template)`
- **Default**: `{}`

Use a template repository to create this resource.

| Attribute | Type | Required | Description |
|-----------|------|----------|-------------|
| `owner` | `string` | Yes | The GitHub organization or user the template repository is owned by. |
| `repository` | `string` | Yes | The name of the template repository. |

---

### Teams Configuration

You can use non-computed (known at `terraform plan`) team names or slugs (`*_teams` Attributes) or computed (only known in `terraform apply` phase) team IDs (`*_team_ids` Attributes).

> **NOTE**: When using non-computed names/slugs, teams need to exist before running plan. This is due to some Terraform limitation and we will update the module once Terraform removes this limitation.

#### `pull_teams`

- **Type**: `list(string)`
- **Default**: `[]`

Can also be `pull_team_ids`. A list of teams to grant pull (read-only) permission. Recommended for non-code contributors who want to view or discuss your project.

---

#### `triage_teams`

- **Type**: `list(string)`
- **Default**: `[]`

Can also be `triage_team_ids`. A list of teams to grant triage permission. Recommended for contributors who need to proactively manage issues and pull requests without write access.

---

#### `push_teams`

- **Type**: `list(string)`
- **Default**: `[]`

Can also be `push_team_ids`. A list of teams to grant push (read-write) permission. Recommended for contributors who actively push to your project.

---

#### `maintain_teams`

- **Type**: `list(string)`
- **Default**: `[]`

Can also be `maintain_team_ids`. A list of teams to grant maintain permission. Recommended for project managers who need to manage the repository without access to sensitive or destructive actions.

---

#### `admin_teams`

- **Type**: `list(string)`
- **Default**: `[]`

Can also be `admin_team_ids`. A list of teams to grant admin (full) permission. Recommended for people who need full access to the project, including sensitive and destructive actions like managing security or deleting a repository.

---

### Collaborator Configuration

#### `pull_collaborators`

- **Type**: `list(string)`
- **Default**: `[]`

A list of user names to add as collaborators granting them pull (read-only) permission. Recommended for non-code contributors who want to view or discuss your project.

---

#### `triage_collaborators`

- **Type**: `list(string)`
- **Default**: `[]`

A list of user names to add as collaborators granting them triage permission. Recommended for contributors who need to proactively manage issues and pull requests without write access.

---

#### `push_collaborators`

- **Type**: `list(string)`
- **Default**: `[]`

A list of user names to add as collaborators granting them push (read-write) permission. Recommended for contributors who actively push to your project.

---

#### `maintain_collaborators`

- **Type**: `list(string)`
- **Default**: `[]`

A list of user names to add as collaborators granting them maintain permission. Recommended for project managers who need to manage the repository without access to sensitive or destructive actions.

---

#### `admin_collaborators`

- **Type**: `list(string)`
- **Default**: `[]`

A list of user names to add as collaborators granting them admin (full) permission. Recommended for people who need full access to the project, including sensitive and destructive actions like managing security or deleting a repository.

---

### Branches Configuration

#### `branches`

- **Type**: `list(branch)`
- **Default**: `[]`

Create and manage branches within your repository. Additional constraints can be applied to ensure your branch is created from another branch or commit.

| Attribute | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `name` | `string` | Yes | - | The name of the branch to create. |
| `source_branch` | `string` | No | - | The branch name to start from. Uses the configured default branch per default. |
| `source_sha` | `string` | No | - | The commit hash to start from. Defaults to the tip of `source_branch`. If provided, `source_branch` is ignored. |

---

### Deploy Keys Configuration

#### `deploy_keys`

- **Type**: `list(deploy_key)` or `list(string)`
- **Default**: `[]`

Specifies deploy keys and access-level of deploy keys used in this repository. Every `string` in the list will be converted internally into the `object` representation with the `key` argument being set to the `string`.

| Attribute | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `key` | `string` | Yes | - | The SSH public key. |
| `title` | `string` | No | Comment field or `md5(key)` | A title for the key. |
| `read_only` | `bool` | No | `true` | Specifies the level of access for the key. |
| `id` | `string` | No | `md5(key)` | Specifies an ID which is used to prevent resource recreation when the order in the list of deploy keys changes. The ID must be unique between `deploy_keys` and `deploy_keys_computed`. |

---

#### `deploy_keys_computed`

- **Type**: `list(deploy_key)` or `list(string)`
- **Default**: `[]`

Same as `deploy_keys` argument with the following differences: Use this argument if you depend on computed keys that Terraform cannot use in resource `for_each` execution. Downside of this is the recreation of deploy key resources whenever the order in the list changes.

> **NOTE**: Prefer `deploy_keys` whenever possible. This argument does not conflict with `deploy_keys` and should exclusively be used for computed resources.

---

### Branch Protections v3 Configuration

#### `branch_protections_v3`

- **Type**: `list(branch_protection_v3)`
- **Default**: `[]`

This resource allows you to configure v3 branch protection for repositories in your organization. When applied, the branch will be protected from forced pushes and deletion. Additional constraints, such as required status checks or restrictions on users and teams, can also be configured.

| Attribute | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `branch` | `string` | Yes | - | The Git branch to protect. |
| `enforce_admins` | `bool` | No | `false` | Setting this to `true` enforces status checks for repository administrators. |
| `require_conversation_resolution` | `bool` | No | `false` | Setting this to `true` requires all conversations to be resolved. |
| `require_signed_commits` | `bool` | No | `false` | Setting this to `true` requires all commits to be signed with GPG. |
| `required_status_checks` | `object` | No | `{}` | Enforce restrictions for required status checks. See below. |
| `required_pull_request_reviews` | `object` | No | `{}` | Enforce restrictions for pull request reviews. See below. |
| `restrictions` | `object` | No | `{}` | Enforce restrictions for users and teams that may push to the branch. See below. |

##### `required_status_checks` object

| Attribute | Type | Default | Description |
|-----------|------|---------|-------------|
| `strict` | `bool` | `false` | Require branches to be up to date before merging. |
| `contexts` | `list(string)` | `[]` | The list of status checks to require in order to merge into this branch. |

##### `required_pull_request_reviews` object

| Attribute | Type | Default | Description |
|-----------|------|---------|-------------|
| `dismiss_stale_reviews` | `bool` | `true` | Dismiss approved reviews automatically when a new commit is pushed. |
| `dismissal_users` | `list(string)` | `[]` | The list of user logins with dismissal access. |
| `dismissal_teams` | `list(string)` | `[]` | The list of team slugs with dismissal access. Always use slug of the team, not its name. Each team already has to have access to the repository. |
| `require_code_owner_reviews` | `bool` | `false` | Require an approved review in pull requests including files with a designated code owner. |

##### `restrictions` object

| Attribute | Type | Default | Description |
|-----------|------|---------|-------------|
| `users` | `list(string)` | `[]` | The list of user logins with push access. |
| `teams` | `list(string)` | `[]` | The list of team slugs with push access. Always use slug of the team, not its name. Each team already has to have access to the repository. |
| `apps` | `list(string)` | `[]` | The list of app slugs with push access. |

---

### Branch Protections v4 Configuration

#### `branch_protections_v4`

- **Type**: `list(branch_protection_v4)`
- **Default**: `[]`

This resource allows you to configure v4 branch protection for repositories in your organization. Each element in the list is a branch to be protected and the value the corresponding to the desired configuration for the branch.

When applied, the branch will be protected from forced pushes and deletion. Additional constraints, such as required status checks or restrictions on users and teams, can also be configured.

> **NOTE**: May conflict with v3 branch protections if used for the same branch.

| Attribute | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `pattern` | `string` | Yes | - | Identifies the protection rule pattern. |
| `_key` | `string` | No | `pattern` | An alternative key to use in `for_each` resource creation. |
| `allows_deletions` | `bool` | No | `false` | Setting this to `true` to allow the branch to be deleted. |
| `allows_force_pushes` | `bool` | No | `false` | Setting this to `true` to allow force pushes on the branch. |
| `blocks_creations` | `bool` | No | `false` | Setting this to `true` will block creating the branch. |
| `enforce_admins` | `bool` | No | `true` | Keeping this as `true` enforces status checks for repository administrators. |
| `push_restrictions` | `list(string)` | No | `[]` | The list of actor Names/IDs that may push to the branch. Actor names must either begin with a `/` for users or the organization name followed by a `/` for teams. |
| `require_conversation_resolution` | `bool` | No | `false` | Setting this to `true` requires all conversations on code must be resolved before a pull request can be merged. |
| `require_signed_commits` | `bool` | No | `false` | Setting this to `true` requires all commits to be signed with GPG. |
| `required_linear_history` | `bool` | No | `false` | Setting this to `true` enforces a linear commit Git history, which prevents anyone from pushing merge commits to a branch. |
| `required_pull_request_reviews` | `object` | No | - | Enforce restrictions for pull request reviews. See below. |
| `required_status_checks` | `object` | No | - | Enforce restrictions for required status checks. See below. |

##### `required_pull_request_reviews` object (v4)

| Attribute | Type | Default | Description |
|-----------|------|---------|-------------|
| `dismiss_stale_reviews` | `bool` | `true` | Dismiss approved reviews automatically when a new commit is pushed. |
| `restrict_dismissals` | `bool` | - | Restrict pull request review dismissals. |
| `dismissal_restrictions` | `list(string)` | `[]` | The list of actor Names/IDs with dismissal access. If not empty, `restrict_dismissals` is ignored. Actor names must either begin with a `/` for users or the organization name followed by a `/` for teams. |
| `pull_request_bypassers` | `list(string)` | `[]` | The list of actor Names/IDs that are allowed to bypass pull request requirements. Actor names must either begin with a `/` for users or the organization name followed by a `/` for teams. |
| `require_code_owner_reviews` | `bool` | `true` | Require an approved review in pull requests including files with a designated code owner. |
| `required_approving_review_count` | `number` | `0` | Require x number of approvals to satisfy branch protection requirements. If this is specified it must be a number between 0-6. |

##### `required_status_checks` object (v4)

| Attribute | Type | Default | Description |
|-----------|------|---------|-------------|
| `strict` | `bool` | `false` | Require branches to be up to date before merging. |
| `contexts` | `list(string)` | `[]` | The list of status checks to require in order to merge into this branch. |

---

### Issue Labels Configuration

#### `issue_labels`

- **Type**: `list(issue_label)`
- **Default**: `[]`

This resource allows you to create and manage issue labels within your GitHub organization. Issue labels are keyed off of their "name", so pre-existing issue labels result in a 422 HTTP error if they exist outside of Terraform. Normally this would not be an issue, except new repositories are created with a "default" set of labels, and those labels easily conflict with custom ones. This resource will first check if the label exists, and then issue an update, otherwise it will create.

| Attribute | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `name` | `string` | Yes | - | The name of the label. |
| `color` | `string` | Yes | - | A 6 character hex code, without the leading `#`, identifying the color of the label. |
| `description` | `string` | No | `""` | A short description of the label. |
| `id` | `string` | No | `name` | Specifies an ID which is used to prevent resource recreation when the order in the list of issue labels changes. |

---

#### `issue_labels_merge_with_github_labels`

- **Type**: `bool`

Specify if GitHub default labels will be handled by Terraform. This should be decided on upon creation of the repository. If you later decide to disable this feature, GitHub default labels will be destroyed if not replaced by labels set in `issue_labels` argument.

---

#### `issue_labels_create`

- **Type**: `bool`

Specify whether you want to force or suppress the creation of issues labels. Default is `true` if `has_issues` is `true` or `issue_labels` is non-empty.

---

### Projects Configuration

#### `projects`

- **Type**: `list(project)`
- **Default**: `[]`

This resource allows you to create and manage projects for GitHub repository.

| Attribute | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `name` | `string` | Yes | - | The name of the project. |
| `body` | `string` | No | `""` | The body of the project. |
| `id` | `string` | No | `name` | Specifies an ID which is used to prevent resource recreation when the order in the list of projects changes. |

---

### Webhooks Configuration

#### `webhooks`

- **Type**: `list(webhook)`
- **Default**: `[]`

This resource allows you to create and manage webhooks for repositories in your organization. When applied, a webhook will be created which specifies a URL to receive events and which events to receive. Additional constraints, such as SSL verification, pre-shared secret and content type can also be configured.

| Attribute | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `events` | `list(string)` | Yes | - | A list of events which should trigger the webhook. [See a list of available events.](https://developer.github.com/v3/activity/events/types/) |
| `url` | `string` | Yes | - | The URL to which the payloads will be delivered. |
| `active` | `bool` | No | `true` | Indicate if the webhook should receive events. |
| `content_type` | `string` | No | `"form"` | The media type used to serialize the payloads. Supported values include `json` and `form`. |
| `secret` | `string` | No | - | If provided, the `secret` will be used as the `key` to generate the HMAC hex digest value in the [X-Hub-Signature](https://developer.github.com/webhooks/#delivery-headers) header. |
| `insecure_ssl` | `bool` | No | `false` | Determines whether the SSL certificate of the host for `url` will be verified when delivering payloads. **We strongly recommend not setting this to `true` as you are subject to man-in-the-middle and other attacks.** |

---

### Secrets Configuration

#### `plaintext_secrets`

- **Type**: `map(string)`
- **Default**: `{}`

This map allows you to create and manage secrets for repositories in your organization. Each element in the map is considered a secret to be managed, being the key map the secret name and the value the corresponding secret in plain text.

When applied, a secret with the given key and value will be created in the repositories. The value of the secrets must be given in plain text, GitHub provider is in charge of encrypting it.

> **Attention**: You should treat state as sensitive always. It is also advised that you do not store plaintext values in your code but rather populate the encrypted_value using fields from a resource, data source or variable as, while encrypted in state, these will be easily accessible in your code.

```hcl
plaintext_secrets = {
  SECRET_NAME_1 = "plaintext_secret_value_1"
  SECRET_NAME_2 = "plaintext_secret_value_2"
}
```

---

#### `encrypted_secrets`

- **Type**: `map(string)`
- **Default**: `{}`

This map allows you to create and manage encrypted secrets for repositories in your organization. Each element in the map is considered a secret to be managed, being the key map the secret name and the value the corresponding encrypted value of the secret using the GitHub public key in Base64 format.

When applied, a secret with the given key and value will be created in the repositories.

```hcl
encrypted_secrets = {
  SECRET_NAME_1 = "c2VjcmV0X3ZhbHVlXzE="
  SECRET_NAME_2 = "c2VjcmV0X3ZhbHVlXzI="
}
```

---

### Autolink References Configuration

#### `autolink_references`

- **Type**: `list(autolink_reference)`
- **Default**: `[]`

This resource allows you to create and manage autolink references for GitHub repository.

| Attribute | Type | Required | Description |
|-----------|------|----------|-------------|
| `key_prefix` | `string` | Yes | This prefix appended by a number will generate a link any time it is found in an issue, pull request, or commit. |
| `target_url_template` | `string` | Yes | The template of the target URL used for the links; must be a valid URL and contain `<num>` for the reference number. |

---

### App Installations

#### `app_installations`

- **Type**: `set(string)`
- **Default**: `{}`

A set of GitHub App IDs to be installed in this repository.

```hcl
app_installations = ["05405144", "12556423"]
```

---

## Module Configuration

### `module_depends_on`

- **Type**: `list(dependency)`
- **Default**: `[]`

Due to the fact that Terraform does not offer `depends_on` on modules, we might hit race conditions when dealing with team names instead of IDs. So when using the feature of adding teams by slug/name to the repository when creating it, make sure to add all teams to this list as indirect dependencies.

---

## Module Outputs

The following attributes are exported by the module:

| Output | Type | Description |
|--------|------|-------------|
| `repository` | `object(repository)` | All repository attributes as returned by the `github_repository` resource. |
| `branches` | `object(branches)` | All branch attributes as returned by the `github_branch` resource. |
| `full_name` | `string` | A string of the form "orgname/reponame". |
| `html_url` | `string` | URL to the repository on the web. |
| `ssh_clone_url` | `string` | URL that can be provided to git clone to clone the repository via SSH. |
| `http_clone_url` | `string` | URL that can be provided to git clone to clone the repository via HTTPS. |
| `git_clone_url` | `string` | URL that can be provided to git clone to clone the repository anonymously via the git protocol. |
| `collaborators` | `object(collaborators)` | A map of Collaborator objects keyed by the `name` of the collaborator as returned by the `github_repository_collaborator` resource. |
| `deploy_keys` | `object(deploy_keys)` | A merged map of deploy key objects for the keys originally passed via `deploy_keys` and `deploy_keys_computed` as returned by the `github_repository_deploy_key` resource keyed by the input `id` of the key. |
| `projects` | `object(project)` | A map of Project objects keyed by the `id` of the project as returned by the `github_repository_project` resource. |
| `issue_labels` | `object(issue_label)` | A map of issue labels keyed by label input id or name. |
| `webhooks` | `object(webhook)` | All attributes and arguments as returned by the `github_repository_webhook` resource. |
| `secrets` | `object(secret)` | List of secrets available. |
| `app_installations` | `set(number)` | A map of deploy app installations keyed by installation id. |

---

## External Documentation

- [github_repository](https://registry.terraform.io/providers/integrations/github/latest/docs/resources/repository)
- [github_branch](https://registry.terraform.io/providers/integrations/github/latest/docs/resources/branch)
- [github_repository_collaborator](https://registry.terraform.io/providers/integrations/github/latest/docs/resources/repository_collaborator)
- [github_repository_deploy_key](https://registry.terraform.io/providers/integrations/github/latest/docs/resources/repository_deploy_key)
- [github_repository_project](https://registry.terraform.io/providers/integrations/github/latest/docs/resources/repository_project)
- [github_repository_autolink_reference](https://registry.terraform.io/providers/integrations/github/latest/docs/resources/repository_autolink_reference)
- [github_branch_protection](https://registry.terraform.io/providers/integrations/github/latest/docs/resources/branch_protection)
- [github_branch_protection_v3](https://registry.terraform.io/providers/integrations/github/latest/docs/resources/branch_protection_v3)
