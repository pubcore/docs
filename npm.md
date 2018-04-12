# pubcore npm usage

## initial publish

  * make sure project is named `@pubcore/<project name>` in `package.json` or do it so with
  ```
  npm init --scope=pubcore
  ```
  * login to your npm account
  ```
  npm login
  ```
  * publish as your personal npm packages
  ```
  npm publish --access public
  ```
  * grant access for pubcore organization
  ```
  npm access grant read-write pubcore:pubcore
  ```
