# vNEXT

* Please see the [Upgrading](docs/Upgrading.md) docs.

* Major refactor and support for arbitrary build plugins.  Plugin authors
  are invited to read the [Build Plugins](docs/Build_Plugins.md) docs.
  This also means that we no longer need to make a new release for any core
  changs in `babel-compiler`, since we now use/wrap the currently installed
  version.  We still use a replacement `ecmascript-hot`, but `babel-compiler`
  can be upgraded independently, as can `gadicc:hot` and `gadicc:hot-builder`.

* Removed all transformStateless code, new react hotloader doesn't need it.

* Like in `fast.14`, we now have *basic* HMR support for non-react hotloaders.
  We provide a partial `hot.accept()` implementation only, and only on the
  client, for now.

# 1.3.2-fast.14 (2016-04-20)

* Much faster reloads (for shared files too) by running an external
  "Accelerator" forked process.  You can read a bit more about this in the
  update HOW THIS WORKS section of the README (#26).

* *Basic* HMR support for non-react hotloaders.  We provide a partial
  `hot.accept()` implementation only, and only on the client - for now.

* Fixed `Failed to parse SourceMap: http://localhost:3000/packages/undefined`
  by being more careful to clear the cache from an older broken version (#21).

* Ability to customize what files are passed to `transformStateless` with
  a likewise named field in the `package.json`'s `ecmascript-hot` key (#18).
  See the README for more info, this allows you to use functional stateless
  components in `.js` files too, for example.

* More useful warning when a compile error occured on a transformed file.

* Improved stateless matching, don't get confused by single line functions
  next to functional stateless components.

* Stateless transform: only match on `const UppercaseIdentifier` which
  reduces the risk of false positives, thanks @clayne11!  (our first PR!)

* hot-client: more useful debug info for failures, more resilliency in
  maintaining a connection to the server.

* Use checkNpmVersions to ensure that `babel-preset-meteor` is up-to-date,
  now that we don't include it directly.

* Fixes for cases where, if Meteor died unexpectantly, the accelerator
  process would live on forever and prevent restarts (#26)

* Fixes for running in `meteor test` and `meteor test-packages`
  environments (#27, #37).  Thanks also to @clayne11.

* Default to setting BABEL_ENV="production" in test environment (to avoid
  react HMR stuff), overridable in `package.json` (see README) - #38.

# v1.3.1_1 (2016-04-04)

* For Meteor 1.3.1.

# v1.3.0_5 (2016-04-02)

* We now survive server code changes, so this now works for both
  `imports` (that aren't beneath a `client` subdirectory) and
  **SSR** code from mixed / "both" directories with shared code.  (#17)

* We no longer rely on Mongo for communications, so we now require an
  extra port.  By default this is Meteor's port + 2 (i.e., right after
  mongo), but you can override it with the `HOT_PORT` environment variable.

* Fixes the "non-stop reload" bug where changes to files in `public`
  (and I guess, other shared directories with lots of files) could cause
  a lot of restarts.  (#22)

# v1.3.0_4 (2016-04-01)

* **Crucial fix** for the infinite loop affecting majority of deployments.
  My sincere apologies for this slipping through.

* Now that Meteor 1.3 is out, we're using proper versioning.  You no longer
  need to specify the exact version to use, just put `gadicc:ecmascript-hot`
  in your `packages` file and updates will arrive with `meteor update`.

* Fix `Uncaught TypeError: Cannot read property 'c' of undefined` from
  first load (since last release).

* Disable (broken) sourcemap for modules-runtime.js.

* ~~Transform stateless components in `.js` files now too.~~ As this was
  a critical update, this potentially unstable feature has been delayed
  until the next release.

# v1.3.0-2 (2016-03-30)

* Support for a `client/.babelrc` and `server/.clientrc` (that we recommend
  extends your root `.babelrc`).  See the end of the README for the new
  recommendations.  You should modify your `.babelrc` appropriately.  Amongst
  other things, this allows us to use hotloading on the client without
  breaking flowrouter-ssr on the server.

* Fix browser caching issues by modifying boilerplate HTML and substituting
  module-runtime's hot with module-runtime-hot's hash.

* No need to "eagerly evaluate" replaced modules, so don't do that.

* Package support :)  See README.

* Fix problems with escaping single inverted commas (`\\'`) in hot bundle.

* Fix some further newline munging which broke scripts where the last line
  had a comment.

# v1.3.0-1 (2016-03-28)

* Works with Meteor 1.3 (final) and `rc.4+`

* Speed increase by re-using `inputfile.getSourceHash()` instead of hashing
  twice.

* Correctly hash on `BABEL_ENV` too (if it exists).

* Use `127.0.0.1` instead of `localhost` for mongo url.

* Use Meteor's `METEOR-PORT` file to figure out the mongo port.

# v0.0.14-rc.12 (2016-03-26)

* Works with Meteor 1.3 `rc.4 - rc.12`

* Fixed a bunch of issues that broke production and bundling:
  Don't transform stateless components, don't connect to Mongo,
  don't replace modules-runtime, don't block HCPs, and lastly,
  use NODE_ENV as part of the babel cache hash.

* Use JSON5 for parsing `.babelrc`, just like babel does.
  This allows for comments, etc.

# v0.0.13-rc.10 (2016-03-24)

* Works with Meteor 1.3 `rc.4 - rc.10`.

* No longer auto-populate babelrc `presets` with `meteor`, to allow other
  plugins to be loaded before those in the preset.  If you don't already
  have a .babelrc, one will be created for you.  If you do, ensure you
  have `{ "presets": "meteor" }`.  Also,
  `npm install --save-dev babel-preset-meteor`.

# v0.0.12-rc.8 (2016-03-23)

* Fix transformStateless() from munging newlines and breaking some code.
* Use rawCollection() for serving hot.js via WebApp (without a Fiber).
* Slight change on file match algorithm.
* On reload, keep hot bundles from last 10s -- may help in future with server
  restarts.

# v0.0.10-rc.4 (2016-03-20)

* Release for Meteor `1.3-rc.4`

# v0.0.10-rc.3 (2016-03-19)

* Release for Meteor `1.3-rc.3`

# v00.0.9-rc.2 (2016-03-18)

* `.babelrc` support!  See the README for upgrading.

* `react-transform-catch-error` via `.babelrc`.

* Release for Meteor `1.3-rc.2`

* Remove slightly annoying notice on server "Creating a bundle..."
* Be slightly stricter in identifying stateless components, we now look for
  `/return\s+\(\s*\</` to match (i.e. we added a `<` after the `return (`.

# v0.0.7-beta.12 (2016-03-09)

* Accept function components that contain code (before we just accepted functions
  that mapped to jsx).  See README for the new example.

# v0.0.6-beta.12 (2016-03-08)

* Even more reliable HCP strategy.  Fixes broken CSS hotloading (#9) and all
  the cases which require a real reload (new files, etc) which we were accidently
  blocking.

# v0.0.5-beta.12 (2016-03-06)

* Release for Meteor `1.3-beta.12`.

# v0.0.5-beta.11 (2016-03-06)

* New and more reliable strategy to block HCP.  Regular page reload (via
  ctrl-R) now works how we expect (it's always up to date, no more stored
  patches) and `hot.reload()` is no longer required.

* Relax path restrictions a bit.  Match on anything that contains  'client'
  and not 'test' in the path / filename.

# v0.0.4-beta.11 (2016-02-22)

* `beta.11` support.

# v0.0.3-modules.7 (2016-02-12)

* Code to force a client refresh, used for changes we can't handle.

* Different method for overriding core modules-runtime.

# v0.0.2-modules.7 (2016-02-11)

* Correctly resolve relationships when importing from root paths
  (`/something`) and relative paths involving parents
  (`../../something).  Relative paths in the same directory
  (`./something) were all that was supported previously (#4).

# v0.0.1-modules.7 (2016-02-09) - first Atmosphere release

* Relax restriction on stateless components to not be in a `components`
  directory, let's hope it's ok.

* Intercept requests to `/packages/modules-runtime.js` and avoid need to
  replace entire module (until
  [install#86](https://github.com/benjamn/install/pull/6) is accepted).
