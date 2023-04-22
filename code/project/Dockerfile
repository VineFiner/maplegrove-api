FROM gcr.io/kaniko-project/executor:debug AS kaniko

FROM node:16-alpine as build
# Installing libvips-dev for sharp Compatibility
# 配置国内源
RUN sed -i 's/dl-cdn.alpinelinux.org/mirrors.aliyun.com/g' /etc/apk/repositories
RUN apk update && apk add --no-cache build-base gcc autoconf automake zlib-dev libpng-dev vips-dev && rm -rf /var/cache/apk/* > /dev/null 2>&1
ARG NODE_ENV=production
ENV NODE_ENV=${NODE_ENV}
WORKDIR /opt/
COPY ./package.json ./package-lock.json ./
ENV PATH /opt/node_modules/.bin:$PATH
RUN npm install --production
WORKDIR /opt/app
COPY ./ .
RUN npm run build

FROM node:16-alpine
# Installing libvips-dev for sharp Compatibility
RUN apk add vips-dev
RUN rm -rf /var/cache/apk/*
ARG NODE_ENV=production
ENV NODE_ENV=${NODE_ENV}
# 工作目录
# WORKDIR /opt/app
WORKDIR /data/strapi
COPY --from=build /opt/node_modules ./node_modules
ENV PATH /opt/node_modules/.bin:$PATH
COPY --from=build /opt/app ./
EXPOSE 1337
CMD ["npm", "run","start"]