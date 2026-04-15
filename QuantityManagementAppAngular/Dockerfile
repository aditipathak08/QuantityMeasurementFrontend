# ─── Stage 1: Build ───────────────────────────────────────────────────────────
FROM node:20-alpine AS builder

WORKDIR /app

# Set legacy-peer-deps globally to avoid Angular peer dep conflicts
RUN npm config set legacy-peer-deps true

# Copy package file and install dependencies
COPY package.json ./
RUN npm install

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

# --single flag handles Angular client-side routing (redirects 404s to index.html)
CMD serve ./dist --listen tcp://0.0.0.0:${PORT:-3000} --single
