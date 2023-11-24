# Building a Node.js Calculator App with HTML UI

## 1. Setting Up the Node.js Environment

Install Node.js: Download and install Node.js from the official website.

Create a Project Directory:

mkdir simple-calculator && cd simple-calculator

Initialize npm:

npm init -y

## 2. Creating the Node.js Calculator App with HTML UI

Create index.js:
This is your main server file. Include the necessary code to create a basic web server and define the /calculate endpoint for your calculator operations.

Install Express:

npm install express

Create index.html:
This file will contain the HTML structure for your calculator UI. Include inputs for numbers, buttons for operations, and a script to handle button clicks and make requests to your server.

## 3. Containerizing the Application

Create a Dockerfile:
This file is used to build a Docker container for your app.

FROM node:14

WORKDIR /usr/src/app

COPY package*.json ./

RUN npm install

COPY . .

EXPOSE 3000

CMD ["node", "index.js"]

## 4. Testing the Application Locally

Run Node.js Server:

node index.js

Test the application by accessing http://localhost:3000 in a web browser and using curl to test the API.

Testing with Docker:

Build the Docker image:

docker build -t my-calculator-app .

Run the Docker container:

docker run -p 3000:3000 my-calculator-app

Access http://localhost:3000 to interact with the application running inside the Docker container.

## 5. Creating a GitHub Repository and Pushing the Code

Initialize Git Repository:

In your project directory:

git init

Create a .gitignore File:
Include node_modules/ and other files/directories you don't want to track.

Add Files to Git:

git add .

Commit Files:

git commit -m "Initial commit"

Create a New Repository on GitHub:

Go to GitHub, sign in, and create a new repository.

Add GitHub Repository as Remote:

Replace YOUR-REPOSITORY-URL with your GitHub repository URL.

git remote add origin YOUR-REPOSITORY-URL

Push to GitHub:

git push -u origin master

