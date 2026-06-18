# www.reqvire.org

This repository owns the GitHub Pages deployment for
[www.reqvire.org](https://www.reqvire.org).

The website source is not maintained here. The canonical source lives in the
main Reqvire repository:

```text
reqvire-org/reqvire:/website
```

Deployment is handled by GitHub Actions in this repository. The workflow checks
out `reqvire-org/reqvire`, builds `website/`, adds the custom-domain `CNAME`,
and publishes the generated static artifact to GitHub Pages.

GitHub Pages for this repository should be configured as:

```text
Source: GitHub Actions
Domain: www.reqvire.org
```

Manual deployment:

1. Open **Actions** in this repository.
2. Run **Deploy Website**.
3. Optionally provide a Reqvire git ref, branch, or tag. The default is `main`.
