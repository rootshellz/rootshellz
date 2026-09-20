# Profile stats setup

One workflow, `workflows/update-profile-stats.yml`, generates four Tokyo Night SVGs
under `profile/` daily at 09:23 UTC and on manual dispatch. Only those four SVGs
are committed, together, using the built-in repository `GITHUB_TOKEN`. Unchanged
output produces no commit. The README images appear after the first successful run.

## Token permissions and limitations

Create your own token under **Settings → Developer settings → Personal access
tokens → Fine-grained tokens → Generate new token**. Choose an expiration, resource
owner `rootshellz`, and **Only select repositories**, selecting the repositories
you want included. Set these repository permissions to **Read-only**:

- Commit statuses
- Contents
- Issues
- Pull requests
- Metadata (automatically included)

Leave other permissions unset. No private-repository write permission is needed
by this workflow. A fine-grained token covers one resource owner; organization
repositories require that owner and may require organization approval.

**Upstream limitation:** GitHub Stats Extended's current documentation warns that
fine-grained tokens limit commit statistics to public repositories. Its action
recommends a classic PAT with `repo` and `read:user` for private statistics. Streak
Stats also documents classic `repo` for private contributions, although its basic
setup says no scopes are required. Summary Cards explicitly documents read-only
fine-grained tokens for private aggregates. Therefore a read-only token is the
preferred least-privilege setup, but full private commit coverage in all four
cards is not guaranteed by upstream. Do not interpret a successful run as proof
that every private contribution was counted.

The upstream-documented classic alternative is `repo` + `read:user`; `repo`
unavoidably grants write access to private repositories and cannot be narrowed to
read-only or selected repositories. This conflicts with the requested read-only
policy, so do not broaden the token automatically. The workflow accepts either
token type but does not create tokens or change permissions.

Sources checked during implementation:

- [Stats action v2](https://github.com/stats-organization/github-readme-stats-action#inputs)
- [Stats Extended token permissions and fine-grained limitation](https://github-stats-extended.vercel.app/frontend/docs/deploy/#first-step-get-your-personal-access-token-pat)
- [Summary Cards token permissions](https://github.com/vn7n24fzkq/github-profile-summary-cards#including-private-repo-activity-without-exposing-repo-names)
- [Streak Stats v1 setup](https://github.com/DenverCoder1/github-readme-streak-stats#option-2-github-actions)
- [Checkout v7](https://github.com/actions/checkout)

## Save the secret and generate the cards

1. Open `rootshellz/rootshellz` on GitHub.
2. Select **Settings → Secrets and variables → Actions → New repository secret**.
3. Enter **GH_STATS_TOKEN** as the name, paste your token into **Secret**, and click
   **Add secret**. Never put the token in a file, URL, or workflow option.
4. Open **Your profile → Contribution settings** above the contribution calendar
   and enable **Private contributions**. GitHub then exposes anonymized counts.
5. After the workflow is on the repository's default branch, select **Actions →
   Update profile stats → Run workflow**, select the default branch, and click
   **Run workflow**. Wait for the successful run and its generated-card commit.

[GitHub's private contribution setting](https://docs.github.com/en/account-and-profile/how-tos/contribution-settings/manage-visibility-settings-for-private-contributions-and-achievements)

## What is published

The overview contains a contribution graph and aggregate contribution information.
Its optional email/company/location row is removed before publication; an unknown
layout or detected identifying data stops publication. The other cards render
aggregate statistics, a grade, contribution dates/counts, and language totals.
The PAT is supplied only to generators running on the Actions runner, never to a
hosted card endpoint. No raw responses, repository lists, or other summary-card
outputs are staged.

The language card weights code bytes, not repository counts. It describes code
in accessible owned non-fork repositories, including private repositories the
token can read; it is not a measurement of code personally authored or every
language used in contributions elsewhere. Upstream documents a 1,000-repository
limit. Overview public-repository and star counters remain public-only by design.

[Language card scope and weighting](https://github-stats-extended.vercel.app/frontend/docs/cards/top-languages/)

Actual private totals and generated SVGs must be verified after your first run;
no token or generated placeholder data is included in this repository change.
