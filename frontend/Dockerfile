# Stage 1: Build the application assets safely
FROM node:20-alpine AS builder
WORKDIR /app

# Copy dependency manifests
COPY package.json package-lock.json ./
RUN npm install --legacy-peer-deps

# Copy source code and build the production assets into /app/dist
COPY . .
RUN npm run build

# Stage 2: Minimal Production Runtime Environment
FROM node:20-alpine AS runner
WORKDIR /app

# Set production environment variable flag
# ENV NODE_ENV=production

# Copy only production dependencies and compiled files, granting access to the native non-root 'node' user
COPY --from=builder --chown=node:node /app/package.json ./package.json
COPY --from=builder --chown=node:node /app/node_modules ./node_modules
COPY --from=builder --chown=node:node /app/dist ./dist

# Vite preview requires access to the config file at runtime to serve assets properly
COPY --from=builder --chown=node:node /app/vite.config.js* ./
COPY --from=builder --chown=node:node /app/vite.config.ts* ./

# Switch execution context to the safe non-root user
USER node

EXPOSE 4173

# Execute the preview server bound to host 0.0.0.0 for Docker networking
CMD ["npm", "run", "preview", "--", "--host", "0.0.0.0", "--port", "4173"]