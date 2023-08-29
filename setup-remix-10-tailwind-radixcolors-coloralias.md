## add tailwindcss with radix-ui colors to a fresh nextjs project
radix-ui/colors brings us out-of the box dark-theme support
And setup includes one example of "primary" color alias.

```zsh
npm i @tailwindcss/typography @radix-ui/colors windy-radix-palette windy-radix-typography @tailwindcss/forms &&
{ cat <<'EOF' > remix.config.js
/** @type {import('@remix-run/dev').AppConfig} */
module.exports = {
  ignoredRouteFiles: ["**/.*"],
  // appDirectory: "app",
  // assetsBuildDirectory: "public/build",
  // serverBuildPath: "build/index.js",
  // publicPath: "/build/",
  serverModuleFormat: "cjs",
  future: {
    v2_dev: true,
    v2_errorBoundary: true,
    v2_headers: true,
    v2_meta: true,
    v2_normalizeFormMethod: true,
    v2_routeConvention: true,
  },
  tailwind: true,
};
EOF
} &&
{ cat <<'EOF' > tailwind.config.js
/** @type {import('tailwindcss').Config} */
module.exports = {
  content: ["./app/**/*.{js,ts,jsx,tsx,mdx}"],
  plugins: [
    require("windy-radix-palette"),
    require("@tailwindcss/typography"),
    require("windy-radix-typography"),
    require("@tailwindcss/forms"),
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
} &&
{ cat <<'EOF' > ./app/tailwind.css
@tailwind base;
@tailwind components;
@tailwind utilities;
EOF
} &&
{cat <<'EOF' > ./app/root.tsx
import stylesheet from "~/tailwind.css";
import type { LinksFunction, Request } from "@remix-run/node";
import {
  Links,
  LiveReload,
  Meta,
  Outlet,
  Scripts,
  ScrollRestoration,
  useLoaderData,
} from "@remix-run/react";

import { json } from "@remix-run/node";
import { useChangeLanguage } from "remix-i18next";
import { useTranslation } from "react-i18next";
import i18next from "./i18n/i18next.server";

export const links: LinksFunction = () => [
  { rel: "stylesheet", href: stylesheet },
];

export let loader = async ({ request }: { request: Request }) => {
  let locale = await i18next.getLocale(request);
  return json({ locale });
};

export let handle = {
  i18n: "common",
};

export default function App() {
  let { locale } = useLoaderData();
  let { i18n } = useTranslation();

  useChangeLanguage(locale);

  return (
    <html lang={locale} dir={i18n.dir()}>
      <head>
        <meta charSet="utf-8" />
        <meta name="viewport" content="width=device-width,initial-scale=1" />
        <Meta />
        <Links />
      </head>
      <body>
        <Outlet />
        <ScrollRestoration />
        <Scripts />
        <LiveReload />
      </body>
    </html>
  );
}
EOF
} &&
npm rm @remix-run/css-bundle &&
mkdir -p ./app/components &&
{ cat <<'EOF' > ./app/components/Typography.tsx
export default function Typography({
  children,
  className = "",
}: {
  children: React.ReactNode;
  className?: string;
}) {
  return (
    <div className={`prose lg:prose-lg w-full prose-slate ${className}`}>
      {children}
    </div>
  );
}
EOF
} &&
{ cat <<'EOF' > ./app/routes/_index.tsx
import type { V2_MetaFunction } from "@remix-run/node";
import { useTranslation } from "react-i18next";
import Typography from "~/components/Typography";

export const meta: V2_MetaFunction = () => {
  return [{ title: "" }, { name: "description", content: "" }];
};

export default function Index() {
  const { t } = useTranslation();
  return (
    <div className="box-border min-h-screen bg-primary-1">
      <Typography>
        <h1>{t("hello")}</h1>
      </Typography>
    </div>
  );
}
EOF
}
``````