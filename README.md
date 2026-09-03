# cirrus-binder

A public, unauthenticated [BinderHub](https://binderhub.readthedocs.io) for the NSF NCAR
CIRRUS Kubernetes cluster. Point it at a git repository and get a Jupyter server, no account
required.

- **URL:** https://binder.k8s.ucar.edu — BinderHub at `/`, JupyterHub at `/jupyter/`
- **Images:** built with `dind` and pushed to the `cirrus-binder` project on
  `hub.k8s.ucar.edu`
- **Deployment:** ArgoCD, pulling the upstream `binderhub` chart from the JupyterHub Helm
  repo with `values.yaml` from this repo

There is no local chart. `values.yaml` carries only what is specific to CIRRUS — hostname,
registry, quotas, resources — and leaves the rest of the upstream chart at its defaults.

## Site customization

BinderHub's UI is a React bundle on Bootstrap 5, so there is no server-rendered page to edit.
Everything visual lives in `branding/`:

- `page.html` extends the chart's own template (`{% extends "templates/page.html" %}`) to set
  the title and favicon, swap in the paired NSF NCAR / Binder logo, and mount the banner and
  footer. The banner and footer are `<template>` elements placed by a small inline script,
  since the chart's template exposes no blocks inside `<body>`.
- `banner.html` is the "Runs on CIRRUS" block, included by `page.html`.
- `theme.css` overrides Bootstrap custom properties for the NCAR palette, plus the footer
  styles.
- images are served at `/extra_static/<name>`.

`kustomization.yaml` generates the `binder-branding` ConfigMap from those files, and
`values.yaml` mounts it at `/etc/binderhub/templates` and `/etc/binderhub/static`.