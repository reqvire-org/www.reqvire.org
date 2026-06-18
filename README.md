# www.reqvire.org

This repository owns the GitHub Pages deployment for
[www.reqvire.org](https://www.reqvire.org).

The website source is not maintained here. The canonical source lives in the
main Reqvire repository:

```text
reqvire-org/reqvire:/website
```

Deployment is handled by GitHub Actions in this repository. The workflow checks
out `reqvire-org/reqvire`, builds `website/`, and publishes the generated static
artifact to GitHub Pages.

GitHub Pages for this repository should be configured as:

```text
Source: GitHub Actions
Custom domain: reqvire.org
```

GitHub ignores `CNAME` files for Actions-based Pages deployments. The custom
domain must be set in **Settings -> Pages -> Custom domain**. DNS should point
the apex domain to GitHub Pages and `www.reqvire.org` to
`reqvire-org.github.io`; GitHub then handles the www/apex redirect for the
configured custom domain.

Manual deployment:

1. Open **Actions** in this repository.
2. Run **Deploy Website**.
3. Optionally provide a Reqvire git ref, branch, or tag. The default is `main`.
