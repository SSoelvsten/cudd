# CUDD

The CUDD package is a package written in C for the manipulation of decision
diagrams. It supports binary decision diagrams (BDDs), algebraic decision
diagrams (ADDs), and Zero-Suppressed BDDs (ZDDs).

<!-- markdown-toc start - Don't edit this section. Run M-x markdown-toc-refresh-toc -->
**Table of Contents**

- [Usage](#usage)
- [Additional Content](#additional-content)
    - [DDDMP](#dddmp)
    - [Nanotrav](#nanotrav)
- [License](#license)
- [Feedback](#feedback)

<!-- markdown-toc end -->

## Usage

To get started with CUDD, you need to place the repository somewhere on your
machine. The simplest way to do so is to add it as a submodule inside the Git
repository of your project.

```bash
git submodule add https://github.com/SSoelvsten/cudd external/cudd
git submodule update
```

Then include the following line in your project's CMakeLists.txt.
```bash
add_subdirectory (external/cudd cudd)
```

Finally, every single executable target is linked to CUDD in the
CMakeLists.txt file with the following lines.
```bash
add_executable(<target> <source>)
target_link_libraries(<target> cudd)
```

At this point, you may include the C header `"cudd.h"` and/or the C++ header
`"cuddObj.hh"` and get started on programming.

## Additional Content

Next to the CUDD library in *cudd/* and its C++11 API in *cplusplus/* and its
dependencies, this folder also contains the following.

### DDDMP

Also included in this distribution is the dddmp library by Giampiero Cabodi and
Stefano Quer.

### Nanotrav

This directory contains a set of packages that allow you to build a test
application based on the CUDD package.

The test application provided in this kit is called nanotrav and is a
simple-minded FSM traversal program. (See the README file and the man page
nanotrav.1 in the nanotrav directory for the details.) It is included so that
you can run a sanity check on your installation.

## License

This software is distributed under the [BSD-3-Clause license](LICENSE).

## Feedback

Send feedback to:

Fabio Somenzi
University of Colorado at Boulder
ECE Dept.
Boulder, CO 80309-0425
Fabio@Colorado.EDU
http://vlsi.colorado.edu/~fabio
