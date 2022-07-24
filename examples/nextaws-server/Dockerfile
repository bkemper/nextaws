# see, https://github.com/vercel/next.js/tree/canary/examples/with-docker
# see, https://nodejs.org/en/docs/guides/nodejs-docker-webapp/

FROM node:18 AS deps
WORKDIR /app
COPY package*.json ./
RUN [ -f package-lock.json ] && npm ci || (echo "Lockfile not found." && exit 1)


FROM node:18 AS builder
WORKDIR /app
COPY --from=deps /app/node_modules ./node_modules
COPY . .
ENV NODE_ENV production
ENV NEXT_TELEMETRY_DISABLED 1
RUN npm run build


FROM node:18 AS runner
WORKDIR /app
ENV NODE_ENV production
ENV NEXT_TELEMETRY_DISABLED 1
RUN addgroup --system --gid 1001 nodejs
RUN adduser --system --uid 1001 nextjs
COPY --from=builder /app/next.config.js ./
COPY --from=builder /app/public ./public
COPY --from=builder /app/package.json ./package.json
COPY --from=builder --chown=nextjs:nodejs /app/.next/standalone ./
COPY --from=builder --chown=nextjs:nodejs /app/.next/static ./.next/static
USER nextjs
EXPOSE 3000
ENV PORT 3000
CMD ["node", "server.js"]