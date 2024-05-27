FROM node:lts-alpine as base

USER root
# Update image and install dependencies
RUN apk update && apk upgrade --available && apk add --no-cache g++ make python3

# Setup the application directory
RUN mkdir /app && chown -R node:node /app

USER node
WORKDIR /app

# Copy application files
COPY --chown=node:node . /app

# Add NX
RUN yarn global add nx@latest 

#############################
# Install NPM packages so we can ditch caches
FROM base as builder

# Use caching for Yarn
RUN --mount=type=cache,target=/usr/local/share/.cache/yarn/v6 \
    yarn install --immutable

#############################
# Runner without caches
FROM base as runner

WORKDIR /app

# Grab previously installed modules without caches. 
COPY --from=builder /app/node_modules ./node_modules

EXPOSE 3333

# Permissions for entrypoints
RUN chmod +x ./docker/backend/entrypoint-backend.sh
CMD ["./docker/backend/entrypoint-backend.sh"]
