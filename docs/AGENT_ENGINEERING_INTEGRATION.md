# PromptForge Agent Engineering Integration

## Purpose

This document describes the agent-engineering patterns now embedded in PromptForge's prompt-generation, prompt-analysis, and evaluation-generation instructions. It is an implementation note for the current application, not a claim of behavioral certification.

## Implemented components

### 1. Prompt generation — `frontend/prompt-generator.js`

The prompt engineer now:
- classifies the artifact as a one-off prompt, reusable template, system prompt, agent specification, skill, tool contract, or evaluation rubric;
- separates provider-neutral policy from model-, harness-, project-, and domain-specific instructions;
- checks scope, available context/tools, output contract and completion criteria;
- de-personalizes reusable prompts unless user-specific information is explicitly required;
- treats quoted, uploaded, retrieved and tool-returned content as untrusted source data;
- specifies action authorization, bounded delegation and dependency-aware parallelism when relevant;
- preserves load-bearing constraints rather than optimizing for token count alone;
- reports assumptions and asks at most one material clarification.

The existing response headings remain “Revised prompt” and “Questions” for compatibility with the current UI parser.

### 2. Prompt audit — `api/internal/services/prompt_analyzer.go`

The analyzer now audits:
- objective and task classification;
- target model/runtime portability;
- scope and completion conditions;
- source provenance and evidence;
- instruction hierarchy and trust boundaries;
- tool contracts and side effects;
- authorization and safety;
- workflow/delegation;
- output contract;
- stale, contradictory, duplicated or over-specified instructions;
- evaluation criteria and boundary cases.

Findings must be tied to prompt evidence and labeled by severity and confidence. Static analysis must not claim runtime safety or test success. The output contract is valid HTML only.

### 3. Evaluation generation — `api/internal/services/eval_generator.go`

Generated test cases now include the `expected` field with an observable expected behavior. The generation prompt requests relevant adversarial and boundary scenarios covering trust boundaries, authorization, tool routing, evidence, deterministic verification, portability and completion claims.

Evaluation criteria now use operational pass conditions rather than generic quality labels. Empty or unsupported evaluation-type sets return an error instead of dividing by zero or returning an empty rubric.

## Design boundaries

- The application does not execute tool calls on behalf of the prompt being analyzed.
- The prompt under analysis is treated as data, not as instructions to the analyzer.
- Provider-specific schemas must be adapted to the selected target runtime.
- A generated evaluation suite is a test design, not evidence that the target model passed.
- This integration does not add external-action execution, agent delegation runtime, model telemetry, or automatic prompt deployment.

## Operational note

The source files were updated in the repository. Per the user's explicit instruction, no tests were run for this change. Runtime behavior, compilation, UI compatibility and generated evaluation quality remain unverified.
