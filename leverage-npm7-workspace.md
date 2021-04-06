## Leverage npm 7 workspaces in context of local development of javascript packages
Following a multi-repo approach with two existing git remote repos, this document
records testing of npm 7 workspaces feature.

#### Prerequisites
1. npm version 7.8 or later installed and used
1. There are at least _two packages_ to work with and one package _depends on_
the other. Workspaces are not needed, for "independent" packages.
1. git client installed and clone of this _two packages_ via ssh works
1. Knowledge: A package that is listed in "workspaces" array is a "workspace"
(So a workspace is _not_ a set of packages)

#### Setup workspaces directory and package.json
First copy one of the packages git remote URIs (SSH), you will be asked for it
later. Create a workspaces directory and change into it:
```
mkdir 'workspaces' && cd workspaces
```
Create a packages json with npm init tool. To answer the question for repo URL,
the copied repo-remote URI of one package can be used. Replace it's
filename-part to "workspaces"
```
npm init
```
Edit the created package.json and define the workspaces array with the two
test candidate packages (without scope):
```
"workspaces": [
  "«a-package»",
  "«a-package-which-depends-on-previous»"
]
```
We can use two little scripts to clone (and cleanup, if needed) by command:  
clone.js
```
'use strict'
//clone all configured workspaces
//It is assumed, that the remotes are siblings of repoBaseUri
const {
    name,
    workspaces,
    repository: { url },
  } = require('./package.json'),
  [repoBaseUri] = url.split('/'),
  git = require('simple-git')({ errors: (err) => err && console.error(err) })

workspaces.forEach((workspace) => {
  git.clone(`${repoBaseUri}/${workspace}.git`)
})
```
clean.js  
```
'use strict'
//cleanup everything, to start from scratch
const { workspaces } = require('./package.json'),
  del = require('del'),
  { exec } = require('child_process')

Promise.all(
  [del('node_modules'), del('package-lock.json')].concat(
    workspaces.map((workspace) => del(workspace))
  )
)
  .then(() => exec('npm i'))
  .catch((e) => console.error(e))
```
Quickly install two required pacakges by this scripts:
```
npm i simple-git del
```
If the repository.url of package.json is correct, and the workspaces exists in
remote repo we can now clone it
```
node clone
```
The clean script may be used to cleanup workspaces dir ```node clean```

#### Prepare cloned packages
1. Check [peer dependencies](https://nodejs.org/es/blog/npm/peer-dependencies/).
Since npm 7 installs peer dependencies, we should make a peer dependency only occur in "peerDependencies" of package.json, and not in "devDependencies" and/or "dependencies".
1. Since npm 7 does check the specified peer dependency versions we should review and adapt it based on [semantic versioning](https://semver.npmjs.com), for instance:

    ```
    Before:
    "peerDependencies": {
      "@material-ui/core": "^4.11.0",
      "react": "^16.8.0",
      "react-dom": "^16.8.0"
    },

    After:
    "peerDependencies": {
      "@material-ui/core": "^4.11",
      "react": "16 - 17",
      "react-dom": "16 - 17"
    },

    In this example all react 16 and 17 versions are
    allowed as peer dependencies;
    ```

1. Check dependencies of workspaces to a workspace: all workspaces with a
"file:" version can be removed (and it's the corresponding file). In this case
no remote package does exist, but since we will use npm workspaces the package
will be found by modules lookup logic.

#### Install the workspaces
In workspaces directory execute npm install:
```
npm i
```

###### Trouble shooting installing the workspaces
Let there be a package @yourScope/a-package with peer dependencies like:
```
"peerDependencies": {
  "@material-ui/core": "^4.11",
  "@material-ui/icons": "~4.9",
  "react": "16 - 17"
}
```

Based on peerDependencies above, the following error occurred :
```
   npm ERR! ERESOLVE unable to resolve dependency tree
   npm ERR!
   npm ERR! While resolving: @yourScope/a-package@0.14.3
4  npm ERR! Found: @types/react@17.0.3
   npm ERR! node_modules/@types/react
6  npm ERR!   peerOptional @types/react@"^16.8.6 || ^17.0.0" from @material-ui/core@4.11.3
   npm ERR!   node_modules/@material-ui/core
   npm ERR!     peer @material-ui/core@"^4.11" from @yourScope/a-package@0.14.3
   npm ERR!     a-package
   npm ERR!     peer @material-ui/core@"^4.0.0" from @material-ui/icons@4.9.1
   npm ERR!     node_modules/@material-ui/icons
   npm ERR!       peer @material-ui/icons@"~4.9" from @yourScope/a-package@0.14.3
   npm ERR!       a-package

   npm ERR!
   npm ERR! Could not resolve dependency:
17 npm ERR! peerOptional @types/react@"^16.8.6" from @material-ui/icons@4.9.1
   npm ERR! node_modules/@material-ui/icons
   npm ERR!   peer @material-ui/icons@"~4.9" from @yourScope/a-package@0.14.3
   npm ERR!   a-packages
   npm ERR!s
   npm ERR! Fix the upstream dependency conflict, or retry
[...]
```
The issue here is the restrictive "tilde" semantic of the peerDependency
"@material-ui/icons": It only allows versions 4.9.x which forces
@types/react@"^16.8.6" (line 17) but installed is @types/react@17.0.3 (line
4 and 6). To fix, relying on semantic versioning a caret (^) can be used to
allow minor version updates.

#### Run watch for all workspaces
Since "watch" scripts are kept running we can not use a sequentially
execution of it (e.g. via "-ws" option). A Run "watch" script for
a workspace looks like: ```npm run watch -w «workspace-name»```.
With option "--if-present" we avoid exit, if there is no "watch" script in a
workspace:
```
npm run --if-present watch -w «workspace-name»
```
If multiple watch scripts executed in context of one shell it is not clear which
output comes from which process, so the output lines should be prefixed with
workspace name. One option to do this via POSIX is to pipe output to "sed":
```
npm run --if-present watch -w «workspace-name» 2>&1 | sed "s/^/«workspace-name»: /"
```
A "watch.js" script, to run such a command for all workspaces defined
in package.json, may look like this:
###### watch.js
```
'use strict'
const { spawn } = require('child_process'),
  { workspaces } = require('./package.json')

workspaces.forEach((workspace) =>
  spawn(
    `npm run --if-present watch -w ${workspace} 2>&1 | sed "s/^/${workspace}: /"`,
    { stdio: 'inherit', shell: true }
  )
)
```
Saved in workspaces directory we can register it as npm script in package.json:
```
"scripts": {
  "watch": "node watch"
}
```
If it is terminated, e.g. by "control-C", all child-processes gets terminated, too. Now, to start watch for all workspaces:
```
npm run watch
```

##### start workspaces where applicable
If there are start scripts in use (bin), there is currently a [bug](https://github.com/npm/cli/issues/2826). So executable scripts are not available in workspace context.

Beside that, this will start workspace:
```
npm start -w a-package
```
