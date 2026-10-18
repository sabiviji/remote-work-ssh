# remote-work-ssh

An agent skill that connects to a remote machine over SSH and works in a remote project. It supports SSH aliases, key authentication, and password authentication.

## Install

```sh
npx skills add sabiviji/remote-work-ssh
```

To install from a local clone:

```sh
npx skills add /path/to/remote-work-ssh
```

## Configure

Create `ssh.md` in your project or at `~/.config/remote-work-ssh/ssh.md`:

```sh
cp skills/remote-work-ssh/ssh.example.md ~/.config/remote-work-ssh/ssh.md
```

Then choose one authentication method in the file:

- **alias**: uses a `Host` entry in `~/.ssh/config`.
- **key**: uses a private key file.
- **password**: reads the password from an environment variable you name in `ssh.md`.

## Requirements

- `ssh` (OpenSSH client)
- `sshpass`, only for password authentication

## Security

- Keep `ssh.md` out of version control. The included `.gitignore` excludes it.
- Store passwords in environment variables, never in files.
- The skill does not disable host key verification. Verify new host keys before accepting them.

## License

Not yet specified.
