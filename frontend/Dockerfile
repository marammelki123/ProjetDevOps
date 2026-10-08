FROM node:22-alpine AS build
ARG API_URL=http://localhost:8080
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN sed -i "s#http://localhost:8080#${API_URL}#g" src/app/services/*.ts
RUN npm run build

FROM nginx:alpine
COPY --from=build /app/dist/frontend/browser /usr/share/nginx/html
EXPOSE 80
