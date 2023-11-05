---
title: "How to embed live demos inside your slides"
categories:
  - blog
tags:
  - python
  - java
  - javascript
  - typescript
  - demo
  - slides
  - Jupyter
  - pyprojectx
header:
    overlay_image: /assets/images/show.jpg
excerpt: >
  Let your code steal the show
---
> **TL;DR**
> Frequent switching on and off presentation mode, or tabbing through all open windows, breaks the flow of your
> presentation.
> 
> But did you ever try to run code and shell commands from within your slides?
> 
> `git clone https://github.com/houbie/code-demo-slides && cd code-demo-slides/demo-slides && ./pw start`

## The ingredients
### markdown + reveal.js
Embedding code inside PowerPoint by taking screenshots from your code editor, is just too tedious.

Luckily, [reveal.js](https://revealjs.com/) can turn plain markdown into clean slides,
without wasting time with fixing layout. 

### Jupyter + RISE
By combining formatted text with runnable code, Jupyter has become ubiquitous in data science with Python.

But it has more to offer than only Python: the [Jupyter kernels wiki](https://github.com/jupyter/jupyter/wiki/Jupyter-kernels)
lists over 150 available kernels, providing a plethora of programming languages to play with.

By adding reveal.js on top of Jupyter, [RISE](https://rise.readthedocs.io/en/stable/index.html) makes it possible
to present notebooks as slide shows.

### Pyprojectx
While RISE allows us to run code and scripts without leaving our slides, we need some additional plugins and
configuration to fix f.e. the scrolling of large output.

It takes some effort to install and configure the complete toolset. But it is really frustrating
when your setup breaks every few months, because some newer, incompatible version of some tool got installed in the meantime.

Just like npm, [Pyprojectx](https://pyprojectx.github.io/) allows you to pin all your dev dependencies and install them
locally inside a directory.

Merely running `./pw start` will implicitly install all tools and runs initialization scripts.
The only things you need to install yourself, are Python 3.8+ and the runtimes required by your demos (Java, Node.js, ...).

## Sharing the slide deck
You can generate a static html, containing the slides and the demo output:
```shell
./pw generate-slides
```

## Example slides
The [example project](https://github.com/houbie/code-demo-slides) contains four slide decks:
* demo slides: explain basic usage and how to write slides
* java: contains a code snippet that uses a maven dependency
* nodejs: run TypeScript and JavaScript code using npm dependencies
* python: use Jupyter to demo the code of your standard python project, without affecting the project's main dependencies

## The details
> It's the pyproject.toml, stupid.

```toml
[tool.pyprojectx.jupyter]
requirements = [
    "IPython ~=8.0",
    ...
    "RISE ~=5.7",
    "jupyter_contrib_nbextensions ~=0.5",
]

# activate the extensions that fix scrolling
post-install="""
jupyter contrib nbextension install --sys-prefix
jupyter nbextension enable autoscroll/main --sys-prefix
jupyter nbextension enable scroll_down/main --sys-prefix"""

[tool.pyprojectx.aliases]
# convenience scripts
start = "jupyter notebook slides.ipynb -y"
generate-slides = "jupyter nbconvert --to slides *.ipynb && python -m process_html"
```

## Conclusion
> Now start writing code that sounds like poetry in programmers ears, bring it on stage, and let it perform!
