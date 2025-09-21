FROM node:18-alpine AS builder

RUN apk add --no-cache git

WORKDIR /app

COPY package*.json ./

RUN npm ci --only=production --no-audit --no-fund

COPY . .

ENV NODE_OPTIONS=--openssl-legacy-provider
RUN npm run build

FROM nginx:alpine

RUN rm /etc/nginx/conf.d/default.conf

COPY --from=builder /app/dist /usr/share/nginx/html

COPY nginx.conf /etc/nginx/conf.d/default.conf

RUN mkdir -p /var/cache/nginx /var/log/nginx /var/run/nginx && \
    chown -R nginx:nginx /var/cache/nginx /var/log/nginx /var/run/nginx /usr/share/nginx/html

EXPOSE 8080

USER nginx

CMD ["nginx", "-g", "daemon off;"]
