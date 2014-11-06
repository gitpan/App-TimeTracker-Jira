# NAME

App::TimeTracker::Command::Jira - App::TimeTracker Jira plugin

# VERSION

version 0.1

# DESCRIPTION

This plugin integrates into Atlassian Jira
[https://www.atlassian.com/software/jira](https://www.atlassian.com/software/jira).

It can set the description and tags of the current task based on data
coming from Jira, set the owner of the ticket and update the
worklog. If you also use the `Git` plugin, this plugin will
generate branch names based on Jira ticket information.

# CONFIGURATION

## plugins

Add `Jira` to the list of plugins.

## jira

add a hash named `jira`, containing the following keys:

### server \[REQUIRED\]

The URL of the Jira instance (without a trailing slash).

### username \[REQUIRED\]

Username to connect with.

### password \[REQUIRED\]

Password to connect with. Beware: stored in clear text!

### set\_assignee\_to

If set, unassigned tickets will be assigned to this user.

### log\_time\_spent

If set, an entry will be created in the ticket's work log

# NEW COMMANDS ADDED TO THE DEFAULT ONES

none

# CHANGES TO DEFAULT COMMANDS

## start, continue

### --jira

    ~/perl/Your-Project$ tracker start --jira ABC-1

If `--jira` is set to a valid ticket identifier:

- set or append the ticket subject in the task description ("Adding more cruft")
- add the ticket number to the tasks tags ("ABC-1")
- if `Git` is also used, determine a save branch name from the ticket identifier and subject, and change into this branch ("ABC-1\_adding\_more\_cruft")
- set the owner of the ticket in Jira (given `set_assignee_to` is set in config)
- updates the status of the ticket in Jira (given `set_status/start/transition` is set in config)

## stop

If <log\_time\_spent> is set in config, adds and entry to the worklog of the Jira ticket.
If <set\_status/stop/transition> is set in config and the current Jira ticket state is <set\_status/start/target\_state>, updates the status of the ticket

# CAVEATS

Note that for the setting of assignees at workflow transitions like "Start Progress" to work,
the workflow transition needs to have a (workflow) "Screen" assigned where the field "Assignee" is part of.
Otherwise you will get a HTTP 400 response with an error like:

    Field 'assignee' cannot be set. It is not on the appropriate screen, or unknown

# EXAMPLE CONFIG

    {
        "plugins" : [
            "Git",
            "Jira"
        ],
        "jira" : {
            "username" : "dingo",
            "password" : "secret",
            "set_assignee_to" : "dingo",
            "log_time_spent" : "1",
            "server_url" : "http://localhost:8080",
            "set_status": {
                "start": { "transition": "Start Progress", "target_state": "In Progress" },
                "stop": { "transition": "Stop Progress" }
            }
        }
    }

# AUTHOR

Michael Kröll <pepl@cpan.org>

# COPYRIGHT AND LICENSE

This software is copyright (c) 2014 by Michael Kröll.

This is free software; you can redistribute it and/or modify it under
the same terms as the Perl 5 programming language system itself.
