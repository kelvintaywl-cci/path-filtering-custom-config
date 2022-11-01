# path-filtering-custom-config

Showcase how to run different continued config, with path-filtering.

Please see the .circleci/config.yml file for details.

@circle-makotom has a flexible solution with an Orb and a sample here:

- Orb: https://github.com/circle-makotom-orbs/config-splitting
- Sample: https://github.com/circle-makotom/circle-combine-split-config

This is a simpler approach to get around the path-filtering Orb.
Specifically, we want to continue using the `mapping` configuration from path-filtering, but allow for a specific contunued config.

Please see https://github.com/kelvintaywl-cci/path-filtering-custom-config/pull/1 for a sample PR.


## Notes

Path-filtering Orb (as of v0.1.3) is basic and allows us to create mappings like:

```yaml
      mapping: |
        foo/.* config ".circleci/continue-foo.yml"
        foo/.* message "kungFOOfighting"
        bar/.* config ".circleci/continue-bar.yml"
        bar/.* message "BARnone"
```

However, the following types are invalid:

```yaml
      # comments cannot be added; they are evaluated as an entry
      mapping: |
        # this comment is counted as an mapping entry
        bar/.* message "BARnone"
```

```yaml
      # no newlines can be added; fix in https://github.com/CircleCI-Public/path-filtering-orb/pull/22
      mapping: |
        foo/.* message "kungFOOfighting"

        bar/.* message "BARnone"
```

```yaml
      # no spaces in values, otherwise it counts as another item (e.g., 5 items in the line below)
      mapping: |
        foo/.* message "kung FOO fighting"
```

This is because the mapping function in its Python script](https://github.com/CircleCI-Public/path-filtering-orb/blob/9b229fa9b2b3974000a8ed53814dab609128b745/src/scripts/create-parameters.py#L57-L77) requires that:

1. Each line must have 3 items, separated by spaces (` `)
2. There cannot be spaces inside the item's value, otherwise it counts as another item
