# Contributing to MetisCloud

You can help by reporting bugs, improving the guides, or making installation and
validation easier to use. For a larger change, open an issue first so we can
agree on its scope before you spend time on it.

## Report a bug

Use the issue tracker on the host where you follow the project:
[GitHub Issues](https://github.com/andreT30/metiscloud/issues) or
[GitLab Issues](https://gitlab.com/andreT30/metiscloud/-/issues).
Search existing reports first. If you do not find the problem, open an issue with:

- Your MetisCloud version and the page or command involved.
- Steps to reproduce the problem.
- What you expected and what happened.
- A short, sanitized error message.
- Relevant database, APEX and SQLcl versions for installation problems.

Use sample values in place of real names, identifiers and endpoints. Do not
attach credentials, wallets, connection definitions, customer data or complete
logs. Report suspected vulnerabilities privately through [SECURITY.md](SECURITY.md).

## Suggest an improvement

Describe the task you are trying to accomplish, who would use the change and
what is difficult today. For a documentation correction, include the page or
section and explain what was unclear or did not work.

Application feature requests are welcome as issues. This repository contains
the installable distribution, deployment scripts and guides; application
development source is maintained separately.

## Submit a pull request or merge request

1. Fork the repository and create a branch for one focused change.
2. Make the change and update any affected instructions or examples.
3. Check local documentation links and any changed installer syntax:

   ```bash
   bash -n db/deployer/install_mc_bundle.sh
   ```

4. Open a pull request on GitHub or a merge request on GitLab, using the same
   host as your fork. Explain the problem, your change and the checks you ran.
   Link the related issue if there is one.

Keep examples runnable with clearly marked placeholders. Avoid unrelated
formatting or cleanup in the same contribution.

Please do not include replacement bundles or unpacked application/database
source in a pull request or merge request. Discuss release-artifact changes with
the maintainer first. A syntax check does not prove deployment behavior; explain which changes
you tested and which still need a disposable database environment.

If you test against a database, use an environment you are authorized to change.
Describe the outcome without publishing its connection details or runtime data.
Screenshots and videos must contain only anonymized demo content and need
maintainer approval before inclusion.

## Licensing

By submitting a contribution, you agree that it is licensed under the same
[PolyForm Noncommercial License 1.0.0](LICENSE) as this repository and that the
project owner may relicense the contribution. Only submit material you have
the right to contribute.
