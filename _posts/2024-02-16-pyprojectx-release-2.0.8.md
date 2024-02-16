---
title: "Pyprojectx v0.8.5 released"
categories:
  - notice
tags:
  - pyprojectx
  - release
---

{% capture notice %}
### Pyprojectx 2.0.8 has been released :tada:
Pyprojectx now allows you to lock the version of the tools you use in your project.

It is advised to run `./pw --lock` to prevent your toolchain to suddenly break (yes [this happens](https://upcycled-code.com/blog/the-broken-version-breakdown)!).


Using pyprojectx in your project is as simple as:

```shell
curl -LO https://github.com/houbie/pyprojectx/releases/latest/download/wrappers.zip && unzip wrappers.zip && rm -f wrappers.zip
```

or on Windows:
```Powershell
Invoke-WebRequest https://github.com/pyprojectx/pyprojectx/releases/latest/download/wrappers.zip -OutFile wrappers.zip; Expand-Archive -Force -Path wrappers.zip -DestinationPath .; Remove-Item -Path wrappers.zip
```

and adding pw and pw.bat to version control.

Then you can, for example, run `./pw --add pdm` and `./pw pdm init` (or `pw --add poetry`) to initialize a new project.


Other notable changes in this release:
* simplified configuration
* combine multiple tools into tool contexts

Enjoy!

{% endcapture %}

<div class="notice--info">
  {{ notice | markdownify }}
</div>
