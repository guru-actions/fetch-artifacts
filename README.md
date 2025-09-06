# fetch-artifacts

Fetches artifact information from the CloudBees Component Repository based on label filters.  
Supports fetching the latest artifact (default) or listing all matching artifacts.

## What It Does

This CloudBees custom action queries the CloudBees Component Repository API for artifacts that match a given set of labels. It returns:

- The latest matching artifact ID, version, and timestamp (by default)
- Or, all matching artifacts when `mode: all` is specified

## Inputs

| Name           | Required | Description                                                                  |
|----------------|----------|------------------------------------------------------------------------------|
| `cb_api_url`   | Yes      | CloudBees API URL (e.g. `https://api.cloudbees.io`)                          |
| `component_id` | Yes      | UUID of the Component to query                                               |
| `cb_pat`       | Yes      | CloudBees Personal Access Token (use `${{ secrets.MY_SECRET }}`)             |
| `labels`       | Yes      | Comma-separated list of labels to match (e.g. `rel=squid-ui,ns=squid-prod`)  |
| `mode`         | No       | Fetch mode: `latest` (default) or `all`                                      |

## Outputs

| Name         | Description                              |
|--------------|------------------------------------------|
| `artifact_id`| The ID of the matched artifact           |
| `version`    | The version of the matched artifact      |
| `timestamp`  | The published timestamp of the artifact  |

## Example Usage

```yaml
apiVersion: automation.cloudbees.io/v1alpha1
kind: workflow
name: Fetch Artifact Example

on:
  push:
    branches:
      - '**'

jobs:
  get-latest-artifact:
    steps:
      - name: Fetch latest artifact for squid-ui in squid-prod
        uses: guru-actions/fetch-artifacts@0.6
        id: fetch
        with:
          cb_api_url: https://api.cloudbees.io
          component_id: 0459d9f1-4f37-4db1-bfb4-e2e1731a0975
          cb_pat: ${{ secrets.CB_PAT }}
          labels: rel=squid-ui,ns=squid-prod
          mode: latest

      - name: Show fetched artifact details
        run: |
          echo "Artifact ID: ${{ steps.fetch.outputs.artifact_id }}"
          echo "Version:     ${{ steps.fetch.outputs.version }}"
          echo "Timestamp:   ${{ steps.fetch.outputs.timestamp }}"

🔍 Filtering artifacts by labels: rel=squid-ui,ns=squid-prod (mode=latest)
Using jq filter: (.labels | index("rel=squid-ui")) and (.labels | index("ns=squid-prod"))
Found latest artifact:
   ID: 70fda432-0f45-4d1d-9b29-050af96792ff
   Version: 2025.09.06.1-cd764ab49a23
   Timestamp: 2025-09-06T05:30:06.435188112Z

