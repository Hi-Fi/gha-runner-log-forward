# gha-runner-log-forward
Example on how to allow run logs to be forwarded to external system/long time storage

## Background

GitHub Enterprise allow to forward [Audit](https://docs.github.com/en/enterprise-cloud@latest/admin/monitoring-activity-in-your-enterprise/reviewing-audit-logs-for-your-enterprise/streaming-the-audit-log-for-your-enterprise) and [System](https://docs.github.com/en/enterprise-server@3.11/admin/monitoring-activity-in-your-enterprise/exploring-user-activity-in-your-enterprise/log-forwarding#enabling-log-forwarding) logs to external system. With some regulated environment also visibility and especially storage of run logs from GitHub Actions would be needed.

Normally Self-hosted GitHub Actions runner doesn't log out any execution log to it's own log, except that job is started and ended. Everything between those is visible only at the GitHub Actions UI.

## Possible solutions

### Webhooks

It's possible to crete webhook that would trigger when job is ended allowing listener to download logs using REST API. As users normally have possibility to also delete the run, this can easily be race which one is faster. Also if runner for some reason fails, logs are not uploaded and that way not downloadable.

This would also require additional openings if GitHub Enterprise Server is in use and it's in airtight environment.

On the plus side this would work also with GitHub Hosted runners.

### GitHub Events (Firehose)

In many cases event listener would allow to do actions after something happens. In this case it doesn't work as firehose doesn't have any action/workflow/job specific events published.

### Soft delete for the logs

If utilizing GHES, user has control over storage used to store run logs. On that level logs can be forced to be kept with e.g. Soft Delete or streaming logs immediately to other place. 

It still has same issue than with webhooks; if runner itself fails for some reason, logs would not be present.

### Logging sidecar (ARC)

With Self-hosted runners it would be possible to configure logging sidecar in a way that sidecar would forward or print logs immediately when they're written to file by runner process (normally at `<runner home>/_diag/blocks` directory).

This would only work with self hosted runner, but would be most robust solution. Issue might be that runner is not streaming logs to files similarly to UI, but only after completion of step. So if e.g. step takes long time to run, whole information of step is published at once. Also files are created time to time with duplicate content

With sidecar logging to STDOUT cluster's normal log collection can be utilized to get logs to centralized system(s).

Runner has also undocumented environment variable [`ACTIONS_RUNNER_PRINT_LOG_TO_STDOUT`](https://github.com/actions/runner/discussions/2897) to control logging, but it seems to be forwarding Runner and Worked logs, not actual job ones.
