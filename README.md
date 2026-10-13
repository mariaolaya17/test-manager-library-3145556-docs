# test-manager-library-3145556-docs

> **Technical Challenge · ADSO Record 3145556**
> Delivery: **Tuesday, October 13, 2026, 5:00 PM** (17:00, Colombia time).

This repository contains the **spec** for *Manager Library*. It is the only one with content: the other five start empty.

## Instructions

Each learner must **create a fork** of the six project repositories and **solve the project with the given spec**.

1. Fork, to your GitHub account, each repository from the table at the end.
2. Read the spec in [`test-manager-library-3145556-docs`](https://github.com/code-sena/test-manager-library-3145556-docs).
   It is delivered in two versions: `spec-python/` and `spec-.net/`.
3. Develop in the forks.

## The challenge is developed with React and PHP (Laravel)

The spec is written for Python and for .NET, but the challenge is **not** done in those languages:

| Layer | Challenge Technology |
|---|---|
| Frontend | React |
| Backend | PHP with Laravel |

What the spec defines about the business — stories, acceptance criteria, rules, API contract, data model — is
respected. What it defines about technology is translated to React and Laravel.

## The challenge is not about writing code

The main purpose is to assess **SDD performance** (*Spec-Driven Development*): how a specification is read,
interpreted, and applied to carry it to a different stack. The code is the means, not the end.

## Delivery

Only what is **pushed to the forks before 5:00 PM** (17:00) counts. Anything uploaded after is not reviewed.
There is no extension: submit partial progress with small commits during the challenge.

## What this repository contains

| Folder | Content |
|---|---|
| [`spec-python/`](spec-python/) | The Manager Library spec written for a Python backend |
| [`spec-.net/`](spec-.net/) | The same system specified for a .NET backend |
| [`spec-laravel/`](spec-laravel/) | Translation of the spec to the challenge stack (Laravel + React) |

The three versions describe the **same product** (same stories, business rules, and endpoints); they differ in
technology decisions. None of the original two is the challenge stack.

## How to read the spec

In either `spec-python/` or `spec-.net/`, in this order:

1. `constitution.md` — non-negotiable principles.
2. `spec.md` — what the system must do: actors, stories, acceptance criteria, business rules.
3. `plan.md`, `architecture.md` — how it is built.
4. `data-model.md` and `api-contract.md` — the data model and the API contract.
5. `tasks.md` — what must be done and with what evidence it is considered done.
6. `adr/` — architecture decisions, with their alternatives and consequences.

Then read [`spec-laravel/`](spec-laravel/) for the translation to the challenge stack.

## The six repositories

| Repository | What goes there |
|---|---|
| [`test-manager-library-3145556-docs`](https://github.com/code-sena/test-manager-library-3145556-docs) | The spec: `spec-python/`, `spec-.net/`, and `spec-laravel/` |
| [`test-manager-library-3145556-api`](https://github.com/code-sena/test-manager-library-3145556-api) | Backend in PHP (Laravel) |
| [`test-manager-library-3145556-app`](https://github.com/code-sena/test-manager-library-3145556-app) | Frontend in React |
| [`test-manager-library-3145556-page`](https://github.com/code-sena/test-manager-library-3145556-page) | Public static presentation site |
| [`test-manager-library-3145556-infra`](https://github.com/code-sena/test-manager-library-3145556-infra) | Containers, network, volumes, and empty database engine |
| [`test-manager-library-3145556-tool`](https://github.com/code-sena/test-manager-library-3145556-tool) | Utilities: demo data seeder |
