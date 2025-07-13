# Use the official lightweight Node.js 16 image (based on Alpine Linux) as the base image for the first stage
FROM node:16-alpine AS stage-one

# Set the working directory inside the container to /app
WORKDIR /app

# Copy package.json and package-lock.json into the container
COPY package*.json ./

# Install project dependencies exactly as specified in package-lock.json
# npm ci is faster and more reliable for production builds than npm install
RUN npm ci

# Copy the rest of the application’s source code into the container
COPY . .

# Build the app for production (typically creates an optimized static build in /build)
RUN npm run build


# Start a new stage with the same base Node.js 16 Alpine image for the final lightweight image
FROM node:16-alpine

# Set the working directory inside the container to /app
WORKDIR /app

# Install "serve" globally to serve the production build as a static site
RUN npm install -g serve

# Copy only the production build artifacts from the first stage into this final image
# This keeps the final image small and avoids including dev dependencies and source code
COPY --from=stage-one /app/build ./build

# Expose port 3000 so the container can accept connections on this port
EXPOSE 3000

# Define the command to run when the container starts:
# serve the static build folder on port 3000
CMD [ "serve", "-s", "build", "-l", "3000" ]
