# Cleanup just created remix project
From scratch on POSIX system

```zsh
cd "$(\ls -1dt ./*/ | head -n 1)" &&
rm -rf ./public/* &&
{cat <<'EOF' > ./app/routes/_index.tsx
import type { V2_MetaFunction } from "@remix-run/node";

export const meta: V2_MetaFunction = () => {
  return [
    { title: "" },
    { name: "description", content: "" },
  ];
};

export default function Index() {
  return (
    <div>
      <h1>Hello World</h1>
    </div>
  );
}
EOF
}
```zsh