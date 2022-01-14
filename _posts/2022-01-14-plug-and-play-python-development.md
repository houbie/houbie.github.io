---
title: "Plug-and-play Python development"
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
    overlay_image: /assets/images/plug-and-play.jpg
excerpt: >
  Remove the frustrations between <br>
  `git clone` and `🎉 build successful`
---
> **TL;DR**
> Most Python projects require quite some tooling to be installed on your laptop before you can build them. 
> Why isn't it just as easy as `npm install && npm test`?
> Learn how [pyprojectx](https://github.com/houbie/pyprojectx) can add reproducible, plug-and-play builds to your Python projects.

## Install hell
The Python ecosystem provides lots of nice development tools like code formatters, linters, dependency managers, etc.
Seasoned Pythonistas may have forgotten, but installing them can be daunting for noobs, even frustrating for developers
coming from other platforms like Nodejs or Java.

My team recently kicked off its first Python project, using [Poetry](https://python-poetry.org/) for dependency management. 
Half of the team (5 out of 10) struggled with the installation, caused by broken Homebrews, shell issues or ... Windows.

## What can we do about it?
### Stop whining and RTFM
We can document all prerequisites and expect everyone to just follow the instructions, but:
* Potential contributors of your OS project will drop out when it's too much hassle to create a simple pull request.
* Teammates end up using different versions of tools. _But it works on my machine._

### Docker
Using a docker image, that provides all the required tools, has become popular for CI/CD servers. Fol local development,
it's kind of heavy and as such less popular. The latest license changes of Docker Desktop won't make it more popular.

### Dev dependencies
Why not use a tool like [Poetry](https://python-poetry.org/) or [PDM](https://pdm.fming.dev/) that installs all other
tools as dev dependencies as npm does?

With Python, all your (recursive) dependencies end up in the same flat dependency list. So adding dev dependencies
impacts your production dependencies. It will typically downgrade some libraries to older (slower, flawed?) versions.

Or worse: they cause dependency conflicts. F.e. I onc tried to add [AWS SAM](https://docs.aws.amazon.com/serverless-application-model/index.html)
as dev dependency, but failed because of a boto3 version conflicts.

Npm works with a nested dependency tree and can install multiple versions of the same library. That's why it's safe
to add dev dependencies in npm.

### The Gradle way
Gradle, the most popular build tool in Java land, treats reproducible builds as top priority.
They release (breaking) changes at rapid pace, but nevertheless projects (or commits) relying on older versions are not impacted. 

The principle to achieve this, is as brilliant as it is simple: use wrapper scripts that intercept all commands, install
the required Gradle version if necessary and delegate the command to that version.
These wrapper scripts (a *nix and a Windows script) are committed to git (or other VCS), together with a config file
and a small bootstrap jar.

So when you need to patch an older version of your project that relies on another Gradle version, you don't have to worry
about it as the correct Gradle will be used, both on developer machines and on CI/CD servers. Just use `gradlew build`
in stead off `gradle build` and you're future-proof.

The wrapper scripts however don't solve dependency conflicts among libraries used by build scripts. Java also lacks the nested
dependency trees that npm has. Gradle solves this by grouping dependencies in isolated _configurations_,
allowing different tools to use different versions of common libraries.

## Meet pyprojectx
By combining some ideas from Gradle and npm, pyprojectx turns a standard _pyproject.toml_ config file into an
executable and self-contained build script, with python 3.7+ being the only prerequisite.

You only need to download the wrapper scripts into your project directory (or any empty directory)
* osx / linux :
```shell
curl -LO https://github.com/houbie/pyprojectx/releases/latest/download/wrappers.zip && unzip wrappers.zip && rm -f wrappers.zip
```
* windows: unpack the [wrappers zip](https://github.com/houbie/pyprojectx/releases/latest/download/wrappers.zip)

and specify which tool(sets) you want to use in  _pyproject.toml_:
```toml
[tool.pyprojectx]
pdm = "pdm==1.12.6"
black = "black==21.7b0"
https = "httpie"
```
**Tip** run `./pw --init project` to create a new _pyproject.toml_ or to append sample config if the file already exists.
{: .notice--info}

Now you can use the configured tools as usual by merely typing `./pw` (`pw` on Windows) in front of them:
```shell
./pw pdm --help
./pw https POST pie.dev/post message='Pyprojectx is awesome!'
```

You can alias commands and simple shell scripts that you use often:
```toml
[tool.pyprojectx.aliases]
install = "poetry install"
run = "poetry run"
test = "poetry run pytest"
clean = """\
rm -r .venv
rm -r dist"""
build = "pw@install && pw@test && pw@poetry build"
```

Aliases also come in handy when writing CI/CD scripts because they are easy to test on your local machine and avoid
debugging on the CI/CD server. See pyprojectx own [github action build script](https://github.com/houbie/pyprojectx/blob/main/.github/workflows/build.yml)

Pyprojectx eats its own dog food, so you can try it out yourself if you have python 3.7+ installed:
```shell
git clone https://github.com/houbie/pyprojectx.git && cd pyprojectx && ./pw build
```

### How it works
The _pw_ script creates a _.pyprojectx_ directory where all the tools (including pyprojectx itself) are installed
in isolated virtual environments (cfg. npm's _node_modules_).

The _tool.pyprojectx_ values are expected to be in [Requirements File Format](https://pip.pypa.io/en/stable/reference/requirements-file-format/),
leaving it free to/how specify versions and to install multiple tools together. A hash of the requirements is used
to determine the virtual environment's directory. So if you change the version of a tool, it will get re-installed.

The _pw_ script _guesses_ the virtual environment to use based on the provided command, so `./pw https --args` delegates
to the _https_ script in the virtual environment containing _httpie_.
Invoking other scripts provided by the same virtual environment can be achieved with _aliases_ that explicitly select
a _tool.pyprojectx_ tool entry:
```toml
[tool.pyprojectx.aliases]
# httpie provides both http and https scripts
http = "@https:http"
post-json = "@https:http POST pie.dev/post message=Awesome!"
```
**NOTE** `./pw http POST pie.dev/post message='a message'` will fail because the _http_ alias doesn't cope with the quotes and
spaces in the command. This limitation still needs to be solved, but as workaround you can either create an extra
tool entry `http = "httpie"` or create an alias that includes all arguments (as in the _post-json_ example above).
{: .notice--warning}

## Conclusion
Pyprojectx can bring the power of npm dev dependencies and npm scripts to Python, helping to get developers faster
onboarded.

No more _make_ files, finally!
