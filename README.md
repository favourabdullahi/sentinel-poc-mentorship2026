# Microsoft Sentinel PoC — Privilege Escalation Detection

**Proof of Concept Documentation**
Prepared by Favour Abdullahi — Testing Engineer, Mentorship2026 | May 2026

## Overview

This project documents a Microsoft Sentinel Proof of Concept (PoC) that simulates
a real-world privilege escalation attack and validates that Sentinel correctly detects,
creates incidents, and triggers automated response playbooks.

## What Was Covered

- Simulating a privilege escalation attack (Contributor role assigned at subscription scope)
- Writing and tuning KQL detection rules against Azure Activity logs
- Configuring Microsoft Sentinel Analytics rules to auto-fire incidents
- Building a Logic App playbook to automate incident response
- Validating the full pipeline: attack to detection to incident to automated comment

## Environment & Infrastructure

| Component | Value |
|---|---|
| SIEM Platform | Microsoft Sentinel |
| Log Analytics Workspace | LAWSentinelCloudScale |
| Resource Group | RG-Sentinel-PoC |
| Subscription | Azure Subscription 1 |
| Playbook Name | Sentinel-PrivEsc-Notify-Playbook |
| Detection Rule | Role Assignment Detection Test |
| Detection Severity | High |
| Region | East US 2 |

## Attack Simulation

The attack simulated assigning the Azure built-in Contributor role to a test user
(TestPrivEscUser) at subscription scope using Azure PowerShell in Cloud Shell.

Audit Log Confirmation KQL:

    AzureActivity
    | where OperationNameValue == "MICROSOFT.AUTHORIZATION/ROLEASSIGNMENTS/WRITE"
    | where ActivityStatusValue == "Success"

## Detection Rule

| Setting | Value |
|---|---|
| Rule Name | Role Assignment Detection Test |
| Data Source | AzureActivity |
| Severity | High |
| Query Frequency | Every 5 minutes |
| Lookup Period | Last 1 hour |

Detection KQL Query:

    AzureActivity
    | where TimeGenerated > ago(1h)
    | where OperationNameValue == "MICROSOFT.AUTHORIZATION/ROLEASSIGNMENTS/WRITE"
    | where ActivityStatusValue == "Success"
    | project TimeGenerated, Caller, OperationNameValue, ActivityStatusValue, ResourceGroup

## Playbook Automation

The Logic App (Sentinel-PrivEsc-Notify-Playbook) triggers automatically on new
Sentinel incidents and adds a comment to the incident to log that automated
processing occurred.

## Completion Status

| Phase / Step | Description | Status |
|---|---|---|
| Phase 1 | Initial Access & MFA Setup | Complete |
| Phase 2-3 | Environment Setup & Modules | Complete |
| Phase 4 | Infrastructure Verification | Complete |
| Phase 5 | Test User Created | Complete |
| Step 33b | Role Assignment at Subscription Scope | Complete |
| Step 33c | Audit Log Confirmed in KQL | Complete |
| Step 34 | Detection Rule Created & Enabled | Complete |
| Step 35 | Incident Created in Sentinel | Complete |
| Step 36 | Playbook Fixed & Succeeded | Complete |
| Step 37 | Cleanup (Remove user & role) | Pending |