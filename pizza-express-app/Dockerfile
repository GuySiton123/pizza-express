# Use an official Node.js runtime as a parent image
FROM node:8.4

# Set the working directory to /app
WORKDIR /app

# Copy the package.json file into the container at /app
COPY package.json /app

# Install any dependencies specified in package.json
RUN npm install

# Copy the rest of the application code into the container at /app
COPY . /app

# Expose ports 3000 to the outside world
EXPOSE 3000

# Run unit tests
#RUN npm test

# Start the application
CMD [ "npm", "start" ]
