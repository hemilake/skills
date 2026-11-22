# Hemilake Skills

Public Agent Skills for connecting AI assistants to a Hemilake context lake.

## Install

Install with the Agent Skills CLI:

```sh
npx skills add hemilake/skills
```

To inspect available skills before installing:

```sh
npx skills add hemilake/skills --list
```

## Available skills

| Skill | What it does |
|---|---|
| [`connect-hemilake`](skills/connect-hemilake/SKILL.md) | Connects Claude Code, Codex, or another MCP client to a Hemilake lake over remote MCP with OAuth and verifies the connection with `connect_status`. |

## References

- [Connect guide (`connect.md`)](https://hemilake.com/connect.md)
- [Hemilake manual](https://hemilake.com/manual)
