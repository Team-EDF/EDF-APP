# =========================
# Build Stage
# =========================
FROM node:20-alpine AS builder

WORKDIR /app

COPY EDF-FE/package*.json ./

RUN npm install

COPY EDF-FE/ .

RUN npm run build


# =========================
# Runtime Stage
# =========================
FROM nginx:stable-alpine

COPY --from=builder /app/dist /usr/share/nginx/html

COPY nginx.conf /etc/nginx/conf.d/default.conf

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]