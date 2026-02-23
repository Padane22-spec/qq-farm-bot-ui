FROM node:20-alpine AS builder

WORKDIR /app

RUN corepack enable

# Copy workspace configuration
COPY pnpm-workspace.yaml package.json ./

# Copy project package.json files
COPY backend/package.json ./backend/
COPY frontend/package.json ./frontend/

# Install dependencies (frozen-lockfile is default for pnpm install in CI)
RUN pnpm install

# Copy source code
COPY backend ./backend
COPY frontend ./frontend

# Build frontend
RUN pnpm build:frontend

# ==========================================

FROM node:20-alpine AS runner

WORKDIR /app/backend

ENV NODE_ENV=production
ENV TZ=Asia/Shanghai

# Copy backend source and dependencies
COPY --from=builder /app/backend ./
COPY --from=builder /app/node_modules ./node_modules/
COPY --from=builder /app/package.json ../package.json

# Copy built frontend assets
COPY --from=builder /app/frontend/dist ../frontend/dist

# Expose port
EXPOSE 3000

# Start command
CMD ["node", "client.js"]
