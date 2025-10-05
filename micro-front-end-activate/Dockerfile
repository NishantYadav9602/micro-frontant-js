FROM node:18-alpine

WORKDIR /app

COPY package*.json ./
RUN npm install

COPY . .

# ✅ Fix OpenSSL issue
ENV NODE_OPTIONS=--openssl-legacy-provider

RUN npm run build

EXPOSE 3002

CMD ["npm", "run", "start"]

