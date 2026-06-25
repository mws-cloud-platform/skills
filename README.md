# MWS Cloud Platform skills library

- [MWS Cloud Platform skills library](#mws-cloud-platform-skills-library)
    - [Plugins](#plugins)
        - [MWS CLI Skill](#mws-cli-skill-plugin)
    - [Installation](#installation)
        - [npx skills](#npx-skills)
        - [Claude Code](#claude-code)
        - [Claude Desktop](#claude-desktop)
    - [Examples](#examples)
        - [Resource management](#resource-management)
    - [Get Help](#get-help)
    - [Creators](#creators)

## Plugins

### MWS CLI Skill Plugin

Manage your resources on the MWS Cloud Platform using [MWS CLI](https://mws.ru/docs/cloud-platform/mws-cli/general/whatis-mws-cli.html).

## Installation

### npx skills

Install skills directly from the repository using [skills.sh](https://skills.sh):

```bash
# All skills in the library
npx skills add https://github.com/mws-cloud-platform/skills
# MWS CLI skill
npx skills add https://github.com/mws-cloud-platform/skills --skill mws-cli
```

### Claude Code

Add the marketplace to Claude Code:

```bash
/plugin marketplace add mws-cloud-platform/skills
```

Install plugins individually:

```bash
/plugin install mws-cli@skills
```

### Claude Desktop

Add the marketplace to Claude Desktop using:

```
Customize -> Personal plugins -> Create plugin -> Add marketplace -> Add from repository -> mws-cloud-platform/skills
```

## Examples

### Resource management

Ask your preferred agent to manage your resources on the MWS Cloud Platform:

```
Show me my powered-on virtual machines 
```

```
Remove disks with name starting with 'test-'
```

## Get Help

Ask for help using the [MWS Cloud Platform Support Center](https://mws.ru/docs/support/about.html).

## Creators

Created and maintained by [MWS Cloud Platform](https://mws.ru/cloud-platform).
