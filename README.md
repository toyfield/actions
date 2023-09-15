# actions

## 基础概念

根据[官方文档](https://docs.github.com/en/actions/learn-github-actions/understanding-github-actions?learn=getting_started&learnProduct=actions)，GitHub Action 主要分以下几个部分：

1. workflow：一个 workflow 代表一次完整的构建流程，包含一个或多个 jobs。
1. jobs：一个 job 是 workflow 中的一系列 steps 的集合，每一个 step 是一个 actions 或者一个 shell 脚本。jobs 可以并行执行，也可以依赖执行。
1. actions：一个 action 代表 step 中的实际操作内容，可以是一个 shell 脚本、一个 Docker 容器，也可以是一个 reusable workflow，属于最小的执行单元。在 workflow syntax 属于 jobs.<job_id>.steps[*] 后的部分。
1. events：触发 action 的事件，例如 push、pull request 等。
1. runners：执行 workflow 的服务，可以是 GitHub 托管的服务，也可以是自己搭建的服务。

其中 workflow 是最顶层的概念，一个 workflow 可以包含多个 jobs，一个 job 可以包含多个 steps，一个 step 可以包含多个 actions。

## 复用逻辑

### 复用 workflow：

[可复用 workflow](https://docs.github.com/en/action·s/using-workflows/reusing-workflows)，可以在编写的job 中直接调用。

被调用的 workflow 称为 called workflow，调用 called workflow 的 workflow 成为 caller workflow。

* 创建可复用 workflow

reusable workflow 只需要在 workflow 的`on`添加`workflow_call`配置，例如：

```yaml
on:
  workflow_call:
```

* 在 caller workflow 中调用 reusable workflow

在 job 中 uses 中配置路径。

`{owner}/{repo}/.github/workflows/{filename}@{ref}` for reusable workflows in public and private repositories.
`./.github/workflows/{filename}` for reusable workflows in the same repository.

* [在 caller workflow 中传递参数](https://docs.github.com/en/actions/using-workflows/reusing-workflows#passing-inputs-and-secrets-to-a-reusable-workflow)

called workflow 本身可以在`workflow_call`中，配置 inputs 和 secrets 字段，来接受传递参数和敏感数据。caller workflow 只需要像使用其他 action 一样，使用`with`和`secrets`字段来传递参数和敏感数据。

* [secrets inherit](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#jobsjob_idsecretsinherit)

caller workflow 通过配置 secrets inherit 让 called workflow 继承所有 caller workflow 的 secrets。

### 复用/使用 action

`{owner}/{repo}/{path}@{ref}` path 到 action.yaml 的文件夹。

action 作为一个执行的最小单元，可以通过[自定义 action](https://docs.github.com/en/actions/creating-actions)来给不同的 workflow 进行复用，action 可以作为 composite action，将其他多个 action 合并起来，也可以创建一个完全独立的 action。