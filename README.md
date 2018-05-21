# Frame Server Tools

A collection of commands that help in the setup and management of our Frame Hosting servers.

## Globals Arguments

`--debug`  : Enable verbose output / debugging output - mostly good for debugging input / argument issues

`--run`    : Enable destructive behaviour in commands, by default all commands should run in 'dry run' mode.

`-v | --version` : show the current Frame Server Tools version string

## Commands

As we're not using AWS the management of SSH keys on different servers can prove challenging. A couple of convenience commands exist to help with key management.

### Rebuilding Authorized Keys

Rebuilds the `.~/.ssh/authorized_keys` file of the current user. 
```bash
#!/usr/bin/env bash
## Dry run, check output
frame rebuild_keys 

## Will make changes
frame rebuild_keys --run 
```

The new key file is a combination of the current frame master public keyfile and any additional keyfiles in `./ssh/additional_keys`

The previous iteration of `authorized_keys` is moved into the backup folder, for reference. This can be useful if you need to retrieve a custom key that was unknown to you at the time of rebuild.. copy it, make a new file for it in `./ssh/addtional_keys` and then run `frame rebuild --run` again. 

When running in "dry-run" mode (default) the 'rebuilt' key file for the user will be at `~/.ssh/test_authorized_keys` for viewing / debugging.

### Rebuilding All Keys
*REDUIRES SUDO*

Rebuilds authorized keys for all applicable users on the server.

```bash
#!/usr/bin/env bash

# Always dry-run this command first!

## Dry run, will generate a lot of output
frame rebuild_keys 

## Will make changes
frame rebuild_keys --run 

``` 

Includes the `/etc/skel` directory, so new users inherit the most up-to-date key file.

This command requires sudo - it will spawn a subshell for each applicable user and run `frame rebuild_keys`.

### Upgrade Self

Upgrades self to the latest version

```bash
#!/usr/bin/env bash

## Pulls latest master release from git
frame upgrade_self

## check new version
frame --version
```

## Installation

```bash

# Login as user with sudo access

# Navigate to the user's home directoy
cd ~

#clone in repo
git clone https://github.com/framedigital/server-tools.git

# link it up
sudo ln -s "${HOME}/server-tools/frame" "/usr/local/bin/frame"

# test
frame --version
```

## Developing additional commands

Extra commands are welcome to be contributed by the team. If you're making bulk managent or multi-user commands then `utils/runner` will be your friend - it can spawn subshells to run frame commands and handles debugging input and passing in global flags.

See  `frame` for some global vars etc, you can trust variables like `${FST_BASEPATH}` to be correct.

Ideally all commands should be idempotent or account for that sort of scenario.