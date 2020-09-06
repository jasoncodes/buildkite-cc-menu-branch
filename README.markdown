# Buildkite CCMenu Current Branch Proxy

This is a quick and dirty proxy written in Ruby which you can point [CCMenu](http://ccmenu.org/) at.
It will determine the current branch for your project’s working tree and then fetch the build
status of that pipeline on [Buildkite](https://buildkite.com/).

See https://buildkite.com/docs/integrations/cc-menu for details on using CCMenu with Buildkite.

## Usage

### Installation

This assumes you use macOS and clone your projects into `~/projects`. The included `launchd` script will make the proxy available at `localhost:39418`. Vary according to one’s taste. Pull Requests to make this more generic are welcome.

Install the proxy with the following:

```sh
cd ~/projects
git clone https://github.com/jasoncodes/buildkite-cc-menu-branch.git
mkdir -p ~/Library/LaunchAgents
ln -s ~/projects/buildkite-cc-menu-branch/launchd.plist ~/Library/LaunchAgents/buildkite-cc-menu-branch.plist
launchctl load ~/Library/LaunchAgents/buildkite-cc-menu-branch.plist
```

Test the proxy is running with `curl localhost:39418`. You should get `Project not found.` if it’s running.

### Configuration

Add a new project entry to CCMenu as you normally would except substitute `https://cc.buildkite.com/` with `http://localhost:39418/` and remove any `branch` query parameter.

Master branch (`demo (master)`): `https://cc.buildkite.com/example/demo.xml?branch=master&access_token=opensesame`
Current branch (`demo (branch)`): `http://localhost:39418/example/demo.xml?access_token=opensesame`

The proxy will lookup the current branch name for `~/projects/demo` and proxy the request to Buildkite using this branch name and the provided access token. Only the pipeline slug in the URL is used to resolve the local git working directory. The organisation slug in the URL is not currently used to resolve the project directory.

It’s recommended that you enable the “with last build label” preference in CCMenu which will display the current branch name in CCMenu’s dropdown. Unfortunately CCMenu locks onto the project name so we cannot dynamically change that to display the current branch name.
