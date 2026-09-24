# XTEN — FinOps Delivery OS

XTEN is a secure consultancy delivery platform for onboarding cloud customers, collecting authorised cost and operational data, establishing trusted baselines, reviewing optimisation opportunities, coordinating approved implementation, verifying outcomes and delivering ongoing reporting and governance.

**Specification phase:** this repository does not yet contain a working application. These documents describe required future behaviour, not implemented capabilities. A restricted AWS pilot is an intermediate release, not completion of the product.

## Project documents

- [Product contract](docs/PRODUCT.md): customers, lifecycle, mandatory scope and proposed technical direction.
- [Roadmap](docs/ROADMAP.md): M00–M21 objectives, dependencies, evidence and acceptance gates.
- [Acceptance register](docs/ACCEPTANCE.md): planned verification requirements, all initially `NOT_RUN`.
- [Status](docs/STATUS.md): inspected repository state, review status and outstanding decisions.
- [Agent working rules](AGENTS.md): scope, evidence and safe contribution rules.

Never commit credentials, secrets or real customer data, or expose them in public logs. Use explicitly synthetic fixtures and safe example configuration only. [Ignore rules](.gitignore) help prevent accidental additions but do not remove secrets from Git history or provide a complete data-security control.
