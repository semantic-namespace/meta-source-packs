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
