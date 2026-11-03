# meta-source-packs

> **Experimental.** The packs' options and what they read may change.

Packs for [meta-source](https://github.com/semantic-namespace/meta-source): each reads one Clojure library's own registry,
where it is, as data. clojure.spec and malli, Pathom, integrant, aero,
re-frame, reitit, Datascript, tilakone, Overarch and grain.

```clojure
;; meta-source.edn
{:deps {io.github.semantic-namespace/meta-source-packs
        {:git/url "https://github.com/semantic-namespace/meta-source-packs" :git/sha "<sha>"}}
 :packs {:integrant {:configs ["resources"]}
         :reitit {}}}
```

meta-source puts this library on the classpath and finds its packs through
its `meta-source/packs.edn`; a pack is loaded only when `:packs` enables it.

- [docs/packs.md](docs/packs.md): each pack, its options, what it checks
- [docs/pattern.md](docs/pattern.md): what a project gets when it follows a
  library's conventions exactly (integrant and reitit), and
  [examples/billing](examples/billing), an app written that way
- [review/](review/README.md): on [sdiff](https://github.com/semantic-namespace/diff)'s
  review page, the findings a change introduces and those it fixes

## Adopting them in an existing app

![Adopting meta-source in an existing app, in Emacs, one pack at a time](docs/images/meta-source-adoption.gif)

An app with an integrant system, specs and Pathom resolvers, open in Emacs,
adopting meta-source one line at a time (every edit is typed in the
recording):

1. **`{:packs {:integrant {:configs ["resources"]}}}`**: a ref to a
   component no config defines, underlined in `system.edn`.
2. **`:pathom {}`**: resolver inputs nothing produces.
3. **`:spec {}`, and one fact in EDN, `{:user/email {:data/privacy :pii}}`**:
   `C-c .` on `:user/email` puts three sources together:

   ```
   :user/email    data key

   spec       (s/and string? #(re-find #"@" %1))    src/shop/spec.clj:5
   :data/privacy  :pii
   member of
     :shop.spec/user    src/shop/spec.clj:12

   produced by
     :shop.resolvers/user-by-id    src/shop/resolvers.clj:11

   consumed by
     :shop.resolvers/user-by-email    src/shop/resolvers.clj:4
   ```
4. **Nothing**: the `s/fdef` the app already has for `place-order` is its
   contract, so the code reading `:order/discount`, which the fdef doesn't
   declare, is underlined. A function with no fdef can opt in with a meta
   key and a contract in EDN.

What it cost, by `git status`: a `meta-source.edn` and a `registry/`
directory. No line of code changed.

### On a re-frame app

![A re-frame app adopting meta-source in Emacs](docs/images/meta-source-re-frame.gif)

1. **`{:packs {:re-frame {:provided [:http-xhrio]}}}`**: an `:on-failure`
   callback to an event nothing registers, and a subscription with a typo.
   re-frame would only log them, when they run.
2. **`C-c .` on `::events/checkout`**: where it is registered and
   dispatched from, what it dispatches, the effects it returns, the keys it
   writes.
3. **`:spec {}` and `{:user/email {:data/privacy :pii}}`**: `C-c .` on
   `:user/email` gives its spec, that it is PII, the event that writes it and
   the subscription that reads it.
4. **The typo fixed**: the underline goes.

## Working on them

```sh
bb smoke                   # every pack's checks; bb smoke reitit, one suite
bb meta-source check --brief --root examples/billing
bb jvm-check               # the packs load on the JVM, and build babashka's index
bb pathom-snapshot-check   # the shop fixture's Pathom snapshot, from real Pathom
bb system-snapshot-check   # the integrant and reitit snapshots, from the real libraries
cd review && bb test
```

## License

MIT
