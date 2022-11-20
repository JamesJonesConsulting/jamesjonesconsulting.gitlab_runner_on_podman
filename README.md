# Ansible Collection

## `jamesjonesconsulting.gitlab_runner_on_podman`

This collection is for setting up GitLab Runner self-hostedagents on Podman. If you've worked with Podman, there several 'gotchas'
and this role takes care of the one's I've seen and will extend it as others come to light.  This is a useful way to run hybrid jobs on your 
private network and doesn't incur costs associated with using 'shared' runners. This allows long and specialized jobs to be 
offloaded to private self-hosted 'runners' and the lightweight jobs in shared runners mitigate bottlenecks associated with a
single 'shared' runner approach (more costly, slower and may require 'poking holes' into your secure private network).

This role only supports one type of runner but will be extended to support runners in your kubernetes cluster (which is pretty much
supported in the GitLab interface so it's just a few 'tweaks' here and there).

Currently, this collection contains 'one' role called `jamesjonesconsulting.gitlab_runner_on_podman.gitlab_runner_setup` that sets up a user group that has permissions to the socket and integrates pretty seemlessly into the tools I aformentioned. 

## Usage

The role is called like this in an ansible playbook (note: you will need to be running `ansible-core`)

```
include_role:
  name: jamesjonesconsulting.gitlab_runner_on_podman.gitlab_runner_setup
vars:
  gitlab_runner_username: gitlab-runner
  gitlab_runner_podman_group: docker
  gitlab_runner_url: https://gitlab.com/
  gitlab_runner_registration_token: abcdefghijklmnopqrstuv
  gitlab_runner_tags: cool-jobs,private-net
  gitlab_runner_count: 1
  gitlab_runner_run_untagged: true
```

### Options for the ansible collection description
* `gitlab_runner_username` - Defaults to 'gitlab-runner' and can be skipped if you don't need to overwrite it (this is the normal user created by the gitlab-runner package).
* `gitlab_runner_podman_group` - This is passed to the `jamesjonesconsulting.podman_socket_group_permissions` collection which does all the 'podman' magic. It's a 'default' so you can skip it if you don't need to override it.
* `gitlab_runner_url` - This is another default. You can skip it unless you are hosting GitLab yourself and you need to update this url accordingly.
* `gitlab_runner_registration_token` - This is provided by your instance of GitLab and is manditory.
* `gitlab_runner_tags` - This is optional to set tags on registration. These are often updated in the web interface as needed so this can be skipped as this can and often is modified after provisioning agents.
* `gitlab_runner_count` - The 'default' is 1 but if you need multiple instances on the same host, you can increase that here based on the capacity of the host.
* `gitlab_runner_run_untagged` - This is a 'default' that permits agents to pick up untagged jobs. This can be modifed once the host is provisioned it you want the agent more 'exclusive'.

## Setup

Usually you have a `requirements.yml` file you can specify the collection like this:

```
collections:
  - name: https://github.com/JamesJonesConsulting/jamesjonesconsulting.gitlab_runner_on_podman.git
    type: git
    version: main
```

Or, you can just install it via command line as well like this:

```
ansible-galaxy collection install git+https://github.com/JamesJonesConsulting/jamesjonesconsulting.gitlab_runner_on_podman.git,main
```
