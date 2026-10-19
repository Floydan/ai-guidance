---
id: working-agreement--nordvik
type: rule
title: Working agreement, Nordvik exceptions
summary: Nordvik's legacy invoicing module is verified by a manual smoke test.
layer: client
client: nordvik
owner: "[NAME]"
version: 1
status: active
overrides:
  - id: working-agreement
    section: exceptions
    mode: append
    reason: Legacy invoicing module, to be replaced in 2027.
---

## Exceptions {#exceptions}

Run the invoicing smoke test by hand after any change in `Invoicing/`.
