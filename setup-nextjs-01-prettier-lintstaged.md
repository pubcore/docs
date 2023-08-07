## Add prettier, lint-staged and pre-commit hook to just created nextjs project
From scratch on POSIX system

#### 1. Change into just created project directory 
```zsh
cd «project-name»
```
#### 2. install/configure pre-commit hooks with husky, prettier [^1]
```zsh
npm i eslint-config-prettier prettier lint-staged husky && 
npm pkg set scripts.prepare="husky install" &&
npx husky install &&
npx husky add .husky/pre-commit "npm run lint" &&
{ cat <<'EOF' > .eslintrc.json 
{
  "extends": ["next", "prettier"]
}
EOF
} &&
{ cat <<'EOF' > .lintstagedrc.js
const path = require("path"); 
const buildEslintCommand = (filenames) => 
  `next lint --fix --file ${filenames 
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

#### 4. Start example app in development mode 
```zsh
npm run dev
```

### Remarks
[^1] There are no "devDependencies" following nextjs ^13 initial package style

### References
[nextjs config](https://nextjs.org/docs/app/building-your-application/configuring/eslint#usage-with-other-tools)