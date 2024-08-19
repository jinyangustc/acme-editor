# Installation

## Mac

I currently use a Macbook Air (M1, 2020) and the system version is
macOS 13.6.7.

### 1. Grab plan9port source code from github.

```bash
git clone https://github.com/9fans/plan9port.git ~/git
```

I put it at `~/git/`.

### 2. Compile and install

plan9port provides a script to compile and install binaries.

```bash
cd ~/git/plan9port
./INSTALL
```

And you wait. After the compile is finished, there will be an instruction on the
screen to show how to config environment variables. I add the following to the
`.profile`:

```bash
PLAN9=/usr/local/plan9
PATH=$PATH:$PLAN9/bin
```

Or the following to fish config:

```fish
set -gx PLAN9 $HOME/git/plan9port
fish_add_path $PLAN9/bin
```

### 3. Install FUSE

The 9p protocol needs FUSE to regeister stuff into the \*nix file system. You can
get the `.pkg` from the [osxfuse](https://osxfuse.github.io) homepage or via
homebrew:

```bash
brew install --cask macfuse
```

Then you should find a new entry in the `System Preferences`:

![FUSE](/images/osxfuse.png)

Note that FUSE is not required to _run_ acme. However, if you want to mount acme
or plumber into your file system, you will need it.

### 4. Launch Acme

Now you should be able to run Acme from the command line:

```bash
acme
```
