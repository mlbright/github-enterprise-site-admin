## GitHub Enterprise Site Administration

For several years, my main responsibility at work was [GitHub Enterprise][enterprise] system administration.
Here is a collection of thoughts, in no particular order.

_Last updated: 2022-jan-19_

### Rate Limiting

GitHub Enterprise comes with [built-in rate limiting][rate-limiting].
Unfortunately, it isn't enabled by default, which is a shame.
If you're running an instance with many users and large repositories (ours which had thousands of user accounts and hundreds of daily users, often with heavy workloads) you will likely run into performance issues. 
GitHub Enterprise is a single VM instance.
A single point of failure, and thus one can only scale it vertically.
The default rate limits are reasonable for most workloads, but it's better to start somewhere and adjust them than allow abusive behavior to take foot.

### Hardware

Use dedicated, powerful hardware with lots of RAM and I/O.
CPUs won't hurt either because Git is computing lots of hashes.

### GitHub Apps

Prefer and promote the use of [GitHub Apps][github-apps] instead of legacy OAuth apps or personal access tokens.
It's a more secure and better scheme for integrating with the server.
It also avoids the "service account" pattern, for which a shared user account is created to perform a role.
GitHub Apps are first class actors in GitHub, but do not consume an Enterprise license.

When I was an admin, there wasn't a way to install a GitHub App on all organizations.
However, this can be very useful and [someone wrote a script to do it][appscript].

### GitHub.com

Ask yourself if your organization really needs GitHub Enterprise as opposed to [GitHub.com][dotcom].
Do you really need the headache of administering your own instance?
Be aware that new features are always rolled out to GitHub.com first, and often do not appear in Enterprise until 6 or more months later.

### Hubble

If you run GitHub Enterprise, you will need to have a sense of its usage that goes beyond what is provided by GitHub.
There's a third party tool for that: [Hubble][hubble].

### Delegate to organization owners

Make it clear that organization owners are responsible for managing the users, teams, and repositories in their organizations.
There is no way site administrators can handle this better than the leaders who are managing the developer teams.
Create an _owners manual_ that:

- Provides context and elaborates on the [official documentation][docs] for:
    - Teams and users
    - Repository permissions
    - User responsibilities with regard to security
    - GitHub Apps
- Outlines the responsibilities of GitHub Owners
- Provides examples of correct usage, and usage anti-patterns
- Answers frequent questions

Post the source of this manual in a Site Administration organization, so that it can be contributed to by everyone.
This keeps users and org owners engaged and gives them the sense of ownership they need to operate effectively.

### Site administration organization

Create a site administration organization within the instance.
Publish the source for all code and instance specific documentation there.
This will provide transparency to management and users and give them a sense of the effort that goes into administering the instance.

### Git LFS and repository hygiene

Avoid using Git LFS if possible, and make it clear to users that it is a last resort.
As rule of thumb, if a file isn't diff-able, then it shouldn't belong in Git.
Set the [global large file threshold][push-limits] to something like 5Mb, only making exceptions on repo by repo basis.
This will save you a lot of computing resources and user aggravation.
The users will find better places to put large binaries, their repositories will clone faster, resulting in better builds and streamlined processes.

### Log forwarding

[Do it][logforwarding].

### Announcements and user facing docs

Create a user facing documentation web site whose source is not hosted in GitHub Enterprise.
(Instead consider GitHub.com?)
The purpose of this web site is to keep users abreast of developments on the server.
It should be available in case of outages to the Enterprise instance.

Inform users about:

- Maintenance
- Outages
- Pointers to other documentation
- Frequent user questions
- New developments
- Upcoming versions and features

### Smoke tests

Monitoring the server should be a no brainer using standard tools.
However, beyond that, it's good to run relatively frequent smoke tests.

These might include:

- Clones of medium sized repositories
- Logging in and creating an issue
- Forking a repository

These exercise what a user would experience, and are often far more indicative of server health than traditional metrics.

Run these from different geos: this will often show that problems are localized, not an issue with the GitHub Enterprise server.

Record and visualize the results.

### Slack Channel

Create a Slack channel dedicated to Git and GitHub.
Answer it frequently and collect common questions into the user facing documentation site.

Consider separating the Git and GitHub concerns into 2 channels, but understand that this may also confuse users when issues straddle both things.

Have an explicit and documented support policy on the user facing documentation site.
This should set expectations about support on the Slack channel.

### Default to "Open"

- Favor public repositories to facilitate collaboration and _discovery_.
- Members of an organization or team should be _visible_, unless there is reason not to.

### Disable GitHub Pages

Completely turn off [GitHub Pages][pages].
The most frequent and severe security issues for GitHub Enterprise were related to GitHub Pages.
I'm betting the next high severity issue will also be.
Best to simply turn the feature off.

Users will find other destinations to publish the source of their web sites, documentation, or whatever.
This cleanly separates the concerns of collaborating around _development_, which lie with GitHub, from the concerns of _publication or hosting_.

**That's it for now!**

[dotcom]: https://github.com
[enterprise]: https://github.com/enterprise
[rate-limiting]: https://docs.github.com/en/enterprise-server@3.3/admin/configuration/configuring-your-enterprise/configuring-rate-limits
[github-apps]: https://docs.github.com/en/developers/apps/getting-started-with-apps/about-apps
[docs]: https://docs.github.com/en/enterprise-server@3.3
[push-limits]: https://docs.github.com/en/enterprise-server@3.1/admin/policies/enforcing-policies-for-your-enterprise/enforcing-repository-management-policies-in-your-enterprise#enforcing-a-policy-for-git-push-limits
[logforwarding]: https://docs.github.com/en/enterprise-server@3.3/admin/user-management/monitoring-activity-in-your-enterprise/log-forwarding
[pages]: https://docs.github.com/en/enterprise-server@3.3/pages/getting-started-with-github-pages/about-github-pages
[appscript]: https://gist.github.com/stoe/2eae33bd3d097a59b4a75e997a14c54d
[Hubble]: https://github.com/Autodesk/hubble