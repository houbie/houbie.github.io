---
title: "Pyprojectx v0.8.4 released"
categories:
  - notice
tags:
  - pyprojectx
  - release
---

{% capture notice %}
### Pyprojectx v0.8.4 has been released :tada:
This release saves you some typing by allowing to run aliases with abbreviations (similar to running
[gradle tasks](https://docs.gradle.org/current/userguide/command_line_interface.html#sec:name_abbreviation)).

Type `./pw foBa` (or even _fB_) to run alias foo-bar or fooBar.


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
