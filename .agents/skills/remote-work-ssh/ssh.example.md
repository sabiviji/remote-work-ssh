# SSH connection

Copy this file to `ssh.md` (in your project or at `~/.config/remote-work-ssh/ssh.md`) and fill in one authentication method. Never commit `ssh.md`.

```yaml
# Choose one: alias | key | password
auth: alias

# Always required
remote_project_path: /home/user/project

# auth: alias
# Uses a Host entry from ~/.ssh/config (keys, agents, jump hosts, ports).
alias: my-server

# auth: key
# host: server.example.com
# user: user
# port: 22                          # optional, default 22
# identity_file: ~/.ssh/id_ed25519
# proxy_jump: bastion.example.com   # optional

# auth: password
# Requires sshpass. The password is read from an environment variable, not from this file.
# host: server.example.com
# user: user
# port: 22                          # optional, default 22
# password_env: REMOTE_SSH_PASSWORD
# proxy_jump: bastion.example.com   # optional
```
