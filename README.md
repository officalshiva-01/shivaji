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

This avoids `sudo npm install -g`, which can leave root-owned files in a
user-managed npm setup. The `DEP0169` line in the output is a Node.js
deprecation warning; it is not the cause of the failed installation.
