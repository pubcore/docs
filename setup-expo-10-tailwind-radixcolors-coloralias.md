# Add tailwind to fresh expo project
See https://www.nativewind.dev/quick-starts/expo

Issue: tailwindcss must be installed in version 3.3.2

```zsh
npx expo install nativewind &&
npm i -D tailwindcss@3.3.2 &&
{cat <<EOF > ./app.d.ts
/// <reference types="nativewind/types" />
EOF
} &&
{cat <<'EOF' > ./tailwind.config.js   
module.exports = {
  content: [],
  content: ["./app/**/*.{js,jsx,ts,tsx}", "./components/**/*.{js,jsx,ts,tsx}"],
  plugins: [],
};
EOF
} &&
{cat <<EOF > ./global.css
@tailwind base;
@tailwind components;
@tailwind utilities;
EOF
} &&    
{cat <<EOF > ./postcss.config.js
module.exports = {
  plugins: {
    tailwindcss: {},
  },
};
EOF
} &&
{cat <<EOF > babel.config.js
module.exports = function (api) {
  api.cache(true);
  return {
    presets: ['babel-preset-expo'],
    plugins: [
      // Required for expo-router
      'expo-router/babel',
      'nativewind/babel',
    ],
  };
};
EOF
} &&
echo -e "import \"../global.css\";\n$(cat ./app/_layout.tsx)" > ./app/_layout.tsx
```