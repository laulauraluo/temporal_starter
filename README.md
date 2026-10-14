<p align="center">
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="assets/banner-dark.svg">
    <source media="(prefers-color-scheme: light)" srcset="assets/banner-light.svg">
    <img src="assets/banner-dark.svg" width="100%" alt="temporal_starter: a Go workflow that survives crashes and retries on its own">
  </picture>
</p>

<p align="center">
  <a href="https://github.com/laulauraluo/temporal_starter/generate"><img src="https://img.shields.io/badge/Use_this_template-2ea44f?style=for-the-badge&logo=github&logoColor=white" alt="Use this template"></a>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/Go-1.22+-00ADD8?logo=go&logoColor=white" alt="Go 1.22+">
  <img src="https://img.shields.io/badge/Temporal_SDK-1.30-141414?logo=temporal&logoColor=white" alt="Temporal Go SDK 1.30">
  <img src="https://img.shields.io/badge/License-MIT-blue" alt="MIT License">
</p>

<p align="center"><sub>A running Temporal skeleton in a few minutes: one workflow, one activity, a worker, a starter, and a unit test.</sub></p>

## ⚡ Quick start

You need Go 1.22+ and the Temporal CLI (`brew install temporal`). Use three terminals:

```bash
# 1. local Temporal server; Web UI at http://localhost:8233
temporal server start-dev
```

```bash
# 2. worker
cd hello-world-temporal
go run worker/main.go
```

```bash
# 3. start one workflow and print its result
cd hello-world-temporal
go run start/main.go
```

Terminal 3 prints the workflow ID, the run ID, and `Hello World!`. The run also shows up in the Web UI.

## 🧭 How it works

```mermaid
sequenceDiagram
    participant S as start/main.go
    participant T as Temporal server
    participant W as worker/main.go
    S->>T: ExecuteWorkflow(GreetingWorkflow, "World")
    W->>T: poll GREETING_TASK_QUEUE
    T->>W: run GreetingWorkflow
    W->>W: ComposeGreeting("World"), 5 s timeout
    W-->>T: "Hello World!"
    T-->>S: result
```

The server records every step. If the worker dies mid-run, the next worker that polls the queue picks the workflow up where it stopped, and a failed activity is retried with Temporal's default retry policy.

## ✅ Tests

```bash
cd hello-world-temporal
go test ./...
```

`workflow_test.go` runs the workflow in Temporal's test environment with the activity mocked, so it needs no server.

## 🛠️ Make it yours

| To change | Edit |
|---|---|
| What the work does | `activity.go` (add activities next to `ComposeGreeting`) |
| The order of steps, timeouts, retries | `greetingWorkflow.go` |
| Which workflows and activities run | register them in `worker/main.go` |
| Task queue name | `shared.go` |
| Workflow ID and input | `start/main.go` |
| Module path | `go.mod`, then the imports in `worker/` and `start/` |

## 📁 Layout

```
hello-world-temporal/
├── greetingWorkflow.go   workflow
├── activity.go           activity
├── shared.go             task queue name
├── workflow_test.go      unit test
├── worker/main.go        worker process
└── start/main.go         starts a workflow
```

## 🙏 Credits

Based on Temporal's official [hello-world-project-template-go](https://github.com/temporalio/hello-world-project-template-go), released under the MIT license (see `LICENSE`).
