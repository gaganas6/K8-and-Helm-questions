**1. Helm upgrade fails due to version conflict**
**Scenario:** Upgrade fails with a version conflict.
**Answer:**
1.	Check release status and history:
2.	helm status <release>
3.	helm history <release>
4.	Identify cause: chart version mismatch, dependency mismatch, or stuck release state.
5.	Update dependencies if needed: helm dependency update <chart-path>.
6.	Upgrade specifying version and flags:
7.	helm upgrade <release> <chart-path> --version <chart-version> -f values.yaml --atomic
8.	Rollback if the upgrade fails: helm rollback <release> <revision>
•	Key: version conflict is often chart version, dependencies, or Helm release state—not namespace.

**2. Failing Helm hooks**
**Scenario:** Pre-install Job fails, blocking install.
**Answer:**
1.	Identify hook resources:
2.	kubectl get jobs -n <namespace>
3.	kubectl describe job <job-name> -n <namespace>
4.	kubectl logs <job-pod> -n <namespace>
5.	Check hook annotations and delete policies (helm.sh/hook, helm.sh/hook-delete-policy).
6.	Verify dependencies exist before hook execution.
7.	Clean failed jobs before retrying.
8.	If hooks are unreliable, consider moving logic outside Helm hooks.


**3. You have a Helm chart with pre-install hooks that create Jobs. Sometimes these jobs fail, and the Helm release goes into failed state. How can you make the release continue even if the hook fails?**
**Answer:**
1.	Problem:
o	Pre-install hooks run before the main resources. If a hook Job fails, Helm marks the release as failed and stops further deployment.
2.	Solution:
o	Use the hook-delete-policy and hook-weight carefully, but most importantly:
	Add the helm.sh/hook-delete-policy: before-hook-creation,hook-succeeded annotation to clean up completed hooks.
	To allow failure without blocking the release, you can add:
	helm.sh/hook: pre-install
	helm.sh/hook-weight: "0"
	helm.sh/hook-delete-policy: hook-succeeded
	And mark the Job as optional by handling failure in the Job itself, or using helm.sh/hook-delete-policy: before-hook-creation,hook-failed.
3.	Alternative approach:
o	If you want Helm to ignore hook failures, consider moving the Job outside of Helm management or wrapping it in scripts that handle retries/errors, so Helm can continue creating main resources.
