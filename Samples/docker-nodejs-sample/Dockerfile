# syntax=docker/dockerfile:1

# Specify the Node.js version to use as a build argument
ARG NODE_VERSION=18.0.0

# Base stage: Use the specified Node.js version with Alpine Linux for a lightweight image
FROM node:${NODE_VERSION}-alpine as base
WORKDIR /usr/src/app 
EXPOSE 3000          

# Development stage: Extend the base image for development purposes
FROM base as dev
# Use bind mounts to link package.json and package-lock.json for dependency installation
# Cache npm dependencies to speed up subsequent builds
RUN --mount=type=bind,source=package.json,target=package.json \
    --mount=type=bind,source=package-lock.json,target=package-lock.json \
    --mount=type=cache,target=/root/.npm \
    npm ci --include=dev 
USER node               
COPY . .                
CMD npm run dev         

# Production stage: Extend the base image for production purposes
FROM base as prod
# Use bind mounts to link package.json and package-lock.json for dependency installation
# Cache npm dependencies to speed up subsequent builds
RUN --mount=type=bind,source=package.json,target=package.json \
    --mount=type=bind,source=package-lock.json,target=package-lock.json \
    --mount=type=cache,target=/root/.npm \
    npm ci --omit=dev 
USER node              
COPY . .                
CMD node src/index.js   

# Test stage: Extend the base image for running tests
FROM base as test
ENV NODE_ENV test
RUN --mount=type=bind,source=package.json,target=package.json \
    --mount=type=bind,source=package-lock.json,target=package-lock.json \
    --mount=type=cache,target=/root/.npm \
    npm ci --include=dev
USER node
COPY . .
RUN npm run test