# Workflow ID contract repair (2026-08-29)

## Why this change exists

The B17 fixture contains 40 workflow definitions. Kopal's remote workflow
contract requires `id` to be a `wf_` short ID: the suffix is base62-decoded
to an integer that must fit in 128 bits. Ten human-readable B17 IDs exceeded
that range, so Git Pull found all 40 files but atomically imported none of
them with `int is out of range (need a 128-bit value)`.

This patch changes only those ten top-level `id` values. Workflow folders,
aliases, definitions, action arguments, and the remaining 30 IDs are
unchanged. A read-only search found no references to the ten old IDs outside
their own `id` fields.

## Stable mapping

The replacement is deterministic and reproducible:

1. Compute UUIDv5 with the standard URL namespace and name
   `urn:kopal:b17-workflow:<alias>`.
2. Encode that UUID's 128-bit integer using Kopal's base62 encoding and pad
   the suffix to 22 characters.
3. Prefix the result with `wf_`.

Aliases remain the human- and test-facing identity. The mapping is:

| Folder / alias | Previous `id` | Contract-valid `id` |
| --- | --- | --- |
| `b17-lvl-a-ctrl-success-edge` | `wf_b17LvlACtrlSuccessEdge` | `wf_36ICQRab9ts3D67t7fDjms` |
| `b17-lvl-a-ctrl-transform-rest` | `wf_b17LvlACtrlTransformRest` | `wf_3zhR6PvSJREDaJNFFvMWsh` |
| `b17-lvl-a-table-ioc-roundtrip` | `wf_b17LvlATableIocRoundtrip` | `wf_3i2wDOy7OXxh2375H6qYz3` |
| `b17-lvl-a-trig-case-created` | `wf_b17LvlATrigCaseCreated` | `wf_1xkQEMbgZbqwekrfCI1V0q` |
| `b17-lvl-a-trig-case-updated` | `wf_b17LvlATrigCaseUpdated` | `wf_1WpKJR8fJpE2vm4WhhjJPW` |
| `b17-lvl-a-trig-comment-created` | `wf_b17LvlATrigCommentCreated` | `wf_2uJ0PXCwxa674U8VxeZzeu` |
| `b17-lvl-a-trig-schedule-tick` | `wf_b17LvlATrigScheduleTick` | `wf_5dCs3LiNMeUqIkocGqgW8u` |
| `b17-lvl-a-trig-seed-status-case` | `wf_b17LvlATrigSeedStatusCase` | `wf_1UqdFOvdn7vdmTFVeBCal3` |
| `b17-lvl-a-trig-status-changed` | `wf_b17LvlATrigStatusChanged` | `wf_1ywKljCPDLVojkhTDxG3dw` |
| `b17-lvl-b-hitl-ext-slack-response` | `wf_b17LvlBHitlExtSlackResponse` | `wf_4kRvcbypVLh4NLacfn35xC` |

## Verification required after merge

Run Pull against this commit. The expected first gate is `Found=40` and
`Imported=40` with zero validation diagnostics. If a later DSL or action
contract issue appears, record it separately; it is not an ID-conversion
failure.
