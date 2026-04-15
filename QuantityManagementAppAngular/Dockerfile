# ─── Stage 1: Build ───────────────────────────────────────────────────────────
FROM node:20-alpine AS builder

WORKDIR /app

# Copy package files first for better Docker layer caching
COPY package.json package-lock.json ./

# Install all dependencies (including devDependencies needed for build)
RUN npm ci

# Copy the rest of the source code
COPY . .

# Build the Angular app in production mode
RUN npm run build

# ─── Stage 2: Serve ───────────────────────────────────────────────────────────
FROM node:20-alpine AS runner

WORKDIR /app

# Install only the 'serve' package to host the static files
RUN npm install -g serve

# Copy built output from the builder stage
COPY --from=builder /app/dist/quantity-management-app-angular/browser ./dist

# Expose the port Render will use (Render sets $PORT at runtime)
EXPOSE 3000

# Start the static file server
# --single flag handles Angular client-side routing (redirects 404s to index.html)
CMD serve ./dist --listen tcp://0.0.0.0:${PORT:-3000} --single
