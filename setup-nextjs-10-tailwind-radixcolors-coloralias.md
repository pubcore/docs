## Add dark-theme support with radix-ui colors to a fresh tailwind project
* [mui base-ui](https://mui.com/base-ui/getting-started/) 
* radix color + typography by [windy-radix-palette](https://windy-radix-palette.vercel.app)

```zsh
npm i @tailwindcss/typography @radix-ui/colors windy-radix-palette windy-radix-typography &&
{ cat <<'EOF' > tailwind.config.js
/** @type {import('tailwindcss').Config} */
module.exports = {
  content: [
    "./src/pages/**/*.{js,ts,jsx,tsx,mdx}",
    "./src/components/**/*.{js,ts,jsx,tsx,mdx}",
    "./src/app/**/*.{js,ts,jsx,tsx,mdx}",
  ],
  plugins: [
    require("windy-radix-palette"),
    require("@tailwindcss/typography"),
    require("windy-radix-typography"),
  ],
  theme: {
    extend: { colors: { ...generateAliasForRadixColors("primary", "blue") } },
  },
};

function generateAliasForRadixColors(alias, color) {
  const colors = { [alias]: {} };
  for (let i = 1; i <= 12; i++) {
    colors[alias][i] = `hsl(var(--${color}${i}) / <alpha-value>)`;
  }
  return colors;
}
EOF
}
```