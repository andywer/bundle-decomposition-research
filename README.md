# Research - Decompose bundles

## Goal

Instead of processing one huge bundle as is, it might be less memory and CPU intensive to first decompose the bundle into its chunks, process them separately and then put everything back together.


## How to use this repo

```
$ git clone ...
$ npm install
```

Then run one of the scripts to create a tiny test bundle:
```
# Run one of them:
$ npm run browserify
$ npm run parcel
$ npm run webpack
```

Now check the created bundle in `dist/` and copy its code into <https://astexplorer.net/> to inspect the parsed AST.


## Preliminary result

### Decomposition

Should be possible to do in a more or less bundler-agnostic way by this algorithm:

- Parse AST of bundle (already quite an intensive task, though!)
- Find top-most `CallExpression`
- Check its `arguments` for `ObjectExpression`/`ObjectLiteral` nodes
- Take object with the most properties
  - If this object node contains a lot (> 20 ?) of properties, assume each property is a chunk
  - Those properties' value might be a function (webpack) or an array containing a function (browserify, parcel)

### Re-composition

Should be easy. Simplest approach:

- During decomposition, replace every chunk's code by a placeholder
- Keep the input bundle with the replaced chunks while chunks are processed
- Process bundle skeleton (with chunk placeholders)
- After processing a chunk, replace the chunk placeholder with the processed chunk

### Evaluation

TODO:
- Time to transpile/minify the *whole bundle at once* vs *decomposition, separate processing & re-composition*
- Size of resulting bundles for both approaches
- Test with bundles of different size and different bundlers
