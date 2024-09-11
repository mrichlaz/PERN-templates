# Stage 1: Setup and build
FROM node:latest AS builder

WORKDIR /frontend

# Create a new React app using create-react-app
RUN npx create-react-app .

# Copy package.json and install dependencies
COPY package*.json .
RUN npm install

# Copy the rest of the application code
COPY . .

# Stage 2: Runtime
FROM node:latest    

WORKDIR /frontend

# Install serve to run the application
RUN npm install -g serve

# Copy built app from builder stage
COPY --from=builder /frontend .

# Build the React app based on environment
ARG REACT_APP_ENV=development
ENV REACT_APP_ENV=$REACT_APP_ENV

# Install check based on environtment
RUN if [ "$REACT_APP_ENV" = "production" ]; then \
        npm run build && npm install -g serve; \
    else \
        echo "Skipping build for development" && npm install; \
    fi

# Store the generated templates in a safe folder
RUN cp -r /frontend /temporary

# Expose the port the app runs on
EXPOSE 3000

# Start the app using serve
CMD cp -r /temporary/* /frontend \
    && if [ "$REACT_APP_ENV" = "production" ]; then \
        serve -s build -l 3000; \
    else \
        npm start; \
    fi  
