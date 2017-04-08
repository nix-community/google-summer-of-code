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
