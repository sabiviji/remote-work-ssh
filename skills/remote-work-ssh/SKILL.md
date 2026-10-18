---
name: remote-work-ssh
description: Connect to a remote machine over SSH and work in a remote project using alias, key, or password authentication configured in ssh.md. Use when the user asks to ssh into a server, run commands on a remote machine, inspect or edit files on a remote project, or work on a remote project.
metadata:
  origin: community
---

# Remote Work over SSH

Connection details live in `ssh.md`. Read it before every remote session. Never guess hosts, users, ports, key paths, or passwords.

## When to Activate

- The user asks to connect to, log into, or run something on a remote machine
- The user asks to inspect, edit, build, or test a remote project
- The user mentions a server, VPS, or remote host and expects SSH access

## Step 1: Locate ssh.md

Check these paths in order and use the first that exists:

1. `./ssh.md` in the current project
2. `~/.config/remote-work-ssh/ssh.md`

If neither exists, copy `ssh.example.md` from this skill's folder to `~/.config/remote-work-ssh/ssh.md`, tell the user to fill it in, and stop.

## Step 2: Validate the Configuration

`ssh.md` contains one YAML block. Check these fields:

- `auth` must be `alias`, `key`, or `password`.
- `remote_project_path` must be set.
- Fields for the chosen method must be set:

| `auth` | Required fields |
|---|---|
| `alias` | `alias` |
| `key` | `host`, `user`, `identity_file` (`port` defaults to 22) |
| `password` | `host`, `user`, `password_env` (`port` defaults to 22) |

If a required field is missing or `auth` is unknown, stop and report exactly which field is wrong. Do not fall back to another method.

## Step 3: Connect

### alias

Resolve the entry first, then connect:

```sh
ssh -G <alias> | grep -E '^(hostname|user|port) '
ssh -o BatchMode=yes <alias> 'hostname && whoami'
```

### key

- Confirm `identity_file` exists. If it doesn't, stop.
- Use only that key:

```sh
ssh -o BatchMode=yes -o IdentitiesOnly=yes -i <identity_file> -p <port> <user>@<host> 'hostname && whoami'
```

- If `proxy_jump` is set, add `-J <proxy_jump>`.

### password

- Check that `sshpass` is installed with `command -v sshpass`. If it's missing, stop and tell the user to install it.
- Check that the environment variable named by `password_env` is set, without printing its value.
- Pass the value to `sshpass` through the `SSHPASS` environment variable and `-e`. Do not put the password in a command-line argument or in any output.
- Disable key-based attempts so only the password is used:

```sh
SSHPASS="<value of password_env>" sshpass -e ssh -o PubkeyAuthentication=no -o PreferredAuthentications=password -p <port> <user>@<host> 'hostname && whoami'
```

- If `proxy_jump` is set, add `-J <proxy_jump>`.
- Do not use `BatchMode=yes` here, because it disables password authentication.

### Host keys (all methods)

- If the host key is unknown or has changed, stop. Show the fingerprint and ask the user to verify it.
- Never set `StrictHostKeyChecking=no` or similar options that skip verification.

## Step 4: Work in the Remote Project

- Run every command inside `remote_project_path`, for example `ssh ... 'cd <remote_project_path> && <command>'`. Quote the path.
- Before assuming a tool exists on the remote machine, check its manifests or run `command -v <tool>`.
- Do not write outside `remote_project_path` unless the user asks.

## Safety Rules

- Ask before destructive commands such as `rm -rf`, force pushes, dropping databases, disk operations, or service restarts.
- Never print, copy, log, or commit passwords, key contents, or tokens.
- Do not store credentials in any file other than the user's `ssh.md` or the environment.
- Do not run commands on the remote machine that the user has not asked for.

## Unsupported Operations

- Storing passwords in tracked files or in this skill
- Handling interactive MFA or one-time codes on the user's behalf
- Disabling host key verification
- Connecting to hosts that are not defined in `ssh.md`
