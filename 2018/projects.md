# Ideas for 2018 GSOC projects

We don't know the deadline yet. Based on the 2017 deadline it'll
probably be beginning of February.

## Make the hnix nix interpreter work with nixpkgs.

We have a
[Nix interpreter written in Haskell](https://github.com/jwiegley/hnix)
but it can't currently build derivations. The idea is to make it fully nix compatible.

## Define and document API to nix-daemon

## Deterministic Builds

Nixpkgs has a great feature where every build is reproducible, which means that
if you and I both build a package from the repository with the same nixpkgs git
checkout, then we should be guaranteed to get the same package version, package
features, and the same closure of that package as well.

However, this does not ensure builds are deterministic. Deterministic builds are
reproducible all the way down to the binary, on disk-representation. For
example, consider the openssl package. While two users may be able to build the
same version, this doesn't ensure the binaries are exactly identical: openssl
embeds the current compilation date into the resulting binaries, meaning
compiling it today will result in a subtly different binary than what we get
tomorrow. This is one of the causes of indeterminism we often face.

Deterministic builds are incredibly powerful as a security feature, since any
two users should be able to always get the exact same binaries
independently. This opens the door to things like semi-untrusted build machines,
which we don't have to trust to ship us 'clean', un-backdoored binaries, and
makes sure we don't even have to trust Hydra to be secure.

The goal of deterministic builds is to root out these causes of indeterminism,
and get the Nixpkgs repository into a state where a significant portion of
binary builds are reproducible. For now, we're aiming for the system_tarball_pc
derivation, meaning everything up-to a full NixOS ISO should be reproducible,
bit-for-bit.

Much of the work for this project has already been done by Alexander
Kjeldaas. The goal is to get his work upstream into the official repository,
make sure it's part of the next NixOS stable release, and work further towards
expanding deterministic builds.

* **Scope of project**: The first step is merging the work of Alexander Kjeldass
  into the HEAD repository. Furthermore, as this should not take a significant
  amount of the 3 months of GSoC time, we should also expand this to have
  tooling support, in Hydra for example. For example, Debian's "Reproducible
  Builds" CI service simply reruns every build twice in order to weed out very
  obvious sources of build indeterminism (file ordering, CPU usage,
  psuedo-randomness, or timestamps). Extending Hydra to support this should be
  relatively simple, and can be enabled for example only on those packages
  marked with an deterministic = true;. Finally, we'll likely want to continue
  making as many packages as possible deterministic.
* **Required skills**: You should be quite familiar with how to create, patch,
  and develop Nix packages; strong Unix skills, and you'll probably want some
  Perl/C++ background to work on Hydra or Nix. familiarity with the basics of
  the Nixpkgs/NixOS manual are fine. Finally, it's possible in the process we'll
  need to work with upstreams or incorporate patches to our core utilities.

## Localization

Nix / NixOS / NixOps are getting a lot of attention, unfortunately all the
documentation which exists so far is in English. The purpose of this project is
to add infrastructure for localizing all the documentation of Nix, NixOS and
NixOps. Currently a large part of the documentation is in Docbook format, and
the rest is embedded in Nix expression files.

What should be done during this project is:

* Make tools to extract to language file for English
* Make the language file readable by Nix, such that all packages (Nixpkgs) meta
  attributes are localized, as well as all options descriptions (NixOS).
* Package tools for localizing Nix / NixOS.
* Document the process for localizing, and how to keep a local up-to-date.

At the end we should at least have a proof of concept in one locale, including
but not limited to:

* Localization of NixOS Introduction chapter.
* Localization of ~100 package descriptions.
* Localization of ~100 NixOS options.

## Peer-to-Peer substitutes

In general, Linux distributions have multiple mirrors to provide backup for
sources / binaries. Nix has a model which gives it the possibility to ask
trusted remote (or signed files from untrusted sources) about the hash of the
build output for one derivation (recipe), and then the binary can be downloaded
from any untrusted source. Being able to download a binary from any source
implies that we do not have to rely on mirrors for downloading the build
results, and that we can use decentralized ideas, such as P2P networks. Another
issue, inherent with this, is to ensure that we have anonymity among the persons
of such network. Not having anonymity implies that an attacker can use such
network to analyze if a server is vulnerable because it is using an outdated
version of bash / openssl.

A decentralized and anonymized P2P would help by:

* Making popular packages/versions persistent.
* Improve download speed, even from China (no need to "sneakernet").
* Reduce load from cache.nixos.org .

The goal of this project, is to create a new binary substitute based on a
decentralize and anonymized P2P, such as Tribler, which serve the content of the
nix store to other Nix users.

## Graphical and text-mode installers for NixOS

Currently, NixOS doesn't have any automatic, fancy installer. The goal would be
to create a minimal one, that does:

* Partition the hard drive
* Expose some basic configuration options from configuration.nix, things like
creating users and selecting desktop environments
    * Maybe a more advanced mode for selecting servers like OpenSSH
* Install NixOS via `nixos-install`

**Note**: it might be worth adapting existing code base of an already existent
installer to not reinvent partitioning

## langserver.org implementation for nix

http://langserver.org/ - would solve a bunch of IDE problems, though
not indentation and syntax highlighting. Might be too complicated.

## Private files in the nix store

Currently, all files in the nix store are world-readable, which means that NixOS
configurations that need passwords or other credentials need to point to paths
outside of the store. It would be awesome if we could come up with a principled
way to fix this limitation.

Some discussion and existing work on the issue can be found at:
https://github.com/NixOS/nix/issues/8

## Multiple outputs infrastructure in nixpkgs

Nix has a feature known as "multiple outputs", where a single derivation can
produce multiple outputs which can be downloaded and garbage-collected
independently. So for example, glibc might have one output containing the
libraries and another containing the headers, and most users would only need the
libraries and would only need to download the headers when performing a
build. Judicious use of this feature could greatly reduce the size of closures
of various packages/the system configuration. Coming up with a good general way
for "normal-case" packages in nixpkgs to take advantage of this would be a great
benefit.

Note: This has already been partially done in the closure-size branch:
https://github.com/NixOS/nixpkgs/pull/7701

## Distributed nixops deployments

Currently NixOps state is all stored in a local sqlite database, so all
deployment has to be managed from one central machine. A good mechanism for
sharing that state would allow multiple users to manage the same deployments
from their own machines.


## Improvements

### Improve Nix and rewrite Perl scripts in C++

Currently Nix as a tool could use some improvement, there are many issues and
basic features that should be worked on:

* Getting rid of Perl dependency
* Inline docstring support
* Support for channel authentication (http simple authentication)
* Package query caching
* More user friendly and modern interface and output (with colors)
* Support for channel generation with one command
* Bindings for other languages
* More sane defaults

### Low-level improvements to nix performance

There may be some low-hanging fruit for improving performance of evaluation of
Nix expressions or Nix builds. Some ideas that might be worth exploring:

* Using lazy cons/nil lists instead of vectors for the list data type (and
  updating general list functions in the nixpkgs lib accordingly)
* Replacing .drv files, a potential IO bottleneck, with a purely in-memory
  representation of derivations
* Fix some instances of reading entire files into memory instead of streaming
* For the ambitious: multithreaded evaluation and/or building.

### Improve nix-build output for post-processing

When building (using nix-build), the standard out and error output are merged
and captured into build log files under /nix/var/log. Nix injects escape codes
to indicate actions but otherwise leaves things as-is.

It would be great if there was an out-of-band mechanism to indicate Nix
messages, standard output and standard error of a build. Build output is mostly
line-based, so one way would be to leave stdout untouched, and add `{E}` in
front of lines coming in from stderr, and `{N}` for nix-build messages. If a
stdout line begins with `{` it could be escaped with `{O}`. Some care can be
taken for builds that don't print newlines to e.g. indicate progress but have
interjections from stderr.

Another option is to prefix each line with the delta in milliseconds since the
last line of the stream, and mark stderr and nix outputs differently (e.g. "5 "
vs "E5 " vs "N5 ").

A logical next step would be to have nix-build not show output until an error
occurs, or to show a running tail of all builds, each in their own screen area.

One further refinement would be allowing the builder to output out-of-band
messages, to indicate the current build phase for example. This would come in as
a stderr line starting with e.g. `{N}phase:configurePhase`.

The overall goal would be to make building from source more approachable and
more amenable to debug.

### Improve Python support

Currently we have python 2.6 - 3.4 and PyPy support in Nix. Improving Python
support is an ongoing effort, see https://github.com/NixOS/nixpkgs/pull/1467

Main work would include:

* Add wheels support https://github.com/NixOS/nixpkgs/issues/329
* Improve documentation at http://nixos.org/nixpkgs/manual/#python
* Update PyPi2nix script for automated packaging https://github.com/garbas/pypi2nix

Other improvements would include:

* Add python modules support for python 3 and PyPy (like we have it for Python
  2.7)
* Split out usable functions for software having python bindings but not using
  distutils/setuptools

**Note**: talk to the mentor to set a goal of what improvements fit in 3 months.

### Improve support for other programming languages

(The same as Python above, for every major programming language in need of some
improvement, *mutatis mutandis*)

## Promote independence from systemd

Systemd is a hot, polemical, flamewar-prone topic. There is a lot of criticism
about it, mainly about being bloated and hard to maintain. And arguably systemd
goes against the underlying functional philosophy of NixOS. There is some
discussion on the mailing list about that:
http://lists.science.uu.nl/pipermail/nix-dev/2014-December/015361.html

The goal of that subject is to promote independence of systemd, and using some
sane default as s6 or even Gentoo's OpenRC suite.

