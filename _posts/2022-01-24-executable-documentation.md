---
title: "Executable docs"
categories:
  - blog
tags:
  - python
  - documentation 
  - Jupyter
  - pyprojectx
header:
    overlay_image: /assets/images/nesscafe.jpg
excerpt: >
  Turn your Python docs into instant notebooks
---
> **TL;DR**
> It's common to include interactive Python sessions inside documentation.
> With a small script we can turn these docs into Jupyter notebooks and serve them instantly.
>
> Example docs:
> 
> Mac/Linux `git clone https://github.com/houbie/tomlkit.git && cd tomlkit &&./pw run-docs`
> 
> Windows `git clone https://github.com/houbie/tomlkit.git && cd tomlkit && pw run-docs`

## Exploring a library
I was recently exploring [tomlkit](https://github.com/sdispater/tomlkit), a library for parsing and editing toml files.

It would have been nice if the docs where a Jupyter notebook that you can play with interactively. No more copy/paste
to the REPL.

Of course this only makes sense if the effort for starting the notebook is less than a dozen copy/pastes.

## The conversion script
The built-in [doctest](https://docs.python.org/3/library/doctest.html) makes it trivial to separate text and code

```python
from doctest import DocTestParser

with open(src, "r") as md:
    docs = DocTestParser().parse(md.read())
    for part in docs:
        if isinstance(part, Example):
            # Example contains source and wanted output
            source = part.source
            ...
        else:
            # plain (markdown) text
            source = part
            ...
```

The documentation parts can then be stored in notebook cells, a list of dicts that is dumped as json to get
the final notebook.

So:
```toml
Modifying
---------
TOML Kit provides an intuitive API to modify TOML documents::
    >>> from tomlkit import dumps
    >>> from tomlkit import parse
    >>> from tomlkit import table
```
is converted to:
```json
[
  {
    "cell_type": "markdown",
    "source": [
      "Modifying\n",
      "---------\n",
      "TOML Kit provides an intuitive API to modify TOML documents::\n"
    ]
  },
  {
    "cell_type": "code",
    "source": [
      "from tomlkit import dumps\n",
      "from tomlkit import parse\n",
      "from tomlkit import table\n"
    ]
  }
]
```

## Launching the Jupyter server
We still need to avoid that our potential users give up when confronted with too long or too complex instructions.

This is where [pyprojectx](https://github.com/houbie/pyprojectx) comes into play; we'll use it to start the entire experimentation session with a oneliner:
```shell
git clone https://github.com/houbie/tomlkit.git && cd tomlkit &&./pw run-docs
```

The _run-docs_ alias is configured in [pyproject.toml](https://github.com/houbie/tomlkit/blob/master/pyproject.toml):
```toml
[tool.pyprojectx]
jupyter = """\
jupyter
.
"""

[tool.pyprojectx.aliases]
run-docs = "python docs/md2notebook.py && pw@jupyter notebook --notebook-dir=docs"
```

Jupyter and the project dir (current dir) are added to the _tool.pyprojectx_ section in pyproject.toml,
making them both available in an isolated virtual environment (no impact on project dependencies).

The _run-docs_ alias runs the conversion script and launches the Jupyter server.

![open tomlkit notebook docs]({{ site.url }}{{ site.baseurl }}/assets/images/open_tomlkit_notebook_docs.png)

When we open _quickstart.ipynb_ we can start fiddling.

![run tomkit docs]({{ site.url }}{{ site.baseurl }}/assets/images/run_tomkit_docs.png)


You can find the [enhanced tomlkit fork on github](https://github.com/houbie/tomlkit).

Happy coding!
