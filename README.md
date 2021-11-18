This document describes a list of upcoming/proposed changes to Haskell core/de facto libraries.

* **Ongoing**: the change is already rolled out.
* **Upcoming**: the change is made upstream, and will be released in near future.
* **Planned**: the change has been approved by the committee. Implementation is not necessaryly available

Ongoing: Word8#
----

* Summary: `Word{8,16,32}` are now defined in terms of the corresponding primitive types instead of `Word#`.
* Reason: Small word types used to store `Word#` inside, making record types containing them space-inefficient.
* Since: GHC 9.2
* Breakage: major
* Proposal: https://ghc-proposals.readthedocs.io/en/latest/proposals/0074-small-primitives.html
* Merge request: https://gitlab.haskell.org/ghc/ghc/-/merge_requests/4390
* How to fix: insert `word*ToWord*` (https://hackage.haskell.org/package/ghc-prim-0.8.0/docs/GHC-Prim.html#v:word8ToWord-35-) conditionally (cf. [Fix build on GHC 9.2 by josephcsible · Pull Request #354 · haskell-crypto/cryptonite](https://github.com/haskell-crypto/cryptonite/pull/354/files)) using CPP

Upcoming: Remove `Control.Monad.Trans.List`
----

* Summary: `Control.Monad.Trans.List` is gone
* Reason: `ListT` is a monad only if the underlying monad is commutative; therefore it is not a valid monad transformer.
* ETA: GHC 9.4 / transformers-0.6
* Expected breakage: medium
* Tracking issue: https://hub.darcs.net/ross/transformers/issue/75
* Patch: https://hub.darcs.net/ross/transformers/patch/7c809b6f9db019ed761c971d10c86fa004f60d89
* How to fix: remove instance declarations for transformer's `ListT` (deprecated).
    Users should switch to lawful alternatives such as [pipes](https://hackage.haskell.org/package/pipes-4.3.16/docs/Pipes.html#g:5).

Upcoming: Remove `Control.Monad.Trans.Error`
----

* Summary: `Control.Monad.Trans.Error` is removed in favour of `Control.Monad.Trans.Except`.
* Reason: `ErrorT` imposes an Error constraint, making it difficult to use as a general-purpose `Either` transformer.
* ETA: GHC 9.4 / transformers-0.6
* Expected breakage: medium
* Tracking issue: N/A
* Patch: https://hub.darcs.net/ross/transformers/patch/7c809b6f9db019ed761c971d10c86fa004f60d89
* How to fix: remove instance declarations for `ErrorT` and switch to `ExceptT`

Upcoming: Monomorphise Data.List
----

* Summary: Foldable/Traversable-polymorphic functions in Data.List get specialised to `[]`
* Reason: There's a custom importing `Data.List` unqualified, making additions like `singleton` and `uncons` controversial. Making the list API monomorphic and encouraging to import it qualified make it more consistent with other container modules.
* ETA: GHC 9.6 / base-4.18?
* Proposal: https://groups.google.com/g/haskell-core-libraries/c/q3zHLmzBa5E/m/OrHHKaJNAQAJ?pli=1
* Tracking issue: [#20025](https://gitlab.haskell.org/ghc/ghc/-/issues/20025)
* Merge request: [!5304](https://gitlab.haskell.org/ghc/ghc/-/merge_requests/5304)
https://gitlab.haskell.org/ghc/ghc/-/merge_requests/5107
* Expected breakage: significant
* Relevant warnings: `-Wcompat-unqualified-imports` (implied by -Wcompat)
* How to fix: import `Data.List` qualified or explicitly import functions that don't conflict with Foldable/Traversable variants

Planned: forall becomes a keyword
----

* Summary: 'forall' will become a keyword at the term level
* Reason: Making Type-level and term-level syntax more consistent is a way forward to introduce dependent types
* Proposal: [Visible 'forall' in types of terms](https://github.com/ghc-proposals/ghc-proposals/pull/281)
* ETA: GHC 9.10?
* Tracking issue: N/A
* Merge request: N/A
* Expected breakage: minor (authors of the affected packages were notified)
* Relevant warnings: `-Wforall-identifier` (introduced and enabled by default in GHC 9.4)
* How to fix: Rename identifiers

Planned: remove return from Monad
----

* Summary: return gets removed from the Monad class and becomes a top-level function defined as `return = pure`
* Reason: Now that Monad is a subclass of Applicative, `return` is completely redundant (it doesn't make sense if `return` is not `pure`).
* Proposal: [GHC wiki: monad-of-no-return](https://gitlab.haskell.org/ghc/ghc/-/wikis/proposal/monad-of-no-return), related: [Enable -Wnoncanonical-monad-instances and -Wnoncanonical-monoid-instances by default](https://github.com/ghc-proposals/ghc-proposals/pull/314)
* ETA: Unknown
* Tracking issue: N/A
* Merge request: N/A
* Expected breakage: minor (canonical definition of `return` will be ignored)
* Relevant warnings: `-Wnoncanonical-monad-instances` (enabled by default as of GHC 9.2)
* How to fix: Remove definitions of return (compatible up to GHC 7.10)

Planned: remove mappend from Monoid
----

* Summary: mappend gets removed from the Monoid class and becomes a top-level function defined as `mappend = (<>)`
* Reason: Since `Semigroup` is a superclass of `Monoid`, `mappend` does not have to be defined.
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
* Reason: The only valid semantics of `x /= y` is `not (x == y)`; unlike `compare` vs `(<=)`, there's no room for any performance improvements.
* Proposal: https://github.com/haskell/core-libraries-committee/issues/3
* ETA: Unknown
* Tracking issue: N/A
* Merge request: N/A
* Expected breakage: 131 packages according to the proposal
* How to fix: Remove definitions of `(/=)` (backward-compatible)


Planned: disable StarIsType by default
----

* Summary: `*` will no longer be parsed as a kind of value types (`Type`).
* Reason: Special treatment to `*` complicates the syntax and prevents the use of it as a type operator.
* Proposal: https://github.com/ghc-proposals/ghc-proposals/pull/143
* ETA: Unknown
* Tracking issue: N/A
* Merge request: N/A
* Expected breakage: medium
* Relevant warnings: `-Wstar-is-type`
* How to fix: Replace `*` with `Type` (from `Data.Kind`)

See also
----

* [deprecation-mechanisms/type-class-methods](https://gitlab.haskell.org/ghc/ghc/-/wikis/design/deprecation-mechanisms/type-class-methods) (not implemented; it would be nice to have method deprecation)
* [Warnings and sanity-checking](https://downloads.haskell.org/~ghc/latest/docs/html/users_guide/using-warnings.html)
* [Approved core library proposals](https://github.com/haskell/core-libraries-committee/issues?q=is%3Aissue+is%3Aclosed+label%3Aapproved)
* [Accepted GHC proposals](https://github.com/ghc-proposals/ghc-proposals/pulls?q=is%3Apr+is%3Aclosed+label%3AAccepted)
* [GHC Boot Library Version History](https://gitlab.haskell.org/ghc/ghc/-/wikis/commentary/libraries/version-history): useful for updating version constraints
