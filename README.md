# PRMirror
Mirrors pull requests from one repository to another.  This is primarily meant for use on SS13 repos, but it can be modified to fit most use-cases.

## Getting started
- Update ``client.UserAgent`` in ``main.go`` line 48 from ``"YourBotAccount/PRMirror"`` to fit the name of your mirror bot.
- Compile the code by running `go get` and then `go build`
- Copy the included file ``merge-upstream-pull-request.sh`` into the parent directory.
- Make sure that `merge-upstream-pull-request.sh` is marked as executable (`chmod +x merge-upstream-pull-request.sh`)
- Clone the target repo (i.e, the one you want to mirror to) to disk in the parent directory.
- Make sure that you can push new commits back to the repository from the cloned directory, IE: Setup SSH keys or use a PAT (see below)
- A basic config is included.
  - GitHubToken should be a [GitHub Access Token](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/)
  - Your GitHub Access Token needs requested through the owning org if it's an org repo, with basically all permissions.  The ones that work for us are as follows:
    - ORG permissions SHOULD (tentatively) be able to be left ignored
	- Repo Read access to Dependabot alerts, actions variables, administration, codespaces, codespaces lifecycle admin, codespaces metadata, dependabot secrets, metadata, repository advisories, secret scanning alerts, secrets, and security events 
	- Repo Read and Write access to actions, code, codespaces secrets, commit statuses, deployments, discussions, environments, issues, merge queues, pages, pull requests, repository hooks, and workflows 
	- You can *probably* trim this down somewhat, but we've had certain mirrors fail due to touching workflows & similar (i.e, updates to the CI suite) - better safe than sorry!
  - Your git CLI needs to be properly configured to use the personal access token *or* an SSH key to avoid password input every time the bot pushes a new branch.
  - Upstream is at [tgstation/tgstation](https://github.com/tgstation/tgstation/) and Downstream is at [Bird-Lounge/Skyraptor-SS13](https://github.com/Bird-Lounge/Skyraptor-SS13)
    - UpstreamOwner is usually tgstation
    - UpstreamRepo is usually also tgstation
    - DownstreamOwner should be either the owning user or organization - for us, it's Bird-Lounge
    - DownstreamRepo should be the name of the repo you want to mirror to - for us, it's Skyraptor-SS13
  - RepoPath is the path to the repository on disk.
  - ToolPath is the path to the tool from within the repository.  Defaults to ``../merge-upstream-pull-request.sh`` - if you put your repo next to it, things should work out of the box.
  - UseWebhook - should be set to true if you're using the GitHub webhook system instead of scraping the events API
  - WebhookPort - if you're using the webhook system, set the port for the HTTP server to listen on
  - WebhookSecret - if you're using the webhook system, generate a secure secret and set it both on GitHub and in here so we can verify the payloads 
- Make sure before you run the PRMirrorer for the first time that you are 1-1 with your upstream.  You can technically run it with changes, but you might see funky behaviour.
- Run the PRMirrorer standalone first to make sure it works, if it polls and is working, continue to set it up as a service and make sure that it doesn't go down - consider using ``tmux`` to ensure you can always check in on its output log to search for errors.
- You're done.


### Current issues:
- On the first run it may open PRs that you already have.
- If your server running the bot does down and you don't notice for awhile - you will have issues!  Be prepared to use wide-ranging diff tools like ``Meld`` if you notice too many missed mirrors or merge conflicts - it can help suss out things that got lost in translation.
