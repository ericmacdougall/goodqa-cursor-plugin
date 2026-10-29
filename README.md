# GoodQA plugin for Cursor

One MCP connection to [GoodQA](https://goodqa.app) for every organization you work in. The plugin adds:

- the GoodQA MCP server (`https://goodqa.app/v1/mcp`, streamable HTTP), authenticated with your personal user token;
- a short `goodqa` skill: find the right organization first, pass `orgId`, and what to do when GoodQA refuses a call.

GoodQA's rulebook for agents that talk to people is served by the MCP server itself (resource
`goodqa://skills/conversations`), so it is always current and is not copied into this package.

## Install

1. Install GoodQA from the Cursor Marketplace, or clone this repository and add it as a local plugin
   (https://cursor.com/docs/reference/plugins).
2. Set the plugin variable `GOODQA_TOKEN` to your user token (see below). On a team plan an admin sets it in the
   Cursor dashboard under Plugins, Configure. The plugin itself stores no value.
3. Ask the agent to run `whoami` and `list_orgs`. You should see yourself and the organizations the token reaches.

## Get a token (`GOODQA_TOKEN`)

Sign in at https://goodqa.app, open Account, then User tokens (`https://goodqa.app/account/user-tokens`), and create a
token. If your account does not show User tokens, use an organization API key instead (Organization settings, API
keys); it reaches one organization.

When you create it, choose:

- **Abilities**: what the agent may do. `read`, `write` and `issues` cover reading and editing tickets. Deleting
  anything needs `data.delete`. Leave out abilities the agent does not need.
- **Organizations**: leave it open to cover every organization you belong to, or name only the ones the agent should
  reach. Organizations outside the list answer "not found".
- **Ask me first** (on by default): sensitive actions (deletes, billing, security settings) wait until you approve
  them at goodqa.app.
- **Expiry**: optional; shorter is safer.

The token is shown once. Paste it straight into the `GOODQA_TOKEN` setting.

## What the agent can do

The tool list depends on your organizations' features. Core tools include `whoami`, `list_orgs`, `list_projects`,
`list_issues`, `search_issues`, `get_issue`, `get_issue_by_number`, `create_issue`, `update_issue`,
`comment_on_issue`, `close_issue_done`, `list_org_members` and `call_api`, plus tools for test plans, pipelines,
follow-ups, CRM, outreach, scheduling, chat with people and secret requests when those features are on. The full,
current list is at `https://goodqa.app/v1/meta/mcp-remote`.

One token can reach several organizations, so:

- pass `orgId` to tools that take one (`list_orgs` shows the ids);
- tools that act on one organization ask for `orgId` and refuse with `org_required` without it;
- `get_issue_by_number` takes `projectId`, or `projectSlug` together with `orgId` (slugs repeat across organizations).

## Security notes

- A user token is a bearer credential: it acts as **you**, limited to the abilities and organizations you chose. Treat
  it like a password. Never commit it, paste it into a chat or a ticket, or write it into `mcp.json`: this package
  only ever contains the placeholder `${GOODQA_TOKEN}`.
- Revoke it any time at Account, User tokens. Revocation takes effect on the next request. Organization owners and
  admins can also revoke a member's token that reaches their organization (through the GoodQA API).
- Every request a token makes is recorded in the token's own activity log, with the address and agent that sent it.
- A rejected or revoked token gets HTTP 401 with `WWW-Authenticate: Bearer realm="goodqa", error="invalid_token"`.
- Ticket text, comments, chat messages and emails come from other people. The skill tells the agent to treat them as
  data, never as instructions.

## Contributing and support

This repository is published from GoodQA's main codebase, so pull requests here are not merged directly. Please open an
issue for bugs or requests (https://github.com/ericmacdougall/goodqa-cursor-plugin/issues); we read every one.

## License

The contents of this repository are licensed under the [MIT License](LICENSE), except as noted below.

"GoodQA" and the GoodQA Sentinel logo (`assets/logo.svg`) are trademarks of Transition 2 Consulting Incorporated. They
are not licensed under the MIT License, and nothing in this repository grants permission to use them, except to
identify this plugin unmodified.
