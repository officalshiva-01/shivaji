# shivaji

## Fixing Bob Shell's `EACCES` npm install error

The installer is able to download and verify Bob Shell, but npm then tries to
write the global package to `/usr/local/lib/node_modules`. That directory is
owned by root on most Linux systems, so a normal user receives:

```text
EACCES: permission denied, mkdir '/usr/local/lib/node_modules'
```

Configure npm to put global packages in a directory owned by the current user,
add its executable directory to `PATH`, and then rerun the installer:

```bash
mkdir -p "$HOME/.local"
npm config set prefix "$HOME/.local" --location=user
export PATH="$HOME/.local/bin:$PATH"
printf '\nexport PATH="$HOME/.local/bin:$PATH"\n' >> "$HOME/.bashrc"

curl -fsSL https://bob.ibm.com/download/bobshell.sh | bash
```

Open a new terminal (or run `source ~/.bashrc`) before using `bob`. Verify the
installation with:

```bash
bob --version
```

## Use Bob in Visual Studio Code

Bob Shell runs in a terminal; it does not need a separate VS Code extension.
Open the folder you want Bob to work with and start Bob from VS Code's
integrated terminal:

```bash
code /path/to/project
```

In VS Code, open **Terminal → New Terminal** (or press <kbd>Ctrl</kbd> +
<kbd>`</kbd>), then run:

```bash
source ~/.bashrc
bob
```

If VS Code was already open when the installation completed, restart its
integrated terminal so it receives the updated `PATH`. From there, Bob runs in
the opened project's directory. Use `bob --help` to see its available commands.

This avoids `sudo npm install -g`, which can leave root-owned files in a
user-managed npm setup. The `DEP0169` line in the output is a Node.js
deprecation warning; it is not the cause of the failed installation.
