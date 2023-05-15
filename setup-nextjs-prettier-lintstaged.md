## Setup nextjs web-app project with prettier, lint-staged and pre-commit hook
From scratch on unix-like system

### Steps
#### 1. Ideate name of project, [kebab case](https://en.wikipedia.org/wiki/Letter_case#Kebab_case) is required (e.g. this-is-kebab-case) 

#### 2. Create nextjs project with default settings
```
npx create-next-app@latest --no-tailwind --ts --eslint --src-dir --app
```
#### 3. Change into created project directory execute commands below. [^1]
```
npm i eslint-config-prettier prettier lint-staged husky && 
npm pkg set scripts.dev="next dev --turbo"
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
git add . && git commit -m "init" 
```

#### 4. Start example app in development mode 
```
npm run dev
```

### Remarks
[^1] There are no "devDependencies" following nextjs ^13 initial package style

### References
[nextjs config](https://nextjs.org/docs/app/building-your-application/configuring/eslint#usage-with-other-tools)