
```zsh
npx create-expo-app --template &&
npx expo install eslint eslint-config-prettier eslint-config-universe eslint-plugin-react-hooks prettier lint-staged husky &&
npm pkg set scripts.prepare="husky install" &&
npm pkg set scripts.lint="npx eslint" &&
npx husky install &&
npx husky add .husky/pre-commit "npm run lint" &&
{ cat <<'EOF' > .eslintrc.json 
{
  "extends": ["universe", "plugin:react-hooks/recommended", "prettier"]
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