ARG NODE_VERSION=22
FROM node:${NODE_VERSION}-slim AS base

# Next.js app lives here
WORKDIR /app

# Set production environment
ENV NODE_ENV="production"


# Throw-away build stage to reduce size of final image
FROM base AS build

# Install packages needed to build node modules
RUN apt-get update -qq && \
  apt-get install --no-install-recommends -y build-essential node-gyp pkg-config python-is-python3

# Install node modules
COPY --link package-lock.json package.json ./
RUN npm ci --include=dev

# Copy application code
COPY --link . .

# Build application
RUN npm run build

# Remove development dependencies
RUN npm prune --omit=dev


FROM base AS run

# Copy built app
COPY --from=build /app/.next/standalone /app
COPY --from=build /app/.next/static /app/.next/static
#COPY --from=build /app/public /app/public

# Run the app
CMD [ "npm", "run", "start" ]