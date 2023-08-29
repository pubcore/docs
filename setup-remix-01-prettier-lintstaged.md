## Add prettier, lint-staged and pre-commit hook to just created remix project
From scratch on POSIX system

#### 1. Change into just created project directory 
```zsh
cd «project-name»
```
#### 2. install/configure pre-commit hooks with husky, prettier [^1]
```zsh
git init --initial-branch=main && git add . && git commit -m "init" &&
npm i -D eslint-config-prettier prettier lint-staged husky && 
npm pkg set scripts.lint-staged="{\"*.{js,jsx,ts,tsx}\": \"eslint --cache --fix\", \"*.{js,jsx,ts,tsx,md,css}\": \"prettier --write\"}" &&
npm pkg set scripts.prepare="husky install" &&
npx husky install &&
npx husky add .husky/pre-commit "npx lint-staged" &&
{ cat <<'EOF' > .eslintrc.js 
/** @type {import('eslint').Linter.Config} */
module.exports = {
  extends: ["@remix-run/eslint-config", "@remix-run/eslint-config/node", "prettier"],
};
EOF
} &&
{ cat <<'EOF' > .lintstagedrc.js
const path = require("path"); 
const buildEslintCommand = (filenames) => 
  `npx eslint --fix --file ${filenames 
    .map((f) => path.relative(process.cwd(), f)) 
    .join(" --file ")}`;

module.exports = { 
  "*.{js,jsx,ts,tsx}": [buildEslintCommand],
};
EOF
} &&
echo '{}' > .prettierrc.json &&
git add . && git commit -m "init prettier, lint-staged, husky"
```