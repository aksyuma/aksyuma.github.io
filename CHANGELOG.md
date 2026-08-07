# [2.1.0](https://github.com/aksyuma/aksyuma.github.io/compare/v2.0.0...v2.1.0) (2026-08-07)


### Bug Fixes

* rename catch-all [...slug] to [slug] to resolve /projects 404 ([bea0f93](https://github.com/aksyuma/aksyuma.github.io/commit/bea0f93f563892172ff28f709e8849bff4c01ed0))
* replace default Astro rocket favicon with AS monogram ([5164572](https://github.com/aksyuma/aksyuma.github.io/commit/516457219710e57830ab2039651921bb043e73f8))


### Features

* add /work timeline page with career progression ([eca0124](https://github.com/aksyuma/aksyuma.github.io/commit/eca01243fd576029cd1a60f6c3bf777c88842575))
* add /writings page with Medium archive and upcoming topics ([bf48f0d](https://github.com/aksyuma/aksyuma.github.io/commit/bf48f0dd2a3bdaf8f898b776842c88636ef2fd69))
* implement /about and /projects navigation routes ([bde9e86](https://github.com/aksyuma/aksyuma.github.io/commit/bde9e86cc784f3f218767c79e25acc9b17c828a1))
* redesign /about and /projects for reader engagement ([93b9ef8](https://github.com/aksyuma/aksyuma.github.io/commit/93b9ef819709482f5e6061562e23052606ba34fc))
* redesign homepage with name, hook, tech stack, and case studies ([d7c80cd](https://github.com/aksyuma/aksyuma.github.io/commit/d7c80cdee6c5ff7f2bdd3cce1b6b794fd1dc0f58))
* update /work page with career timeline and certifications ([55db451](https://github.com/aksyuma/aksyuma.github.io/commit/55db451d7a74b8474888b11b23bf0f7f9f1611cf))
* **ux:** site redesign — navigation, pages, work timeline, and certifications ([6b02f91](https://github.com/aksyuma/aksyuma.github.io/commit/6b02f9104e6579b82cc92a0af5df6697a1bed301))

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
