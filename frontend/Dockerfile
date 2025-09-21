FROM node:22-alpine AS builder

RUN apk add --no-cache git

WORKDIR /app

COPY package*.json ./
RUN npm ci --only=production --no-audit --no-fund

COPY . .

RUN npm run build

FROM nginx:alpine

RUN addgroup -g 1001 -S nginx && \
    adduser -S -D -H -u 1001 -h /var/cache/nginx -s /sbin/nologin -G nginx nginx

RUN mkdir -p /var/cache/nginx /var/log/nginx /etc/nginx/conf.d && \
    chown -R nginx:nginx /var/cache/nginx /var/log/nginx /etc/nginx/conf.d

COPY --from=builder --chown=nginx:nginx /app/dist /usr/share/nginx/html

COPY --chown=nginx:nginx nginx.conf /etc/nginx/conf.d/default.conf

USER nginx

HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
  CMD wget --no-verbose --tries=1 --spider http://localhost:8080/health || exit 1

EXPOSE 8080

CMD ["nginx", "-g", "daemon off;"]
