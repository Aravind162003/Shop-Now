# Stage 1: Dependency Resolution
FROM node:20-alpine AS deps
WORKDIR /app

# Copy package files
COPY package*.json ./

# Install ONLY production dependencies. 
# We use 'npm ci' for a clean, deterministic installation based on your package-lock.json.
RUN npm ci --omit=dev

# Stage 2: Production Runtime
FROM node:20-alpine
WORKDIR /app

# Copy the clean, production-only node_modules from the first stage
COPY --from=deps /app/node_modules ./node_modules

# Copy the rest of your backend application code
COPY . .

# Expose your API port (change this to 8080 if your Express server uses a different port)
EXPOSE 5000

# Start the Node backend
CMD ["node", "index.js"]
