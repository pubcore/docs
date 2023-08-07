## Setup Next.js web-app project and clean it up
From scratch on POSIX systems

### Steps
#### 1. Ideate a name of project, next step will ask for it
Typing in [kebab case](https://en.wikipedia.org/wiki/Letter_case#Kebab_case) is required (e.g. this-is-kebab-case) 

#### 2. Create Next.js project
```zsh
npx create-next-app@latest --ts --eslint --src-dir --app --tailwind --src-dir --import-alias "@/*"
```

#### 3. Execute cleanup script
```zsh
cd "$(\ls -1dt ./*/ | head -n 1)" &&
{ cat <<'EOF' > src/app/page.tsx
export default function Home() {
  return <>The good old &quot;Hello World!&quot;</>;
}
EOF
} &&
rm src/app/favicon.ico &&
{ cat <<'EOF' > src/app/layout.tsx
import "./globals.css";
import type { Metadata } from "next";

export const metadata: Metadata = {
  title: "",
  description: "",
};

export default function RootLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <html lang="en">
      <body>{children}</body>
    </html>
  );
}
EOF
} &&
{ cat <<'EOF' > src/app/globals.css
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
This is a [Next.js](https://nextjs.org/) project bootstrapped with [`create-next-app`](https://github.com/vercel/next.js/tree/canary/packages/create-next-app) and cleaned up by [setup-nextjs-00]()

## development

```bash
npm run dev
```

Open [http://localhost:3000](http://localhost:3000) with your browser to see the result.
EOF
} &&
rm -f ./public/* && 
git add . && git commit -m "initial cleanup"
```

#### 3. Start clean app in development mode 
```zsh
npm run dev
```
