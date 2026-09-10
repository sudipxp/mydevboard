# Stage 1: Resolve and cache all dependencies
FROM node:22-alpine AS builder
WORKDIR /app
COPY package.json ./
# Copy package-lock.json if it exists (optional)
COPY package-lock.json* ./
# Use npm ci if lock file exists, otherwise npm install
RUN if [ -f package-lock.json ]; then npm ci --legacy-peer-deps; else npm install --legacy-peer-deps; fi

# Stage 2: Build code and serve via Vite preview
FROM node:22-alpine AS runner
WORKDIR /app

# Create a non-root system user for security compliance
RUN addgroup -g 10001 appuser && adduser -D -u 10001 -G appuser appuser

# Copy cached node_modules from the previous stage
COPY --from=builder /app/node_modules ./node_modules
COPY . .

# Compile the production assets into the /dist folder
RUN npm run build

# Switch away from root to the non-privileged user
USER appuser

EXPOSE 4173

# Execute the preview server bound to host 0.0.0.0 for Docker networking
CMD ["npm", "run", "preview", "--", "--host", "0.0.0.0", "--port", "4173"]
