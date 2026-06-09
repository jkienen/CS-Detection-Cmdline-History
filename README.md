# Crowdstrike Detection Cmdline History

> A CrowdStrike Falcon SOAR workflow that automatically sends the command-line execution history of a compromised host via email when a **High/Critical severity** detection or a **Falcon OverWatch** detection occurs.

---

## Overview

When a **High/Critical severity** detection or a **Falcon OverWatch** detection is triggered, this workflow:

1. Queries the last **3 hours** of process execution history for the affected sensor
2. Builds a full **process lineage** chain (`GrandParent > Parent > Child`)
3. Exports the results as a **CSV file**
4. Sends the CSV as an **email attachment** to the configured recipients

This gives analysts immediate context about what was running on the host at the time of detection — without needing to manually query LogScale.

---

## Trigger Conditions

The workflow fires on an `Investigatable/EPP` detection signal when **at least one** of the following conditions is met:

| Condition | Value |
|---|---|
| Severity | **High** or **Critical** (> Medium) |
| Tactic | **Falcon OverWatch** |

---

## Workflow Steps

![Workflow Diagram](Files/workflow-diagram.png)

| Step | Type | Description |
|---|---|---|
| Detection > EPP Detection | Trigger | Fires on every EPP detection event |
| Condition | Check | Severity > Medium **OR** Tactic = Falcon OverWatch |
| Workflow-specific event query | Action | Queries last 3h of command-line history for the sensor |
| Send email | Action | Sends the CSV attachment to configured recipients |

---

## CSV Output Fields

| Field | Description |
|---|---|
| `HumanTime` | Process start time (formatted, America/Sao_Paulo) |
| `UserName` | User who executed the process |
| `ComputerName` | Hostname |
| `CommandLine` | Full command line |
| `ProcessLineage` | `GrandParent > Parent > Child` chain |

---

## Configuration

Before importing this workflow, update the following:

### Email Recipients

In `Files/Send-cmdline-history-email-on-detection.yaml`, locate the `SendEmail` action and add the recipient addresses:

```yaml
to:
  - analyst@yourcompany.com
  - soc-team@yourcompany.com
```

### Timeframe (optional)

The query defaults to the **last 3 hours**. To adjust, change the `logscale_search_start_time` value:

```yaml
logscale_search_start_time: 3 hr  # change as needed
```

---

## Requirements

- CrowdStrike Falcon with **SOAR / Fusion Workflows** enabled
- LogScale (Falcon Long Term Repository) with `ProcessRollup2` events ingested

---

## Import Instructions

1. In the Falcon console, navigate to **Fusion SOAR → Workflows**
2. Click **Import**
3. Upload `Files/Send-cmdline-history-email-on-detection.yaml`
4. Configure the email recipients
5. Activate the workflow

---

## Files

| File | Description |
|---|---|
| `Files/Send-cmdline-history-email-on-detection.yaml` | Workflow definition (SOAR export) |
| `Files/workflow-diagram.png` | Workflow diagram screenshot |

---

## License

This workflow template is provided as-is for reference and reuse. Adapt it freely to your environment.