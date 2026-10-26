# Publishing python-kanka to PyPI

Releases come from main after the release PR has successfully merged. Do not publish a release from a PR branch.

## Prepare the release PR

1. Start a clean branch from current main.
2. Choose an unused version. Keep `pyproject.toml`, `src/kanka/_version.py`, and `.bumpversion.cfg` synchronized using bump2version without creating a release tag yet:

   ```bash
   uv run bump2version --no-commit --no-tag --new-version X.Y.Z patch
   uv sync --all-groups
   ```

3. Document changes in `CHANGELOG.md` and include the regenerated `uv.lock`. Keep the supported Python minimum in `requires-python`; `.python-version` selects the development interpreter independently.
4. Run `make format`, `make check`, `uv build`, and `uv run twine check dist/*`. CI must pass on every supported interpreter.
5. Commit and push the changes, open a PR, and wait for it to merge successfully into main. Do not tag or publish the PR commit while the PR is open.

## Publish from main

1. Fetch current main and identify its exact commit. Confirm the release PR is merged and that this main commit contains the intended version and changes.
2. Confirm the version and tag are unused. Never overwrite an existing release tag or reuse a published package version.
3. Create the version tag at that verified main commit and publish the GitHub release with its changelog notes.
4. The existing `Publish to PyPI` workflow runs the supported Python test matrix, builds the wheel and source distribution, and uploads them through PyPI trusted publishing. Routine releases do not require new API tokens or credential changes.
5. Verify the workflow succeeded, then check actual PyPI availability, package metadata, downloaded distribution hashes, and installation. A successful workflow alone does not prove a new version was published when `skip-existing` is enabled.

The workflow also supports manual Test PyPI publication. Use a verified main commit for that path as well; leave the Test PyPI option selected unless production publication is explicitly authorized.

## Existing releases

Published artifacts are immutable. If a release contains an error, assess whether a new version is needed; do not delete, retag, yank, or republish an existing release without explicit authorization.
