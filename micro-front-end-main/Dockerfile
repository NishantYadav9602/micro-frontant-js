FROM node:18-alpine

WORKDIR /app

COPY package*.json ./
RUN npm install

COPY . .

# ✅ Fix OpenSSL issue for Next.js + Webpack
ENV NODE_OPTIONS=--openssl-legacy-provider

RUN npm run build

EXPOSE 3001

CMD ["npm", "run", "start"]

