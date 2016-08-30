# Update Patches with `quilt`

## Usage

```
Usage: quilt [--trace[=verbose]] [--quiltrc=XX] command [-h] ...
       quilt --version
Commands are:
        add       fold    new       remove    top
        annotate  fork    next      rename    unapplied
        applied   graph   patches   revert    upgrade
        delete    grep    pop       series
        diff      header  previous  setup
        edit      import  push      shell
        files     mail    refresh   snapshot
```

## Config

`.quiltrc`

```
QUILT_DIFF_ARGS="--no-timestamps --no-index -p ab --color=auto"
QUILT_REFRESH_ARGS="--no-timestamps --no-index -p ab"
QUILT_PATCH_OPTS="--unified"
QUILT_DIFF_OPTS="-p"
EDITOR="vim"
```

## Basic Concepts and Operation

Quilt manages a stack of patches. Patches are applied incrementally on top of the base tree plus all preceding patches. They can be pushed on top of the stack (`quilt push`), and popped off the stack (`quilt pop`). Commands are available for querying the contents of the series file (`quilt series`), the contents of the stack (`quilt applied`, `quilt previous`, `quilt top`), and the patches that are not applied at a particular moment (`quilt next`, `quilt unapplied`). By default, most commands apply to the topmost patch on the stack.

When files in the working directory are changed, those changes become part of the working state of the topmost patch, provided that those files are part of the patch. Files that are not part of a patch must be added before modifying them so that `quilt` is aware of the original versions of the files. The `quilt refresh` command regenerates a patch. After the refresh, the patch and the working state are the same.

Patch files are located in the patches sub-directory of the source tree. The QUILT_PATCHES environment variable can be used to override this location. The patches directory may contain sub-directories. patches may also be a symbolic link instead of a directory.

A file called `series` contains a list of patch file names that defines the order in which patches are applied. Unless there are means by which series files can be generated automatically, they are usually provided along with a set of patches. In series, each patch file name is on a separate line. Patch files are identified by pathnames that are relative to the `patches` directory; patches may be in sub-directories below the patches directory. Lines in the series file that start with a hash character (#) are ignored. When quilt adds, removes, or renames patches, it automatically updates the series file. Users of quilt can modify series files while some patches are applied, as long as the applied patches remain in their original order.

```
work/ -+- ...
       |- patches/ -+- series
       |            |- patch2.diff
       |            |- patch1.diff
       |            +- ...
       +- .pc/ -+- applied-patches
                |- patch1.diff/ -+- ...
                |- patch2.diff/ -+- ...
                +- ...
```

## Use Case

### New Patch

```
# New patch
$ quilt new 00-xxxx.patch

# Add changes into patch (equal to: quilt edit foo.c)
$ quilt add foo.c
$ vim foo.c

# Generate/update patch (result in patches/00-xxxx.patch)
$ quilt refresh

# Check diff
$ quilt diff
```

### Import External Patches

```
# Import patches
$ quilt import /path/to/external/patches
# Apply patches
$ quilt push -a
```

### Updating Patches for a New Upstream Version

```
$ quilt push -f
Applying patch 01_disable_broken_test.diff
patching file tests/regressiontests/test_utils/tests.py
Hunk #1 FAILED at 422.
1 out of 1 hunk FAILED -- saving rejects to file tests/regressiontests/test_utils/tests.py.rej
Applied patch 01_disable_broken_test.diff (forced; needs refresh)
$ vim tests/regressiontests/test_utils/tests.py    
$ quilt refresh
Refreshed patch 01_disable_broken_test.diff
```

> https://raphaelhertzog.com/2012/08/08/how-to-use-quilt-to-manage-patches-in-debian-packages/

## Reference

- https://stuff.mit.edu/afs/athena/system/i386_deb50/os/usr/share/doc/quilt/quilt.html
- http://linux.die.net/man/1/quilt
- https://raphaelhertzog.com/2012/08/08/how-to-use-quilt-to-manage-patches-in-debian-packages/
- http://blog.csdn.net/jasonchen_gbd/article/details/44163105
