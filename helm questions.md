Helm upgrade fails due to version conflict
Scenario: Upgrade fails with a version conflict.
Answer:
1.	Check release status and history:
2.	helm status <release>
3.	helm history <release>
4.	Identify cause: chart version mismatch, dependency mismatch, or stuck release state.
5.	Update dependencies if needed: helm dependency update <chart-path>.
6.	Upgrade specifying version and flags:
7.	helm upgrade <release> <chart-path> --version <chart-version> -f values.yaml --atomic
8.	Rollback if the upgrade fails: helm rollback <release> <revision>
•	Key: version conflict is often chart version, dependencies, or Helm release state—not namespace.
