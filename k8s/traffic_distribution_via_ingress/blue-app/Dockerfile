# Use minimal base image
FROM node:18-slim

# Set working directory
WORKDIR /app

# Copy package.json and package-lock.json (if available)
COPY package*.json ./

# Install only production dependencies (none in this case, but good practice)
RUN npm install --production

# Copy application source
COPY . .

# Expose port 8080
EXPOSE 8080

# Start the app
CMD ["node", "index.js"]
