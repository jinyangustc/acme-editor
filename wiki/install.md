# Installation

## Mac

I currently use a Macbook Air (13-inch, Early 2015) and the system version is
macOS Sierra 10.12.5.

### 1. Grab plan9port source code from github.

``` bash
git clone https://github.com/9fans/plan9port.git /usr/local/plan9
```
I put it at `/usr/local/` because the manual says it is the convention.
Alternatively, you can install plan9port via hombrew:
``` bash
brew install plan9port
```

### 2. Compile and install

plan9port provides a script to compile and install binaries.
```bash
cd /usr/local/plan9
./INSTALL
```
And you wait. After the compile is finished, there will be an instruction on the
screen to show how to config environment variables. I add the following to the
`.profile`:

```
PLAN9=/usr/local/plan9
PATH=$PATH:$PLAN9/bin
```

### 3. Install FUSE

The 9p protocol needs FUSE to regeister stuff into the *nix file system. You can
get the `.pkg` from the [osxfuse](https://osxfuse.github.io) homepage or via
homebrew:
```bash
brew cask install osxfuse
```
Then you should find a new entry in the `System Preferences`:

![FUSE](/images/osxfuse.png)

Note that FUSE is not required to *run* acme. However, if you want to mount acme
or plumber into your file system, you will need it.

### 4. Launch Acme

Now you should be able to run Acme from the command line:
```bash
acme
```
