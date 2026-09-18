# Troubleshooting: curl vs curl-minimal on Amazon Linux 2023

## Incident Summary

During an AWS Systems Manager Run Command exercise, an attempt was made to install
the `curl` RPM package across the Linux fleet.

The installation failed because Amazon Linux 2023 already had `curl-minimal`
installed.

The `curl` command itself was already available and functional on both instances.

## Environment

- OS: Amazon Linux 2023
- Architecture: x86_64
- Fleet size: 2 EC2 instances
- Management: AWS Systems Manager Session Manager / Run Command
- Region: ap-south-1

## Initial Command

The following operation was attempted:

```bash
dnf install -y curl
```

The package manager reported a conflict between the installed `curl-minimal`
package and the full `curl` package.

The relevant installed package was:

```text
curl-minimal-8.21.0-5.amzn2023.0.1.x86_64
```

## Root Cause

Both instances already had `curl-minimal` installed.

The `curl-minimal` package provides the `/usr/bin/curl` command, so installing
the full `curl` package was unnecessary for this exercise.

The package conflict occurred because the installed `curl-minimal` package
conflicted with the full `curl` package available from the Amazon Linux
repository.

## Verification

The fleet was verified using AWS Systems Manager Run Command.

Results on both instances:

```text
RPM:
curl-minimal-8.21.0-5.amzn2023.0.1.x86_64

Binary:
/usr/bin/curl

Version:
curl 8.21.0
```

Therefore, the required curl functionality was already available on both
instances.

## Why the SSM Command Initially Reported Success

The original shell commands continued executing after:

```bash
dnf install -y curl
```

failed.

The subsequent command:

```bash
curl --version
```

succeeded because `curl-minimal` had already provided the curl binary.

This demonstrated an automation error-handling problem:

```text
dnf install
    |
    +-- FAILED
    |
    v
script continued
    |
    v
curl --version
    |
    +-- SUCCESS
    |
    v
overall command appeared successful
```

The important lesson is that an SSM command reporting `Success` does not
necessarily mean that every intended operation inside a shell script succeeded.

The script itself must handle errors and verify the desired state.

## Production Lesson

Automation should fail when a required operation fails.

For shell-based automation, consider:

```bash
set -euo pipefail
```

This helps prevent a script from silently continuing after an important
command fails.

Automation should also verify the desired state rather than assuming that
an installation or configuration change succeeded.

For example:

```bash
rpm -q curl-minimal
command -v curl
curl --version
```

These checks verify the actual state of the system.

## Corrective Action

No package replacement was performed.

The existing `curl-minimal` installation was retained because:

1. The curl command was already available.
2. The required functionality was already present.
3. Replacing the package was unnecessary.
4. Unnecessary package changes increase operational risk.

The fleet was left in its original working state.

## Key DevOps/SRE Takeaways

- SSM `Success` should not replace application-level verification.
- Shell scripts need explicit failure handling.
- Package managers can report dependency or package conflicts even when the
  requested functionality already exists.
- Desired-state verification is more important than blindly executing
  installation commands.
- Avoid unnecessary changes to production systems.
- Fleet-wide automation should be designed to be safe and repeatable.
- A failed change should not automatically be followed by additional changes
  without first understanding the system state.