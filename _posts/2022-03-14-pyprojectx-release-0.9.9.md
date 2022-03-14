---
title: "Pyprojectx v0.9.9 released"
categories:
  - notice
tags:
  - pyprojectx
  - release
---

{% capture notice %}
### Pyprojectx v0.9.9 has been released :tada:
Version [0.9.9](https://github.com/pyprojectx/pyprojectx/releases/tag/v0.9.9) is released! This will be the last release before going stable.

The project moved to its own organization and now has [extensive documentation](https://pyprojectx.github.io/) :smiley:

Using pyprojectx in your project is as simple as:

**Linux/Mac**
```bash
curl -LO https://github.com/pyprojectx/pyprojectx/releases/latest/download/wrappers.zip && unzip wrappers.zip && rm -f wrappers.zip
```

**Windows**
```powershell
(Invoke-WebRequest https://github.com/pyprojectx/pyprojectx/releases/latest/download/wrappers.zip).Content | Expand-Archive -DestinationPath .
```

Run `./pw -h` to show help (`pw -h` on Windows).

Enjoy!

{% endcapture %}

<div class="notice--info">
  {{ notice | markdownify }}
</div>
