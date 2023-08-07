## Setup Next.js web-app project and clean it up
From scratch on POSIX systems

### Steps
#### 1. Ideate a name of project, next step will ask for it
Typing in [kebab case](https://en.wikipedia.org/wiki/Letter_case#Kebab_case) is required (e.g. this-is-kebab-case) 

#### 2. Create Next.js project
```zsh
npx create-next-app@latest --ts --eslint --src-dir --pages --tailwind --src-dir --import-alias "@/*"
```

#### 3. Execute cleanup script
```zsh
cd "$(\ls -1dt ./*/ | head -n 1)" &&
rm -rf ./public/* &&
rm -rf ./src/pages/api/* && 
{ cat <<'EOF' > src/pages/index.tsx
export default function Home() {
  return <>The good old &quot;Hello World!&quot;</>;
}
EOF
} &&
{ cat <<'EOF' > src/styles/globals.css
@tailwind base;
@tailwind components;
@tailwind utilities;
EOF
} &&
{ cat <<'EOF' > tailwind.config.js
/** @type {import('tailwindcss').Config} */
module.exports = {
  content: [
    "./src/pages/**/*.{js,ts,jsx,tsx,mdx}",
    "./src/components/**/*.{js,ts,jsx,tsx,mdx}",
    "./src/app/**/*.{js,ts,jsx,tsx,mdx}",
  ]
};
EOF
} &&
{ cat <<'EOF' > README.md
This is a [Next.js](https://nextjs.org/) project bootstrapped with [`create-next-app`](https://github.com/vercel/next.js/tree/canary/packages/create-next-app) and cleaned up by [setup-nextjs-00-pages-router.md]()

## development

```bash
npm run dev
```

Open [http://localhost:3000](http://localhost:3000) with your browser to see the result.
EOF
} &&
git add . && git commit -m "initial cleanup"
```