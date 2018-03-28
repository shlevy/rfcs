---
feature: nix-release-model
start-date: 2018-03-28
author: Shea Levy <shea@shealevy.com>
co-authors: Michael Raskin <7c6f434c@mail.ru> (any other Nix committers on-board?)
related-issues: (will contain links to implementation PRs)
---

# Summary
[summary]: #summary

A model of when, what, and how to make new releases of Nix.

# Motivation
[motivation]: #motivation

We want to set clear expectations for users, provide guidance for
developers, and provide both groups the benefits of easy availability
of new improvements.

# Detailed design
[design]: #detailed-design

The proposed process contains a number of interrelated components.
I've numbered them for ease of reference and discussion, not to
indicate any meaningful ordering. Any aspect of this can be suspended
at the security team's discretion when handling a vulnerability. Items
marked with a * need technical work to implement, which needn't delay
the rest of the process.

1. Keep `master` as close to release readiness as possible. This
   includes testing, documentation, changelog entries, etc. being
   ready *before* merge.
2. \*Use code review and automated checks, enforced through branch
    protection, to ensure point 1 (and generally improve quality and
    increase knowledge spread).
3. Follow semver. In light of 1, this includes ensuring any necessary
   semver bumps happen before a relevant change is merged.
4. Any community member may request a new release by opening an issue
   on GitHub.
5. If any Nix core team member thinks the release is warranted, they
   can assign themselves the ticket and assume release manager
   responsibilities for the release, barring another team member's
   explained disagreement with the timing. Core team members are
   trusted to use their best judgment in this decision, balancing both
   the needs of the community at large and the needs of developers
   working on Nix. In particular, if an especially major change has
   landed recently or is nearing readiness, the release manager should
   either branch off before the relevant change or be prepared to do
   extra stabilization work.
6. If no core team member is available to do the release work but the
   team agrees (through its normal decision procedures) that a release
   is warranted, a volunteer from the community can serve as release
   manager.
7. Under normal circumstances, there will be at least one mainline
   release between each `NixOS` stable release. An explicit decision
   *not* to release should be made by the core team if a stable
   release is approaching and no new release has been made.
8. The release manager will branch off a `#-stabilization` branch
   (e.g. `2.0.1-stabilization`) in advance of any release.
9. Changes aimed at getting the release ready (fixing bugs, improving
   docs, etc.) should target the `#-stabilization` branch and then
   be merged forward into `master`. No commit should exist on the
   `#-stabilization` branch without very soon thereafter being merged
   into `master` except under rare circumstances. This implies that
   especially large changes ready around a release should either be
   included before stabilization branch-off (with extra time to
   stabilize the new feature) or should, if possible, wait to be
   merged to `master` until after the release. Which path is taken
   should be a collaboration between the release manager and the
   developers of the change in question.
10. New development unrelated to the new release can go directly into
    `master` in parallel with a release stabilization.
11. During stabilization, the release manager should update
    `nixUnstable` to point to various commits of the `#-stabilization`
    branch, to easily enable wider testing and integration.
12. When the release manager determines readiness, the relevant commit
    is announced to the community. Barring objections or last-minute
    fixes judged valid by the release manager (or core team), the
    commit is tagged and the branch receives no further commits.
    Normal development against `master` continues. Readiness is a
    judgment call, and should require increased scrutiny/validation
    for a release with more complex/major changes.
13. Associated with each `NixOS` stable release is an associated
    `maintained` series of `Nix`, with the same support lifetime as
    the stable release.
14. If `master` has not yet diverged enough to warrant a minor semver
    bump relative to the latest release of a `maintained` series, a
    maintenance release can follow the normal stabilization workflow.
15. If `master` has diverged too much, a `#-maintenance` branch (e.g.
    `2.1-maintenance` can be created.
16. Any important bugfix can be cherry-picked from `master` to a
    `#-maintenance` branch at any committer's discretion.
17. In rare cases, if a change truly only makes sense on a
    `#-maintenance` branch (e.g. when we added support to `1.11` to
    be able to work with a `2.0` database schema) it can be targeted
    directly to the `#-maintenance` branch. This should be avoided if
    possible and subject to especially careful testing and review.
18. A maintenance release can be tagged off of the `#-maintenance`
    branch at any time *after* there is a mainline release that
    contains all of the fixes included in the desired maintenance
    release, at the discretion of any Nix core team member.
19. \*Automated testing, ideally applied to all mainline PRs, should
     ensure that all releases of all supported maintained series are
     able to successfully install master if a release were cut. This
     should include a number of definitions of "install", especially
     `NixOS` upgrades.

# Drawbacks
[drawbacks]: #drawbacks

The main drawback to having a formalized model in general is our model
may not match a reasonable productive process in practice and so may
lead to obnoxious busy work or delays for no purpose.

The drawbacks of this specific model are a bit hard to see without
putting it into practice. One thing that seems apparent is there is a
non-trivial amount of complexity (19 bullet points!) which could
perhaps be reduced. It also may prove difficult under this model to
develop especially large features, as without the right development
practices (e.g. feature flags on `master`) this can tend to create
new long-lived branches that can effectively become the new `master`.

# Alternatives
[alternatives]: #alternatives

If we don't do anything at all, we are likely to have more long-lived
unreleased branches and significant undertested divergence, as well as
lack of guidance to the Core team for how to handle release requests.

This model is a synthesis of a [discussion] on the nix-core mailing
list, some other concrete designs can be seen there.

[discussion]: https://groups.google.com/forum/#!msg/nix-core/9L7jZ9W8VGc/8LaBUc_tBQAJ

# Unresolved questions
[unresolved]: #unresolved-questions

What should the testing components implied by this proposal look like?

# Future work
[future]: #future-work

The main future work required by accepting this model is putting into
place the requisite automated testing. There is additional small
administrative overhead implied, including enabling branch protection
and opening the Nix release process to the core team.

We may in the future decouple maintenance support from the `NixOS`
release cycle. Any work which involves splitting components out of
`Nix` or breaking `Nix` itself up into components will also need to
keep this in mind.

Automated testing of semver bounds could be nice. Newer versions
running older testsuites, alternating new and old versions running
operations on the same store, old versions running all new versions
testsuites except tests explicitly marked as being bugfixes since
then, etc.

Explicit guidelines/process around developing major features without
long-lived branches or mainline instability.
