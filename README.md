# MCP Linker servers

The curated MCP server catalog used by the [MCP Linker](https://github.com/milisp/mcp-linker)
desktop app and the MCP Linker website. Each server is one JSON file in the
official MCP Registry [`server.json`](https://static.modelcontextprotocol.io/schemas/2025-12-11/server.schema.json)
format, plus one SVG icon.

Anyone can list a server in the official [MCP Registry](https://registry.modelcontextprotocol.io),
and MCP Linker already browses it. This catalog is the reviewed, featured
subset shown first.

## Layout

```
index.json          display order, categories, and collections
servers/<id>.json   one server, server.json format
icons/<id>.svg      its icon
collections/<id>.json  a themed set of servers
schema/             vendored server.json schema (2025-12-11)
scripts/validate.mjs
```

## Adding a server

1. Pick an `<id>`: lowercase kebab-case, e.g. `my-service`.
2. Create `servers/<id>.json`. Copy an existing entry with the same transport:
   `notion.json` for a remote server, `clickup.json` for an npm package.
   - `name` is `io.github.milisp.mcp-linker/<id>`.
   - `description` is at most 100 characters. Longer text goes in `fullDescription`.
   - Declare user-supplied values as `environmentVariables` or headers with
     `isSecret: true`. **Never commit a real token.**
   - Remote URLs must use `https`. Plain `http` is only accepted for loopback
     servers exposed by a local desktop app.
3. Add `icons/<id>.svg` and set `icons` to
   `[{ "src": "https://cdn.jsdelivr.net/gh/milisp/mcp-servers@main/icons/<id>.svg", "mimeType": "image/svg+xml", "sizes": ["any"] }]`.
4. Add `<id>` to `servers` in `index.json`. Array order is display order.
5. Run `bun install && bun run validate`, then open a pull request.

You don't need to clone anything: on GitHub, open `servers/`, choose
**Add file → Create new file**, and GitHub forks the repository and opens the
pull request for you.

No GitHub account? Email the server name, endpoint or package, required
inputs, and a link to its documentation to help@milisp.dev.

### Catalog metadata

MCP Linker reads its own fields from
`_meta["io.modelcontextprotocol.registry/publisher-provided"]["io.github.milisp.mcp-linker"]`:

| Field | Required | Meaning |
|---|---|---|
| `category` | yes | One of `index.json` `categories` |
| `configName` | no | Key written into client configuration files |
| `featured` | no | `true` shows the server in the Featured section |
| `fullDescription` | no | Details beyond the 100-character `description` |

These are locally curated definitions, not claims of official registry
publication or ownership of the publisher's namespace. `version: "1.0.0"`
versions the catalog definition, not the upstream software.

### Icons

- Use the brand's official assets or [Simple Icons](https://simpleicons.org).
  Prefer the square mark over a wordmark.
- One SVG with a `viewBox`, no `width`/`height` on the root, at most 32 KB.
- No scripts, event handlers, `<foreignObject>`, or external references.
  Clients render these files, so validation rejects anything executable.

`icons/workato.svg` is a placeholder until an official mark is available.

## Collections

A collection groups catalog servers for one kind of work, such as
`collections/ios.json`:

```json
{
  "title": "iOS development",
  "description": "At most 200 characters.",
  "servers": ["mobilebuildmcp", "figma", "sentry", "linear"]
}
```

Every id must be a server in `index.json`, and a collection needs at least two.
List the collection id in `index.json` `collections`; array order is display
order.

## Badge

Once your server is listed, you can link to its page from your README. Linking
is optional and has no effect on review or placement:

```md
[![Add to MCP Linker](https://img.shields.io/badge/MCP_Linker-Add-4f46e5?style=for-the-badge)](https://mcp-linker.milisp.dev/servers/<id>)
```

## Review

Every pull request is checked by `bun run validate` and reviewed by hand.
A server is accepted only if its endpoint or package is the vendor's official
one and installing it needs no credentials beyond what the user enters.

## License

The catalog data and scripts are [MIT](LICENSE). Icons from Simple Icons are
CC0-1.0. Product names, logos, and brands are the property of their owners and
are used only to identify their MCP servers; inclusion does not imply
endorsement.
