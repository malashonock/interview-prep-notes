---
tags: [Security]
title: 'Security: Cross-site script inclusion (XSSI)'
created: '2024-03-28T08:22:15.406Z'
modified: '2024-03-28T08:28:28.517Z'
---

# Security: Cross-site script inclusion (XSSI)

[XSSI types](https://www.scip.ch/en/?labs.20160414)  
[JSON is not as safe](https://incompleteness.me/blog/2007/03/05/json-is-not-as-safe-as-people-think-it-is/)

The vulnerability regards clients reading from JSON APIs.

In older browsers, built-in JS object/array constructors are overridden (monkey-patched) by a malicious script that gets access to the object/array parsed from JSON, and do whatever with its content.

**Threat mitigation**: Commonly, JSON APIs prepend a `")]}',\n"` string to all JSON responses to make them non-executable.
