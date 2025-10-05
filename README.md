🧩 Microfrontends using Next.js and Module Federation

A complete example of building Micro Frontends using Next.js and Module Federation (@module-federation/nextjs-mf).
Each micro-frontend runs independently and communicates through the shell app.

🚀 Project Overview

This repository demonstrates how to create and connect multiple Next.js applications using Webpack Module Federation.

Structure:

microfrontend-nextjs-docker/
│
├── micro-front-end-main       # App 1 → Luigi component
├── micro-front-end-activate   # App 2 → Mario component
└── micro-front-end-shell      # Shell / Host app


Each app runs independently and exposes or consumes components via @module-federation/nextjs-mf.

🧠 Module Federation Setup
🟦 Micro Frontend 01 – app2 (Mario)
const { withModuleFederation } = require("@module-federation/nextjs-mf");

module.exports = {
  future: { webpack5: true },
  images: { domains: ['upload.wikimedia.org'] },
  webpack: (config, options) => {
    const mfConf = {
      mergeRuntime: true,
      name: "app2",
      library: { type: config.output.libraryTarget, name: "app2" },
      filename: "static/runtime/app2remoteEntry.js",
      exposes: {
        "./mario": "./components/mario",
      },
    };
    config.cache = false;
    withModuleFederation(config, options, mfConf);
    return config;
  },
};

🟩 Micro Frontend 02 – app1 (Luigi)
const { withModuleFederation } = require("@module-federation/nextjs-mf");

module.exports = {
  future: { webpack5: true },
  images: { domains: ['upload.wikimedia.org'] },
  webpack: (config, options) => {
    const mfConf = {
      mergeRuntime: true,
      name: "app1",
      library: { type: config.output.libraryTarget, name: "app1" },
      filename: "static/runtime/app1RemoteEntry.js",
      exposes: {
        "./luigi": "./components/luigi",
      },
    };
    config.cache = false;
    withModuleFederation(config, options, mfConf);
    return config;
  },
};

🟨 Shell App – shell
const { withModuleFederation } = require("@module-federation/nextjs-mf");

module.exports = {
  future: { webpack5: true },
  images: { domains: ['upload.wikimedia.org'] },
  webpack: (config, options) => {
    const mfConf = {
      name: "shell",
      library: { type: config.output.libraryTarget, name: "shell" },
      remotes: {
        app1: "app1",
        app2: "app2",
      },
      exposes: {},
    };
    config.cache = false;
    withModuleFederation(config, options, mfConf);
    return config;
  },
};

🐳 Run with Docker

Each app has its own Dockerfile with the OpenSSL fix applied (NODE_OPTIONS=--openssl-legacy-provider).

🧱 docker-compose.yml
version: "3.8"

services:
  app1:
    build: ./micro-front-end-main
    container_name: app1
    ports:
      - "3001:3001"

  app2:
    build: ./micro-front-end-activate
    container_name: app2
    ports:
      - "3002:3002"

  shell:
    build: ./micro-front-end-shell
    container_name: shell
    ports:
      - "3000:3000"
    depends_on:
      - app1
      - app2

⚙️ Local Setup (Without Docker)
# Run App2 (Mario)
cd micro-front-end-activate
npm install 
npm run build
npm run dev

# Run App1 (Luigi)
cd ../micro-front-end-main
npm install
npm run build
npm run dev

# Run Shell (Main Host)
cd ../micro-front-end-shell
npm install
npm run build
npm run dev

🌍 Test the Setup

Once everything is running:

App	Description	URL
🟩 App1	Luigi component	http://localhost:3001/luigi
🟦 App2	Mario component	http://localhost:3002/mario
🟨 Shell	Main host app	http://localhost:3000/

Shell app consumes components:

http://localhost:3000/mario → renders from App2

http://localhost:3000/luigi → renders from App1

☁️ Deploying on EC2 with Docker
# SSH into EC2
ssh -i "your-key.pem" ubuntu@<EC2-PUBLIC-IP>

# Install Docker
sudo apt update && sudo apt install -y docker.io docker-compose
sudo systemctl enable docker && sudo systemctl start docker
sudo usermod -aG docker ubuntu
exit  # logout & login again

# Clone Repo
git clone https://github.com/NishantYadav9602/microfrontend-nextjs-docker.git
cd microfrontend-nextjs-docker

# Build and Run
docker-compose build --no-cache
docker-compose up -d


Access:

http://<EC2-PUBLIC-IP>:3000/mario

http://<EC2-PUBLIC-IP>:3000/luigi

🧰 Tech Stack

Next.js

Webpack 5

Module Federation

Docker & Docker Compose

EC2 (Ubuntu)

🏁 Author

Nishant Yadav
💼 DevOps & Cloud Enthusiast | Docker | Kubernetes | Terraform | Jenkins
