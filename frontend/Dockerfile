# Stage 1: Build the application assets safely
FROM node:20-alpine AS builder
WORKDIR /app

# Copy dependency manifests
COPY package.json package-lock.json ./
RUN npm ci --legacy-peer-deps

# Copy source code and build production assets
COPY . .
RUN npm run build


# Stage 2: Minimal Production Runtime Environment
FROM node:20-alpine AS runner
WORKDIR /app

# Copy assets and dependencies directly assigning ownership to 'node' user
COPY --from=builder --chown=node:node /app/package.json ./package.json
COPY --from=builder --chown=node:node /app/node_modules ./node_modules
COPY --from=builder --chown=node:node /app/dist ./dist
COPY --from=builder --chown=node:node /app/vite.config.js ./vite.config.js

# Switch execution context to the safe non-root user
USER node

EXPOSE 4173

# Execute the preview server bound to host 0.0.0.0 for Docker networking
CMD ["npm", "run", "preview", "--", "--host", "0.0.0.0", "--port", "4173"]
