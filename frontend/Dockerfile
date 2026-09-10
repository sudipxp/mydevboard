# Stage 1: Install dependencies
FROM node:20-alpine AS builder

WORKDIR /app

COPY package.json package-lock.json ./

RUN npm ci --legacy-peer-deps

# Copy application source
COPY . .

# Build production files
RUN npm run build


# Stage 2: Run application
FROM node:20-alpine AS runner

WORKDIR /app

# Copy node_modules and application files
COPY --from=builder /app/node_modules ./node_modules
COPY --from=builder /app/dist ./dist
COPY --from=builder /app/package.json ./package.json
COPY --from=builder /app/vite.config.js ./vite.config.js

# Make sure the application directory belongs to the node user
RUN chown -R node:node /app

# Run as non-root user
USER node

EXPOSE 4173

CMD ["npm", "run", "preview", "--", "--host", "0.0.0.0", "--port", "4173"]