## From scratch

#### Setup a web-app with vite, material-ui, prettier, eslint and TypeScript

- Environment: node v14.17.0, npm v7.14.0, git client on macOS
- «scope» ... a scope/group name

```
cd ~/dev/«scope»
npm init @vitejs/app vite-mui-ts -- --template react-ts

# complete package.json settings
npm init

# setup eslint: check syntax only; module-type: esm; TypeScript: yes;
# config-format: json;
npm i -D eslint
npx eslint --init

# setup linter for react-hooks and TypeScript
npm i -D eslint-plugin-react-hooks @typescript-eslint/parser @typescript-eslint/eslint-plugin
npx -p json json -I -f .eslintrc.json -e \
"this.plugins = [].concat(this.plugins).concat(['@typescript-eslint', 'react-hooks'])"
npx -p json json -I -f .eslintrc.json -e \
"this.extends = [].concat(this.extends).concat('plugin:@typescript-eslint/recommended')"
npx -p json json -I -f .eslintrc.json -e \
"this.rules['react-hooks/rules-of-hooks'] = 'error'"
npx -p json json -I -f .eslintrc.json -e \
"this.rules['react-hooks/exhaustive-deps'] = 'warn'"

# setup prettier
npm i -D prettier eslint-config-prettier eslint-plugin-prettier
npx -p json json -I -f .eslintrc.json -e \
"this.extends = [].concat(this.extends).concat('prettier')"
npx prettier --write .

# setup git repo
git init

# setup pre-commit hooks
npm i -D husky lint-staged
npx husky install
npm set-script prepare "husky install"
npx husky add .husky/pre-commit "npx lint-staged"
npx -p json json -I -f package.json -e "this['lint-staged'] = \
{'*.{js,jsx,ts,tsx}': 'eslint --cache --fix', '**/*':'prettier --write \
--ignore-unknown'}"
echo "\n.husky\n.env" >> .gitignore

git add . && git commit -m "init"
```
