# plan9port-config

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

### 4. Launch Acme

Now you should be able to run Acme from the command line:
```bash
acme
```

# Notes

## Configuration

## Editing

## Plumber

## Work with Unix Tools

# Reference

## Articles and Manuals

[The Acme User Interface for Programmers](http://acme.cat-v.org) lists various
resources abou the acme editor.

Russ Cox (rsc) gives a nice video introduction in [A Tour of
Acme](https://research.swtch.com/acme).

[Ruben Berenguel](http://www.mostlymaths.net/p/contact.html) posted several
blogs about acme.

[Brian Zwahr](http://www.mostlymaths.net/p/contact.html) shared his joys and
pains during a 3-months experience with acme in a series of blogs (9 episodes),
from entry to ~~give up~~ master :).

[Jesper Louis
Andersen](http://jlouisramblings.blogspot.sg/2013/04/acme-as-editor_20.html)
used acme for a long time (months at least) and talked about it in several
posts.


## Existing Configurations

- [Ev Bogdanov's config](https://github.com/evbogdanov/acme)
- [Michael Auchter's config.](https://github.com/auchter/dotfiles/tree/master/plan9)
- [Jesper Louis Andersen's config](https://github.com/jlouis/plan9-setup)