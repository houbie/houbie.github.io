---
title: "Pyprojectx v0.8.5 released"
categories:
  - notice
tags:
  - pyprojectx
  - release
---

{% capture notice %}
### Pyprojectx v0.8.5 has been released :tada:
This is a bugfix release that improves command line help.

If you forgot the exact syntax of a command alias, just type the first letters:
```shell
./pw c
# output (depending on your config):
# 'c' is ambiguous
# Candidates are:
# clean, clean-all, check
```

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
