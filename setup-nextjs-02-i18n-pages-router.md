## Add i18n support to fresh Next.js 13 installation with pages router

```zsh
npm i next-intl@3.0.0-beta.9 &&
mkdir -p ./src/i18n/locales/en &&
{cat <<'EOF' > ./src/i18n/locales/en/global.json
{
  "title": "The good old \"Hello World!\""
}
EOF
} &&
{cat <<'EOF' > ./src/i18n/getMessages.ts
import { GetStaticPropsContext } from "next";

export async function getMessages(ctxt: GetStaticPropsContext) {
  const defaultMessages = (
    await import(`./locales/${ctxt.defaultLocale}/global.json`)
  ).default;
  const messages =
    ctxt.locale && ctxt.defaultLocale !== ctxt.locale
      ? (await import(`./locales/${ctxt.locale}/global.json`)).default
      : {};
  return { ...defaultMessages, ...messages };
}
EOF
} &&
{cat <<'EOF' > ./src/pages/_app.tsx
import "@/styles/globals.css";
import { NextIntlProvider } from "next-intl";
import type { AppProps } from "next/app";

export default function App({ Component, pageProps }: AppProps) {
  return (
    <NextIntlProvider
      timeZone="Europe/Berlin"
      messages={pageProps.messages}
      defaultTranslationValues={{ b: (v) => <b>{v}</b> }}
    >
      {
        <Component {...pageProps} />
      }
    </NextIntlProvider>
  );
}
EOF
} &&
{cat <<'EOF' > ./src/pages/404.tsx
import { getMessages } from "@/i18n/getMessages";
import { NextPageContext } from "next";

export default function Custom404() {
  return (
    <>
        <h1>404 - Page Not Found</h1>
    </>
  );
}

export const getStaticProps = async (context: NextPageContext) => ({
  props: {
    messages: await getMessages(context),
  },
});
EOF
} &&
{cat <<'EOF' > ./src/pages/500.tsx
import { getMessages } from "@/i18n/getMessages";
import { NextPageContext } from "next";

export default function Custom500() {
  return (
    <>
        <h1>500 - Server-side error occurred</h1>
    </>
  );
}

export const getStaticProps = async (context: NextPageContext) => ({
  props: {
    messages: await getMessages(context),
  },
});
EOF
} &&
{cat <<'EOF' > ./src/pages/index.tsx
import { getMessages } from "@/i18n/getMessages";
import { NextPageContext } from "next";
import { useTranslations } from "next-intl";

export default function Home() {
  const t = useTranslations();
  return <h1>{t("title")}</h1>;
}

export async function getStaticProps(context: NextPageContext) {
  return {
    props: {
      messages: await getMessages(context),
    },
  };
}
EOF
} &&
{cat <<'EOF' > ./next.config.js
/** @type {import('next').NextConfig} */

const nextConfig = {
  reactStrictMode: true,
  i18n: {
    locales: ["en"],
    defaultLocale: "en",
  },
}
module.exports = nextConfig;
EOF
}
```