FROM node:12.18.2-alpine AS build

RUN apk add --update --no-cache \
    python \
    make \
    g++ \
    git

COPY . /src
WORKDIR /src

RUN npm ci

RUN npm run lint
RUN npm run build:nginx
RUN npm run test

RUN npm prune --production

FROM nginx:1.19.1-alpine

RUN apk add --update --no-cache curl

WORKDIR /usr/src/service

COPY --from=build /src/dist ./dist
COPY --from=build /src/nginx ./nginx

HEALTHCHECK --interval=5s \
            --timeout=5s \
            --retries=6 \
            CMD curl -fs http://localhost:1234/ || exit 1

RUN ["chmod", "+x", "./nginx/entrypoint.sh"]
ENTRYPOINT [ "ash", "./nginx/entrypoint.sh" ]