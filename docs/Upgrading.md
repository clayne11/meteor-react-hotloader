## From 1.3.1_1 or .fast releases

* We now use React-Hot-Loader v3.
* We now rely on Meteor's official `.babelrc` support (from 1.3.3?) XXX
* We can now upgrade `hot` and the new `hot-build` independently of `ecmascript-hot`.

This project is now a general hot loading project, and includes sample instructions
for React in the [React Hotloading docs](./React_Hotloading.md).  However, the steps
below will help you upgrade from an existing installation to the new setup described
there.

### Remove the old setup

1. `npm rm --save-dev babel-plugin-react-transform react-transform-hmr react-transform-catch-errors`
1. Remove the entire `react-transform` section from your `client/.babelrc` env block (or delete the file completely if you never modified it)
1. Remove your `package.json`'s `ecmascript-hot` section completely, it's no longer used.

### Add the new setup

1. `npm install --save-dev react-hot-loader@^3.0.0-alpha.12` (check latest release)
1. In your *project root* `.babelrc`, make sure you have `{ "plugins": ["react-hot-loader/babel"] }`.
1. Modify your main client entry point / wherever you mount your root to resemble:

  ```js
import { AppContainer } from 'react-hot-loader';  // <-- add this line
import Root from './containers/Root';  // example; wherever you keep your main component

// Wherever you do this, probably in Meteor.startup()
ReactDOM.render(
  <AppContainer
    component={Root}
    props={{ store }}     {/* if e.g. you're using redux */}
  />,
  document.getElementById('root')
);

// This section is new, the references to "containers/Root" should match your import statement
if (module.hot) {
  module.hot.accept('./containers/Root', () => {
    render(
      <AppContainer
        component={require('./containers/Root').default}
        props={{ store }}
      />,
      document.getElementById('root')
    );
  });
}
```

1. You need to `import 'react-hot-loader/patch';` *before* importing `React`.  If you have a *single* client entry-point and get everything else from the `/imports` directory, just add this as your first line.  If you still use a "regular" client directory (with more than 1 file), try place just this line in a file called `client/_patchReact.js` (or possibly in `client/lib` if you use react from within that directory).

There's an (incredibly convoluted) example at https://github.com/gadicc/meteor-react-hotloader/.tree/master/demo-rhl3.
