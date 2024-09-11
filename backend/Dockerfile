# Stage 1: Setup and build
FROM node:latest AS builder

WORKDIR /backend

# Copy package.json and install dependencies
COPY package.json .
RUN npm install

# Create directory structure
RUN mkdir -p src/config src/controllers src/models src/routes src/middleware src/utils tests scripts

# Create initial app.js file
RUN echo "const express = require('express');" > src/app.js && \
    echo "const app = express();" >> src/app.js && \
    echo "const port = 8080;" >> src/app.js && \
    echo "app.get('/', (req, res) => {" >> src/app.js && \
    echo "  res.send('Hello World from PERN backend!');" >> src/app.js && \
    echo "});" >> src/app.js && \
    echo "app.listen(port, () => {" >> src/app.js && \
    echo "  console.log(\`Backend server running on port \${port}\`);" >> src/app.js && \
    echo "});" >> src/app.js && \
    echo "module.exports = app;" >> src/app.js

# Copy the rest of the application code
COPY . .

# Stage 2: Runtime
FROM node:latest

WORKDIR /backend

# Copy built app from builder stage
COPY --from=builder /backend .

# Build argument to determine the environment
ARG NODE_ENV=development
ENV NODE_ENV=$NODE_ENV

# Install production dependencies and nodemon for development
RUN if [ "$NODE_ENV" = "production" ]; \
    then npm ci --only=production; \
    else npm ci && npm install -D nodemon; \
    fi

# Store the generated templates in a safe folder
RUN cp -r /backend /temporary

# Expose the port the app runs on
EXPOSE 8080

#Start the app depending on the environment
CMD cp -r /temporary/* /backend \
    && if [ "$NODE_ENV" = "production" ]; \
    then npm run start || { echo 'Production start failed'; exit 1; }; \
    else npm run dev || { echo 'Development start failed'; exit 1; }; \
    fi
