# syntax=docker/dockerfile:1

ARG NODE_BUILD_IMAGE=node:24-alpine
ARG NODE_RUNTIME_IMAGE=node:24-alpine

FROM ${NODE_BUILD_IMAGE} AS build

WORKDIR /app

COPY package*.json ./

RUN npm ci

COPY . .

RUN npm run build

FROM ${NODE_RUNTIME_IMAGE} AS runtime

WORKDIR /app

ENV NODE_ENV=production

COPY --from=build /app/.output /app/.output

EXPOSE 3000

CMD ["node", ".output/server/index.mjs"]
