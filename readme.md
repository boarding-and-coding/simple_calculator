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

## Level 2 - Create CI/CD Pipeline to GCS Bucket

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

Enable the Cloud Build API and Cloud Storage API.

### 2. Create a Google Cloud Storage Bucket

Open Cloud Storage: In the GCP Console, navigate to "Storage" > "Browser".

Create a Bucket:

Click "Create bucket".

Enter a unique bucket name. (I always use the project's project ID)

Choose a default storage class and location for your bucket.

Click "Create".

### 3. Set Up Cloud Build

Open Cloud Build: In the GCP Console, go to "Cloud Build" > "Triggers".

Connect Repository:

Click "Connect Repository".

Choose GitHub (or another source if your repository is elsewhere).

Authenticate and select the repository you want to connect.

Create a Build Trigger:

After connecting your repository, create a new trigger.

Configure the trigger to build on changes to the master branch.

### 4. Create a Build Configuration File

Create cloudbuild.yaml in Your Repository:

In your project's root directory, create a file named cloudbuild.yaml.

This file will define the build steps and actions to be performed.

Define Build Steps in cloudbuild.yaml:

Specify steps to build your application (if necessary) and copy files to the GCS bucket.

Example of copying files to a bucket:

```yaml
steps:

- name: 'gcr.io/cloud-builders/gsutil'

  args: ['cp', '-r', '.', 'gs://YOUR_BUCKET_NAME']
```

Replace YOUR_BUCKET_NAME with the name of your GCS bucket.

### 5. Push cloudbuild.yaml to Your Repository

Commit cloudbuild.yaml to your repository and push it to GitHub:

```bash
git add cloudbuild.yaml

git commit -m "Add Cloud Build configuration"

git push origin master 
```

### 6. Test Your Pipeline

Make a change to your repository and push it to the master branch.

Cloud Build should trigger a build and execute the steps defined in cloudbuild.yaml.

Notes
You might need to configure permissions and service account roles in GCP for Cloud Build to access GCS.
Ensure you understand the costs associated with GCP services used in your pipeline.
This setup will create a basic CI/CD pipeline that deploys your code to a GCS bucket whenever you push to the master branch. You can expand and customize this pipeline based on your specific requirements.