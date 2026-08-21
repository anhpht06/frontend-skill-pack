---
name: 08-jira-task-logging
description: Guidelines and strict formatting rules for planning and generating daily Jira worklogs.
---

# Jira Daily Task Logging

This document outlines how an AI Agent should assist Developers in breaking down, planning (pre-coding), and writing daily Jira worklogs accurately, following strict formatting and time-tracking logic.

## 1. Mandatory Task Log Structure

Every single worklog must strictly adhere to the following template:

### [PROJECT-KEY] | [FE] <Brief task description>
- **title**: [PROJECT-KEY] | [FE] <Brief task description>
- **Duration**: <0.5h | 1h | 2h | 3h>
- **Date**: dd/mm/yyyy (Day of week)
- **description**:
  - Repo: <Jira ticket code (e.g., PROJ-123)>
  - <List specific action items: what will be/was done, in which file/package, inputs/outputs>
  - Test: (Mandatory for feature tasks)
    - <2-4 bullet points: specify test type (unit/integration/manual) — what is the input, what is the expected outcome>

*Note: Replace `[PROJECT-KEY]` with the actual project key. Only prepend `[FE]` to the title if the task relates to Frontend development.*

## 2. Time Tracking Rules

When the AI Agent breaks down a day's work, it **MUST** apply the following rules:

1. **Chunking:** The duration of each sub-task is restricted to **0.5h**, **1h**, **2h**, or a maximum of **3h**.
2. **Total Daily Hours:** It is not strictly forced to be 8-9h. A day's total duration can be **7h, 8h, or 9h** depending on the Developer's actual workload for that day.
3. **No Cross-Day Splitting:** Never split a single task log across two different days. If a large feature takes 5 hours, it must be divided into two distinct sub-tasks with different titles/descriptions (e.g., a 3h task for Day 1, and a 2h follow-up task for Day 2).

## 3. Instructions for AI Agents

The AI Agent must be versatile to handle two scenarios (Pre-coding planning vs Post-coding logging):

### Scenario 1: Planning Jira Logs BEFORE coding
When the Dev requests *"I'm about to build Feature X, please break down the tasks and write Jira logs for me"*:
1. Analyze the business requirements of the feature.
2. Predict the UI components, services, and APIs that need to be built.
3. Break them down into `0.5h`, `1h`, `2h`, `3h` chunks (totaling 7h - 9h/day) to serve as a Jira logging roadmap.

### Scenario 2: Writing Jira Logs AFTER coding
When the Dev requests *"Generate my Jira log for today based on the code I just wrote"*:
1. Scan the changed files (git diff) or process the Developer's rough summary.
2. Group logical changes together.
3. Slice these groups into time chunks matching the actual effort (0.5h to 3h/task).
4. Output the result in Markdown strictly using the Template defined in Section 1.
