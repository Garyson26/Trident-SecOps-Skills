---
name: gemini-tool-orchestrator
description: Natural-language orchestration of security tools through Gemini. Translates intent ("scan this scope for exposed admin panels") into safe, parameterized tool pipelines using nmap, masscan, naabu, httpx, nuclei, ffuf, gobuster, subfinder, amass, dnsx, katana, gau, semgrep, trivy, checkov, gitleaks, syft, grype, and custom scripts. Use when the user wants Gemini to drive a chain of CLI tools end to end with guardrails.
---

# Gemini Tool Orchestrator

## Authorization Boundary

- Require explicit scope: target list, exclusions, rate, time window, and written authorization reference before any active step.
- Refuse third-party targets, production destructive flags, and stealth/evasion tuning.
- Prefer read-only, low-rate, lab-confirmed pipelines first.

## Orchestration Pattern

1. Translate user intent into a goal: discovery, surface mapping, vulnerability triage, secrets review, SBOM, IaC review, or evidence collection.
2. Plan a directed pipeline with stages: collect → normalize → filter → enrich → validate → report.
3. For each stage, output: tool, exact command, why this flag, expected artifact path, runtime cap, and failure handling.
4. Run idempotently: write to `./runs/<utc>-<goal>/`, dedupe inputs, and emit JSON Lines so later stages can stream-process.
5. Gate active stages (nuclei, ffuf, fuzzers) on a `--confirm-scope` flag the user must pass.
