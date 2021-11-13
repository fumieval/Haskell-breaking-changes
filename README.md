This document describes a list of upcoming/proposed changes to Haskell core/de facto libraries.

Upcoming: Remove `Control.Monad.Trans.List`
----

* Summary: `Control.Monad.Trans.List` is gone because it's not a monad transformer.
* ETA: GHC 9.4 / transformers-0.6
* Expected breakage: medium
* Tracking issue: https://hub.darcs.net/ross/transformers/issue/75
* Patch: https://hub.darcs.net/ross/transformers/patch/7c809b6f9db019ed761c971d10c86fa004f60d89
* How to fix: remove instance declarations for transformer's `ListT` (deprecated).
    Users should switch to lawful alternatives such as [pipes](https://hackage.haskell.org/package/pipes-4.3.16/docs/Pipes.html#g:5).

Upcoming: Remove `Control.Monad.Trans.Error`
----

* Summary: `Control.Monad.Trans.Error` is removed in favour of `Control.Monad.Trans.Except`.
* ETA: GHC 9.4 / transformers-0.6
* Expected breakage: medium
* Tracking issue: N/A
* Patch: https://hub.darcs.net/ross/transformers/patch/7c809b6f9db019ed761c971d10c86fa004f60d89
* How to fix: remove instance declarations for `ErrorT` and switch to `ExceptT`

Upcoming: Monomorphise Data.List
----

* Summary: Foldable/Traversable-polymorphic functions in Data.List get specialised to `[]`
* ETA: GHC 9.6 / base-4.18?
* Proposal: https://groups.google.com/g/haskell-core-libraries/c/q3zHLmzBa5E/m/OrHHKaJNAQAJ?pli=1
* Tracking issue: [#20025](https://gitlab.haskell.org/ghc/ghc/-/issues/20025)
* Merge request: [!5304](https://gitlab.haskell.org/ghc/ghc/-/merge_requests/5304)
https://gitlab.haskell.org/ghc/ghc/-/merge_requests/5107
* Expected breakage: significant
* Relevant warnings: `-Wcompat-unqualified-imports` (implied by -Wcompat)
* How to fix: import `Data.List` qualified or explicitly import functions that don't conflict with Foldable/Traversable variants

Planned: remove return from Monad
----

* Summary: return gets removed from the Monad class and becomes a top-level function defined as `return = pure`
* Proposal: [GHC wiki: monad-of-no-return](https://gitlab.haskell.org/ghc/ghc/-/wikis/proposal/monad-of-no-return), related: [Enable -Wnoncanonical-monad-instances and -Wnoncanonical-monoid-instances by default](https://github.com/ghc-proposals/ghc-proposals/pull/314)
* ETA: Unknown
* Tracking issue: N/A
* Merge request: N/A
* Expected breakage: minor (canonical definition of `return` will be ignored)
* Relevant warnings: `-Wnoncanonical-monad-instances` (enabled by default as of GHC 9.2)
* How to fix: Remove definitions of return (compatible up to GHC 7.10)

Planned: remove mappend from Monoid
----

* Summary: return gets removed from the Monad class and becomes a top-level function defined as `mappend = (<>)`
* Proposal: [GHC wiki: semigroup-monoid](https://gitlab.haskell.org/ghc/ghc/-/wikis/proposal/semigroup-monoid), related: [Enable -Wnoncanonical-monad-instances and -Wnoncanonical-monoid-instances by default](https://github.com/ghc-proposals/ghc-proposals/pull/314)
* ETA: Unknown
* Tracking issue: N/A
* Merge request: N/A
* Expected breakage: major (without an update to the warning)
* Relevant warnings: `-Wnoncanonical-monoid-instances` (enabled by default as of GHC 9.2)
* How to fix: Remove definitions of mappend (compatible up to GHC 8.4)
* TODO: make `-Wnoncanonical-monoid-instances` warn manual declarations of `mappend`

Planned: remove (/=) from the the Eq class
----

* Summary: (/=) gets removed from the Eq class and becomes a top-level function defined as `a /= b = not (a == b)`
* Proposal: https://github.com/haskell/core-libraries-committee/issues/3
* ETA: Unknown
* Tracking issue: N/A
* Merge request: N/A
* Expected breakage: 131 packages according to the proposal
* How to fix: Remove definitions of `(/=)` (backward-compatible)


Planned: disable StarIsType by default
----

* Summary: `*` will no longer be parsed as a kind of value types (`Type`).
* Proposal: https://github.com/ghc-proposals/ghc-proposals/pull/143
* ETA: Unknown
* Tracking issue: N/A
* Merge request: N/A
* Expected breakage: medium
* Relevant warnings: `-Wstar-is-type`
* How to fix: Replace `*` with `Type` (from `Data.Kind`)

See also
----

* [deprecation-mechanisms/type-class-methods](https://gitlab.haskell.org/ghc/ghc/-/wikis/design/deprecation-mechanisms/type-class-methods)
* [Warnings and sanity-checking](https://downloads.haskell.org/~ghc/latest/docs/html/users_guide/using-warnings.html)
* [Approved core library proposals](https://github.com/haskell/core-libraries-committee/issues?q=is%3Aissue+is%3Aclosed+label%3Aapproved)
* [Accepted GHC proposals](https://github.com/ghc-proposals/ghc-proposals/pulls?q=is%3Apr+is%3Aclosed+label%3AAccepted)
* [GHC Boot Library Version History](https://gitlab.haskell.org/ghc/ghc/-/wikis/commentary/libraries/version-history): useful for updating version constraints