# Stage 1: Build the Vite application
FROM node:26-alpine AS builder
WORKDIR /app

COPY package*.json ./
RUN npm ci

COPY . .
RUN npm run build

# Stage 2: Serve the application using Node's 'serve' tool
FROM node:26-alpine
WORKDIR /app

# Installs the official production static server
RUN npm install -g serve

# Copies Vite's compiled 'dist' output folder
COPY --from=builder /app/dist ./dist

# Exposes the new Node web server port
EXPOSE 3000

# -s flag ensures React Router handles page refreshes smoothly
<<<<<<< HEAD
CMD ["serve", "-s", "dist"]
=======
CMD ["serve", "-s", "dist"]
>>>>>>> 569859f (added jenkinsfile)
