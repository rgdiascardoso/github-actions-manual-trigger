# github-actions-manual-trigger

A simple tutorial for creating externally triggered GitHub Actions workflows

## Overview

GitHub actions support Webhook events as starting points for running a workflow

Most of these builtin events are tied to specific Gitub events, such as "deployment" or "milestone".

There is, however, a generic event that should be used to trigger a GitHub Actions workflow from an external source, named [repository_dispatch](https://help.github.com/en/actions/reference/events-that-trigger-workflows#external-events-repository_dispatch)

## repository_dispatch

"repository_dispatch" has a property called "event_type" that can be invoked at the level of the workflow as a custom event, allowing for further granularity in multiple external calls for diferent workflows to the same repository.

It also allows for an optional "client_payload" property, that can store a JSON object that can be passed to the workflow.

At the level of the GitHub Actions workflow, this can be configured as:

```yml
name: Hello World Repo Dispatch
on:
  repository_dispatch:
    types: example-event

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
    - name: foo
      run: echo ${{ github.event.client_payload.foo }}
```

Such workflow can be triggered by a POST request to the GitHub API, with Basic Authorization with GitHub username and password. The request body should include the "event_type" matching the one defined in the workflow, and an optional "client_payload".

```
POST https://api.github.com/repos/:owner/:repo/dispatches
```

```json
{
  "event_type": "example-event",
  "client_payload": {
    "foo": "bar"
  }
}
```

An example using cURL:

```shell
curl -i -X POST 'https://api.github.com/repos/rgdiascardoso/github-actions-manual-trigger/dispatches' \ 
-u {{ username }}:{{ password }} \
-d '{"event_type": "example-event", "client_payload": {"foo":"bar"}}'
```

A possible better alternative in the long run is to issue a GitHub access token from "Settings\Developer Settings\Personal Access Tokens" and make the POST request with a Bearer Authorization header.

A successful request will be coded 204 No Content, without body.

Additional information on the "repository_dispatch" event can be found [here](https://developer.github.com/v3/repos/#create-a-repository-dispatch-event)

## Solutions using `repository_dispatch`

There are some other tools that utilize the `repository_dispatch` event for you, without needing to run `curl` locally.

### [Actions Panel](https://www.actionspanel.app/)
[Actions Panel](https://www.actionspanel.app/) is a GitHub App that you can use, where the credentials management is done via an OAuth login flow, so you never have to use your username and password, and you never have to manage your own Personal Access Tokens. The UI is hosted for you and conveniently lets you provide parameters to inject into your `repository_dispatch`.

### [peter-evans/repository-dispatch](https://github.com/peter-evans/repository-dispatch)
[peter-evans/repository-dispatch](https://github.com/peter-evans/repository-dispatch) is a GitHub Action which sends a `repository_dispatch` event for you. You can trigger this workflow through some other event, such as adding a comment on a PR or Issue.

### * [Action Button](https://github-action-button.web.app/#details)
[Action Button](https://github-action-button.web.app/#details) is another GitHub App that acts as an easy way for issuing a `curl` command, similar to [Actions Panel](https://www.actionspanel.app/). The [Action Button](https://github-action-button.web.app/#details) suggested approach is to add badges to your README, so no other UI is necessary!
