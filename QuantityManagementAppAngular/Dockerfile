# ─── Stage 1: Build ───────────────────────────────────────────────────────────
FROM node:20-alpine AS builder

WORKDIR /app

# Copy package file first for better Docker layer caching
COPY package.json ./

# Install all dependencies (including devDependencies needed for build)
RUN npm install --legacy-peer-deps

# Copy the rest of the source code
COPY . .

# Build the Angular app in production mode
# angular.json sets outputPath.base=dist, outputPath.browser=""
# so compiled files land directly in /app/dist/ (no browser/ subfolder)
RUN npm run build

# ─── Stage 2: Serve ───────────────────────────────────────────────────────────
FROM node:20-alpine AS runner

WORKDIR /app

# Install serve globally to host static files
RUN npm install -g serve

# Copy compiled output directly from builder's dist/
COPY --from=builder /app/dist ./dist

# Render injects $PORT at runtime; default to 3000 locally
EXPOSE 3000

# --single: redirects all 404s to index.html for Angular client-side routing
CMD serve ./dist --listen tcp://0.0.0.0:${PORT:-3000} --single
