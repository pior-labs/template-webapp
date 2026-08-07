FROM node:22-alpine AS build
WORKDIR /app
RUN corepack enable

COPY package.json pnpm-workspace.yaml tsconfig.base.json .npmrc ./
COPY packages/web/package.json packages/web/package.json
RUN --mount=type=secret,id=github_token \
    GITHUB_TOKEN="$(cat /run/secrets/github_token)" \
    pnpm install --filter @webapp/web... --no-frozen-lockfile

COPY packages/web packages/web
RUN pnpm --filter @webapp/web build

FROM nginx:1.27-alpine AS runtime
COPY packages/web/nginx.conf /etc/nginx/conf.d/default.conf
COPY --from=build /app/packages/web/dist /usr/share/nginx/html
