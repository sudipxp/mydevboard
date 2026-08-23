# Stage 1: Resolve and cache all dependencies
FROM node:20-alpine AS deps
WORKDIR /app
COPY package.json package-lock.json ./
# Added --legacy-peer-deps to ignore upstream plugin conflicts
RUN npm ci --legacy-peer-deps

# Stage 2: Build code and serve via Vite preview
FROM node:20-alpine AS runner
WORKDIR /app

# Copy cached node_modules from the previous stage
COPY --from=deps /app/node_modules ./node_modules
COPY . .

# Compile the production assets into the /dist folder
RUN npm run build

EXPOSE 4173

# Execute the preview server bound to host 0.0.0.0 for Docker networking
CMD ["npm", "run", "preview", "--", "--host", "0.0.0.0", "--port", "4173"]