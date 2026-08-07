# [2.0.0](https://github.com/aksyuma/aksyuma.github.io/compare/v1.0.0...v2.0.0) (2026-08-07)


### Bug Fixes

* correct upload-artifact SHA and corepack ordering ([af68c14](https://github.com/aksyuma/aksyuma.github.io/commit/af68c14cc108beebf028d8e819b0c92ea2071af3))
* provide GITHUB_TOKEN to gitleaks for PR scanning ([3b99873](https://github.com/aksyuma/aksyuma.github.io/commit/3b99873019f237bb6192c6988d7e96184ad769f8))
* use full clone depth for gitleaks PR scanning ([8f2e3e7](https://github.com/aksyuma/aksyuma.github.io/commit/8f2e3e71c98e99ad0cef5795250a696f0191d695))


### Features

* artifact promotion, digest verification, smoke test, caching ([a62cc78](https://github.com/aksyuma/aksyuma.github.io/commit/a62cc786844badc5c9743b3b0a165d67426af807))


### BREAKING CHANGES

* Deploy no longer rebuilds — promotes exact CI artifact.

- CI uploads dist/ artifact with SHA-256 digest
- Deploy downloads and verifies artifact instead of rebuilding
- Post-deploy smoke test validates site returns HTTP 200
- Security findings surfaced as GitHub annotations
- Yarn cache enabled on all jobs
- Job timeouts added (5-10 min)
- .node-version file for local/CI parity

Signed-off-by: Amos Syuma <amossyuma@gmail.com>

# 1.0.0 (2026-05-24)


### Features

* add dynamic route for project pages ([d375d8e](https://github.com/aksyuma/aksyuma.github.io/commit/d375d8ec6192955bdbb9983dc1d2c99e19d133df))
* build interactive redacted project card component ([8aeccca](https://github.com/aksyuma/aksyuma.github.io/commit/8aeccca0b42e213c31d90276af0848bd9a1022dc))
* rework base layout to vault theme ([7b249c3](https://github.com/aksyuma/aksyuma.github.io/commit/7b249c3cb4f324a97ad04dab8a6c574e407d8e0b))
* update index page with content collection query ([eae45c0](https://github.com/aksyuma/aksyuma.github.io/commit/eae45c0ecd4dda1731ad7c16dd101863eaa80142))
