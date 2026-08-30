# FS.GG.Coordination.Authority

Protected Git journal authority for the FS.GG coordination protocol v2.

This repository is a control-plane ledger, not a source-code or work-queue
repository. Authoritative protocol transitions are immutable commits on
sharded branches named:

```text
refs/heads/fsgg/v2/journal/<kind>/<shard>
```

Writers must read the current object ID, create a one-parent commit, and push
with an exact expected-old-object-ID lease. Two active repository rulesets
enforce the boundary:

- `v2-journal-writer` (`21872113`) restricts branch creation and update; only
  the `fs-gg-cross-repo-dispatch` App (`4166418`) is a bypass actor.
- `v2-journal-integrity` (`21872115`) prohibits deletion and non-fast-forward
  updates with no bypass actor, including for the writer App.

Runtime jobs mint the App token for this repository only. Comments, webhooks,
issues, and Projects views are projections or hints; they are not authority.

The policy-owner workflow
`FS-GG/.github/.github/workflows/github-substrate-v2-authority-qualification.yml`
exercises authorized creation, fast-forward CAS, stale-writer rejection,
non-fast-forward rejection, and deletion rejection. The authority repository
receives no App private key. Each qualification run leaves its terminal shard
in the journal as durable evidence.
