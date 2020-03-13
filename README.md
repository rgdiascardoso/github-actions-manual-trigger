# github-actions-manual-trigger
A simple tutorial for creating manually triggered GitHub Actions workflows

GitHub actions support Webhook events as starting points for running a workflow

Most of these builtin events are tied to specific Gitub events, such as "deployment" or "milestone".

There is, however, a generic event that should be used to trigger a GitHub Actions workflow from an external source, named "repository_dispatch" --link

"repository_dispatch" has a property called "event_type" that can be invoked at the level of the workflow as a custom event, allowing for further granularity in multiple external calls for diferent workflows to the same repository.

It also allows for an optional "client_payload" property, that can store a JSON object that can be passed to the workflow.

At the level of the GitHub Actions workflow, this can be configured as:

name: Hello World Repo Dispatch
on:
  repository_dispatch:
    types: example-event

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
    - run: echo ${{ github.event.client_payload.foo }}


An easy way to trigger such a workflow is a POST request to the GitHub API.

POST /repos/:owner/:repo/dispatches

{
  "event_type": "example-event",
  "client_payload": {
    "foo": "bar"
  }
}