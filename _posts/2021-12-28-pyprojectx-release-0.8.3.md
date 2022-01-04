---
title: "Pyprojectx v0.8.3 released"
categories:
  - notice
tags:
  - pyprojectx
  - release
---

{% capture notice %}
### Pyprojectx v0.8.3 has been released :tada:
This is the first official release that is published in [Pypi](https://pypi.org/project/pyprojectx/).

Using pyprojectx in your project is as simple as:

```shell
# run this in your project dir and add pw and pw.bat under version control
curl -LO https://github.com/houbie/pyprojectx/releases/latest/download/wrappers.zip && unzip wrappers.zip && rm -f wrappers.zip
```
**Note:** Windows users can [download](https://github.com/houbie/pyprojectx/releases/latest/download/wrappers.zip) and unzip manually

Run `./pw -h` to show help (`pw -h` on Windows).

Enjoy!

{% endcapture %}

<div class="notice--info">
  {{ notice | markdownify }}
</div>
