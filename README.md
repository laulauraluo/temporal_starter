# temporal_starter

A minimal [Temporal](https://temporal.io) app in Go to start a backend from: one workflow, one activity, a worker, a starter, and a unit test. Use it when your project needs jobs that survive crashes and retry on their own, and you want a running skeleton in a few minutes.

Based on Temporal's official [hello-world-project-template-go](https://github.com/temporalio/hello-world-project-template-go) (MIT, see `LICENSE`).

## How it works

```
start/main.go ──starts──▶ GreetingWorkflow ──on task queue GREETING_TASK_QUEUE──▶ worker/main.go
                                │
                                └─ runs activity ComposeGreeting("World") ─▶ "Hello World!"
```

The workflow gives the activity a 5-second timeout. If the worker dies mid-run, Temporal resumes the workflow on the next worker that polls the queue.

## Quick start

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

## Tests

```bash
cd hello-world-temporal
go test ./...
```

`workflow_test.go` runs the workflow in Temporal's test environment with the activity mocked, so it needs no server.

## Make it yours

| To change | Edit |
|---|---|
| What the work does | `activity.go` (add activities next to `ComposeGreeting`) |
| The order of steps, timeouts, retries | `greetingWorkflow.go` |
| Which workflows and activities run | register them in `worker/main.go` |
| Task queue name | `shared.go` |
| Workflow ID and input | `start/main.go` |
| Module path | `go.mod`, then the imports in `worker/` and `start/` |

## Layout

```
hello-world-temporal/
├── greetingWorkflow.go   workflow
├── activity.go           activity
├── shared.go             task queue name
├── workflow_test.go      unit test
├── worker/main.go        worker process
└── start/main.go         starts a workflow
```
