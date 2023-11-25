# Building a Node.js Calculator App with HTML UI

## Level 1 - Build App and Repository

### 1. Setting Up the Node.js Environment

Install Node.js: Download and install Node.js from the official website.

Create a Project Directory:

```mkdir simple-calculator && cd simple-calculator```

Initialize npm:

```npm init -y```

### 2. Creating the Node.js Calculator App with HTML UI

Create index.js:
This is your main server file. Include the necessary code to create a basic web server and define the /calculate endpoint for your calculator operations.

```javascript
const express = require('express');
const path = require('path');
const app = express();

app.use(express.json());
app.use(express.static(path.join(__dirname))); // Serve static files

app.get('/', (req, res) => {
    res.sendFile(path.join(__dirname, 'index.html'));
});

app.post('/calculate', (req, res) => {
    const { operation, number1, number2 } = req.body;
    
    let result;
    switch (operation) {
        case 'add':
            result = number1 + number2;
            break;
        case 'subtract':
            result = number1 - number2;
            break;
        case 'multiply':
            result = number1 * number2;
            break;
        case 'divide':
            result = number1 / number2;
            break;
        default:
            return res.status(400).send('Invalid operation');
    }

    res.send({ result });
});

const port = process.env.PORT || 3000;
app.listen(port, () => {
    console.log(`Server is running on port ${port}`);
});
```

Install Express:

```npm install express```

Create index.html:
This file will contain the HTML structure for your calculator UI. Include inputs for numbers, buttons for operations, and a script to handle button clicks and make requests to your server.

```html
<!DOCTYPE html>
<html>
<head>
    <title>Simple Calculator</title>
    <style>
        /* Add your CSS styling here */
        body {
            font-family: Arial, sans-serif;
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
        }
        #calculator {
            width: 300px;
        }
        input, button {
            margin: 5px;
            height: 30px;
            width: 100%;
        }
    </style>
</head>
<body>
    <div id="calculator">
        <input type="number" id="number1" placeholder="Number 1">
        <input type="number" id="number2" placeholder="Number 2">
        <button onclick="calculate('add')">Add</button>
        <button onclick="calculate('subtract')">Subtract</button>
        <button onclick="calculate('multiply')">Multiply</button>
        <button onclick="calculate('divide')">Divide</button>
        <div id="result"></div>
    </div>

    <script>
        // Add your JavaScript here
        function calculate(operation) {
            var number1 = parseFloat(document.getElementById('number1').value);
            var number2 = parseFloat(document.getElementById('number2').value);

            fetch('/calculate', {
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json',
                },
                body: JSON.stringify({ operation, number1, number2 }),
            })
            .then(response => response.json())
            .then(data => {
                document.getElementById('result').innerText = 'Result: ' + data.result;
            })
            .catch(error => {
                console.error('Error:', error);
            });
        }
    </script>
</body>
</html>
```

### 3. Containerizing the Application

Create a Dockerfile:
This file is used to build a Docker container for your app.

```Dockerfile
FROM node:14
WORKDIR /usr/src/app
COPY package*.json ./
RUN npm install
COPY . .
EXPOSE 3000
CMD ["node", "index.js"]
```

### 4. Testing the Application Locally

Run Node.js Server:

```node index.js```

Test the application by accessing http://localhost:3000 in a web browser and using curl to test the API.

Testing with Docker:

Build the Docker image:

```docker build -t my-calculator-app .```

Run the Docker container:

```docker run -p 3000:3000 my-calculator-app```

Access http://localhost:3000 to interact with the application running inside the Docker container.

### 5. Creating a GitHub Repository and Pushing the Code

Initialize Git Repository:

In your project directory:

```git init```

Create a .gitignore File:
Include node_modules/ and other files/directories you don't want to track.

Add Files to Git:

```git add .```

Commit Files:

```git commit -m "Initial commit"```

Create a New Repository on GitHub:

Go to GitHub, sign in, and create a new repository.

Add GitHub Repository as Remote:

```git remote add origin YOUR-REPOSITORY-URL```

Replace YOUR-REPOSITORY-URL with your GitHub repository URL.

Push to GitHub:

```git push -u origin master```

## Level 2 - Create CI/CD Pipeline to GCP Cloud Run

### 1. Set Up a Google Cloud Platform (GCP) Project

Sign in to Google Cloud Console: Go to the Google Cloud Console and sign in with your Google account.

Create a New Project:

In the GCP Console, select or create a project.

Click on the project dropdown near the top of the page, then click "New Project".

Enter a project name, select a billing account, and click "Create".

Enable Billing:

Make sure your project has a billing account associated with it.

Enable Required APIs:

Navigate to "APIs & Services" > "Dashboard" in the GCP Console.

Enable the Cloud Build API and the Cloud Run API

Edit Cloud Build Service Account:

Navigate to "Cloud Build" > "Settings" here you will see the service account used to run builds. Remember the email address of that service account.

Navigate to "IAM & Admin" > "IAM"

Locate the Cloud Build service account, click the pencil/edit icon next to it to modify its roles.

Click "Add Another Role".

Search for and select the "Service Account User" role.

Click "Add Another Role".

Search for and select the "Cloud Run Admin" role.

Save the changes.

### 2. Creating a Cloud Build Trigger for Google Cloud Run

Open Cloud Build:

Go to "Cloud Build" > "Triggers"

Connect Your Repository:

Click "Connect Repository"

Choose GitHub (or the relevant source) and select your repository.

Create a New Build Trigger for Cloud Run:

Click "Create Trigger".

Enter the trigger name and description

Select the event (e.g., push to a branch)

Choose the source repository and branch (e.g., master)

Under "Build Configuration", select "Cloud Build configuration file (yaml or json)"

Enter the location of your build configuration file (usually cloudbuild.yaml)

Update/Create cloudbuild.yaml:

Modify your cloudbuild.yaml to include steps for building and deploying to Cloud Run.

Example configuration:

```yaml
steps:
# Build the container image
- name: 'gcr.io/cloud-builders/docker'
  args: ['build', '-t', 'gcr.io/$PROJECT_ID/YOUR_SERVICE_NAME', '.']
# Push the container image to Container Registry
- name: 'gcr.io/cloud-builders/docker'
  args: ['push', 'gcr.io/$PROJECT_ID/YOUR_SERVICE_NAME']
# Deploy the container image to Cloud Run
- name: 'gcr.io/google.com/cloudsdktool/cloud-sdk'
  entrypoint: gcloud
  args: ['run', 'deploy', 'YOUR_SERVICE_NAME', '--image', 'gcr.io/$PROJECT_ID/YOUR_SERVICE_NAME', '--region', 'YOUR_REGION', '--platform', 'managed']
timeout: '1600s'
```

Replace YOUR_SERVICE_NAME with the name of your Cloud Run service and YOUR_REGION with your desired region.

Push the Updated cloudbuild.yaml:

Commit and push the updated cloudbuild.yaml to your repository:

```bash
git add cloudbuild.yaml
git commit -m "Update Cloud Build configuration for Cloud Run"
git push origin master
```

Test the Trigger:

Make a change in your repository and push it to the master branch.
This should trigger a new build, and if configured correctly, your app should be built and deployed to Cloud Run.
