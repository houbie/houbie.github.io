---
title: "The Broken Version Breakdown"
categories:
  - blog
tags:
  - python
  - build tools
  - dependency management
  - pyprojectx
header:
    overlay_color: "#000"
    overlay_filter: "0.3"
    overlay_image: /assets/images/road-crack.jpg
excerpt: >
  Why `pip install my-favorite-tool==1.2.3` might break at any time.
---
> **TL;DR**
> Pypi is a real treasure box full of great Python tools.
> Unfortunately, released versions of most of them can suddenly be broken by a downstream dependency release.
>
> [Pyprojectx](https://pyprojectx.github.io) can prevent that broken tools impact your project.

## The Broken Version
[PDM](https://pdm.fming.dev/) is a great tool for managing dependencies, building and publishing Python projects.

When using PDM in my own projects, I pin the version (and the versions of other tools) in my `pyproject.toml` file:
```toml
[tool.pyprojectx]
pdm = "pdm==2.5.3"
```

Nevertheless, my builds suddenly started to fail:
```
...
  File "/usr/local/lib/python3.9/site-packages/urllib3/response.py", line 794, in _fp_read
    return self._fp.read(amt) if amt is not None else self._fp.read()
  File "/usr/local/lib/python3.9/site-packages/cachecontrol/filewrapper.py", line 96, in read
    self._close()
  File "/usr/local/lib/python3.9/site-packages/cachecontrol/filewrapper.py", line 76, in _close
    self.__callback(result)
  File "/usr/local/lib/python3.9/site-packages/cachecontrol/controller.py", line 353, in cache_response
    self._cache_set(cache_url, request, response, body, expires_time)
  File "/usr/local/lib/python3.9/site-packages/cachecontrol/controller.py", line 274, in _cache_set
    self.serializer.dumps(request, response, body),
  File "/usr/local/lib/python3.9/site-packages/cachecontrol/serialize.py", line 54, in dumps
    u"strict": response.strict,
AttributeError: 'HTTPResponse' object has no attribute 'strict'
```

## The Breakdown
What happened here?

The cache of my CI/CD expired and PDM was reinstalled from scratch with `pip install pdm==2.5.3` (the same version as before).

From PDM's _PKG-INFO_ file, we can see that it depends on _cachecontrol >=0.12.11_,
which  on its turn, depends on the latest version of _requests_.

It turns out that a new release of a transitive dependency, broke PDM version 2.5.3 forever (and probably all older versions as well).

It's ironic that a tool, that is supposed to manage your dependencies, is itself broken by a downstream dependency.

## Tools versus libraries
As library author, you can't limit your users to specific versions of your dependencies.
That's why dependencies of Python libraries are loosely specified (just as in Node.js).

As user of a tool however, you want to be able to install a specific version of that tool and expect it to work consistently.
But unfortunately, pip doesn't distinguish between libraries and tools.

## The Solution
The broken PDM build was a wake-up call to add locking support to [pyprojectx v2](https://github.com/houbie/pyprojectx).

Running `./pw --lock` generates a `pw.lock` file with the exact versions of the tools _and_ all their dependencies.

This is yet another lock file to manage, but it's a small price to pay for reproducible builds.

[Pyprojectx](https://github.com/houbie/pyprojectx) itself uses locked versions of PDM, ruff and other tools.

[Pybreaker](https://github.com/danielfm/pybreaker) is a simpler project without production dependencies.
It uses pyprojectx to manage both tool dependencies and the project's virtual environment.

## What about dev dependencies?
Tools like PDM and Poetry have the _chicken and egg_ problem and obviously can't install themselves.

Besides that _dev dependencies_ are in general misunderstood in the Python community.
The idea is borrowed from Nodejs, but as such doesn't exist in Python.

In Nodejs, dev dependencies are installed together with their own dependencies and don't impact the production environment.

In Python, dev dependencies are installed in the same environment as the production dependencies
and therefore change the dependency graph of the production environment. This typically leads to using outdated versions of libraries.

[Pyprojectx](https://github.com/houbie/pyprojectx) isolates tools from production dependencies and uses a separate
virtual environment for them.

## Conclusion
If you want to prevent that potential contributors drop out because your build is broken or too hard to get started with,
consider using [pyprojectx](https://pyprojectx.github.io).
