# pubcore npm usage

## auto select registries on npm publish based on package's scope
(if it should _not_ published to registry.npmjs.org)  
see
https://docs.npmjs.com/misc/scope#associating-a-scope-with-a-registry

## initial publish
	make sure two factor authentication is enabled

  make sure project is named `@pubcore/<project name>` in `package.json` or do it so with (please check that no `pubcore-` appears in `<project-name>`)
  ```
  npm init --scope=pubcore
  ```


  login to your npm account
  ```
  npm login
  ```
  publish as your personal npm packages
  ```
  npm publish --access public
  ```
  grant access for pubcore organization
  ```
  npm access grant read-write pubcore:pubcore
  ```
  ### hints

  * you can unpublish every package for 24h after initial publish `npm unpublish --force`.
  after 24h removing a package is only possible by contacting npm support
  * after unpublishing a package, you cannot create a package with the same name within the next 24h.
