ARG NODE_VERSION=node:22-alpine

# Base stage
FROM $NODE_VERSION AS base
WORKDIR /app

COPY package.json package-lock.json ./
RUN --mount=type=cache,target=/root/.npm npm install


# Development stage
FROM base AS dev
ENV NODE_ENV=development
ENV HOSTNAME=0.0.0.0
ENV PORT=3000
EXPOSE 3000
WORKDIR /app

COPY . .

CMD ["npm", "run", "dev"]


# Production build stage
FROM base AS build
ENV NODE_ENV=production

COPY . .
RUN npm run build


# Production stage
FROM $NODE_VERSION AS prod
ENV NODE_ENV=production
ENV HOSTNAME=0.0.0.0
ENV PORT=3000
EXPOSE 3000
WORKDIR /app

RUN addgroup --system --gid 1001 nodejs
RUN adduser --system --uid 1001 nextjs

COPY --from=build /app/public ./public

COPY --from=build --chown=nextjs:nodejs /app/.next/standalone ./
COPY --from=build --chown=nextjs:nodejs /app/.next/static ./.next/static

USER nextjs
CMD ["node", "server.js"]