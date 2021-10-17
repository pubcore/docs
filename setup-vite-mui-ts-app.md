## From scratch

#### Setup a web-app with vite, material-ui, prettier, eslint and TypeScript

- Environment: node v14.17.6, npm v7.19.1, git client on macOS
- «scope» ... a scope/group name
- «name» ... app name in kebab-case

```
cd ~/dev/«scope»
npm init vite «name» -- --template react-ts

# complete package.json settings
npm init

# setup eslint: check syntax only; module-type: esm; TypeScript: yes;
# config-format: json;
npm i -D eslint && \
npx eslint --init

# setup linter for react-hooks, TypeScript and vite
npm i -D eslint-plugin-react-hooks @typescript-eslint/parser @typescript-eslint/eslint-plugin && \
npx -p json json -I -f .eslintrc.json -e \
"this.plugins = [].concat(this.plugins).concat(['@typescript-eslint', 'react-hooks'])" && \
npx -p json json -I -f .eslintrc.json -e \
"this.extends = [].concat(this.extends).concat('plugin:@typescript-eslint/recommended')" && \
npx -p json json -I -f .eslintrc.json -e \
"this.rules['react-hooks/rules-of-hooks'] = 'error'" && \
npx -p json json -I -f .eslintrc.json -e \
"this.rules['react-hooks/exhaustive-deps'] = 'warn'" && \
npx -p json json -I -f .eslintrc.json -e \
"this.rules['react/react-in-jsx-scope'] = 'off'"

# setup prettier
npm i -D prettier eslint-config-prettier eslint-plugin-prettier && \
npx -p json json -I -f .eslintrc.json -e \
"this.extends = [].concat(this.extends).concat('prettier')" && \
echo "tabWidth: 2" > .prettierrc.yaml && \
npx prettier --write .

# setup git repo
git init

# setup pre-commit hooks
npm i -D husky lint-staged && \
npx husky install && \
npm set-script prepare "husky install" && \
npx husky add .husky/pre-commit "npx lint-staged" && \
npx -p json json -I -f package.json -e "this['lint-staged'] = \
{'*.{js,jsx,ts,tsx}': 'eslint --cache --fix', '**/*':'prettier --write \ 
--ignore-unknown'}" && \
echo "\n.husky\n.env\n.eslintcache" >> .gitignore

git add . && git commit -m "init"
```
