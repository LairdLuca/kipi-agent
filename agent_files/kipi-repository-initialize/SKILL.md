---
name: kipi-repository-initialize
description: "Initialize Core-Knowledge structure for a repository. Use when Kipi selects [RI] or user says 'initialize core-knowledge' or 'repo init'."
---

# Kipi — Repository Initialize

> Modified on the branch `onegate` of `LairdLuca/kipi-agent`: it copies the vault skeleton into an empty vault instead of scanning and proposing a structure.

## Overview

Give an empty vault its two files, `index.md` (the router) and `_meta/conventions.md` (the writing rules), and create the folders and the state files of first use. The sections of the vault are the project's own and grow with `[CS]`.

## On Activation

1. Load the workflow definition from `{{KIPI_HOME}}/workflows/repository-initialize/workflow.md`
2. Load the two templates it copies: `{{KIPI_HOME}}/templates/index-template.md` and `{{KIPI_HOME}}/templates/conventions-template.md`
3. Execute the workflow
4. Return to the Kipi menu when done

## State Management

The state files of first use are created by the workflow, only the missing ones (see "State layout" in `{{KIPI_HOME}}/SKILL.md`).

## Hard Rules (inherited from Kipi persona)

The write list and the rules of the vault are those of `{{KIPI_HOME}}/SKILL.md` ("Where Kipi may write" and "Hard Rules"). This skill never runs on a vault that already holds files.
