# simplecalc
simplecalc
copy and past
#!/bin/sh
#
# An example hook script to prepare the commit log message.
# Called by "git commit" with the name of the file that has the
# commit message, followed by the description of the commit
# message's source. The hook's purpose is to edit the commit
# message file. If the hook fails with a non-zero status,
# the commit is aborted.
#
# To enable this hook, rename this file to "prepare-commit-msg".

# This hook includes:
# 1. Removal of the "# Please enter the commit message..." help message.
# 2. Validation to ensure a JIRA Ticket Number is included in the commit message.

COMMIT_MSG_FILE=$1
COMMIT_SOURCE=$2
SHA1=$3

# Remove default Git message (help text)
/usr/bin/perl -i.bak -ne 'print unless(m/^. Please enter the commit message/..m/^#$/)' "$COMMIT_MSG_FILE"

# Define a pattern for JIRA ticket numbers (e.g., "JIRA-123")
PATTERN="SC+-[0-9]+"

# Extract the commit message (first meaningful line)
COMMIT_MSG=$(head -n1 "$COMMIT_MSG_FILE")

# Check if the commit message matches the JIRA ticket pattern
if ! echo "$COMMIT_MSG" | grep -Eq "$PATTERN"; then
    echo "ERROR! Bad commit message."
    echo "    '$COMMIT_MSG' is missing a JIRA Ticket Number."
    echo "Example: 'SC-123 Fix login issue'"
    exit 1
fi

exit 0
