[![banner](doc/img/repo-banner@2x.png)](https://oceanprotocol.com)

<h1 align="center">dev-ocean: Ocean Engineering</h1>

<p align="center">
  <img src="https://media.giphy.com/media/Xbaqg3KwpxHjO/giphy.gif" width="270">
</p>

> 🕶 Ocean Engineering Pages with documentation about Development, DevOps, QA, Security, etc.
> [oceanprotocol.com](https://oceanprotocol.com)

---

**This repo contains internal documentation for all Ocean engineering processes. You will find most of the key documents on our [central documentation site](https://docs.oceanprotocol.com) under Core Concepts.**

---

The Ocean Engineering practice follows a list of  [key principles](https://docs.oceanprotocol.com/concepts/principles/) based on the **CAMS** acronym (Culture, Automation, Measurement and Sharing).

- [Docs: Engineering Principles](https://docs.oceanprotocol.com/concepts/principles/)

See [Ocean project map](https://github.com/oceanprotocol/engineering/blob/master/PROJECT_MAP.md) for current development status and handy links.

- [Project Map](https://github.com/oceanprotocol/engineering/blob/master/PROJECT_MAP.md)

## Areas

This repo is divided into different areas:

- [Docs: Architecture](https://docs.oceanprotocol.com/concepts/architecture/)
- [Development](doc/development.md)
- [DevOps](doc/devops.md)
- [QA](doc/qa.md)
- [Security](doc/security.md)
- [Lifecycle](doc/alm.md)

## Additional Information

- **[Ocean Protocol Documentation](https://docs.oceanprotocol.com)**
- [Docs: Software Components](https://docs.oceanprotocol.com/concepts/components/)
- [Docs: Engineering Principles](https://docs.oceanprotocol.com/concepts/principles/)
- [Ocean Github Boards](doc/alm/boards.md)
- [GitHub Engineering Board](https://github.com/oceanprotocol/dev-ocean/projects/1)
- [GitHub Issues](https://github.com/oceanprotocol/dev-ocean/issues)
- [Milestones](https://github.com/oceanprotocol/dev-ocean/milestones?direction=asc&sort=due_date&state=open)

## Integration into `oceanprotocol/docs`

To include a markdown document from this repo on docs.oceanprotocol.com, you need to add 4 required values to a document's YAML frontmatter. E.g. for the [doc/architecture.md](doc/architecture.md) document:

```yaml
---
title: Architecture Overview
description: This page is a central point for documenting the architecture of Ocean Protocol.
slug: /concepts/architecture/
section: concepts
---
```

For more requirements and information please see [External Content Files](https://github.com/oceanprotocol/docs#external-content-files) and [Markdown File Requirements](https://github.com/oceanprotocol/docs#markdown-file-requirements) on the [oceanprotocol/docs](https://github.com/oceanprotocol/docs#markdown-file-requirements) repo.

## Contributing

- See [Docs: Contributing](https://docs.oceanprotocol.com/concepts/contributing/)
