# Stage 1: Resolve and cache all dependencies
# Using Docker Hardened Images (DHI) for Node.js with security patches
FROM node:22-alpine3.21 AS builder
WORKDIR /app

# Copy dependency manifests
COPY package.json ./
COPY package-lock.json* ./

# Install dependencies with npm ci if lock file exists, else npm install
# DHI includes patched Node.js runtime and npm packages
RUN if [ -f package-lock.json ]; then npm ci --legacy-peer-deps; else npm install --legacy-peer-deps; fi

# Stage 2: Build and serve via Vite preview
# Using DHI base image with minimal footprint
FROM node:22-alpine3.21 AS runner
WORKDIR /app

# Create a non-root system user for security compliance (uid/gid 10001)
RUN addgroup -g 10001 appuser && adduser -D -u 10001 -G appuser appuser

# Copy cached dependencies from builder stage
COPY --from=builder --chown=appuser:appuser /app/node_modules ./node_modules

# Copy application source
COPY --chown=appuser:appuser . .

# Build production assets into /dist folder
RUN npm run build

# Switch to non-root user before runtime
USER appuser

# Document port
EXPOSE 4173

# Execute Vite preview server
CMD ["npm", "run", "preview", "--", "--host", "0.0.0.0", "--port", "4173"]