---
layout: default
title: How to collect NGS data
parent: AWS
date:   2022-10-11 20:28:05 -0400
categories: AWS
---


---

# Three tier structure
- S3 as the frontend
- Lambda as the backend
- DynamoDB as the database

I'll create a simple serverless web application that uses S3 for hosting the frontend and Lambda for the backend API. This will be a "Todo List" app with a clean, modern interface.

## Project Structure

```
todo-app/
├── frontend/
│   ├── index.html
│   ├── style.css
│   └── app.js
├── backend/
│   └── lambda-function/
│       ├── index.js
│       └── package.json
└── infrastructure/
    └── s3-bucket-policy.json
```

## 1. Frontend (S3 Hosted)

### `frontend/index.html`
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Todo App</title>
    <link rel="stylesheet" href="style.css" />
</head>
<body>
    <div class="container">
        <h1>📝 My Todo List</h1>
        
        <div class="input-section">
            <input type="text" id="todoInput" placeholder="Enter a new todo..." />
            <button id="addButton">Add Todo</button>
        </div>

        <div id="todoList"></div>
        <div id="loading" style="display: none;">Loading...</div>
    </div>

    <script src="app.js"></script>
</body>
</html>
```

### `frontend/style.css`
```css
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

body {
    font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, sans-serif;
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    min-height: 100vh;
    display: flex;
    justify-content: center;
    align-items: center;
    padding: 20px;
}

.container {
    background: white;
    border-radius: 20px;
    padding: 40px;
    max-width: 600px;
    width: 100%;
    box-shadow: 0 20px 60px rgba(0, 0, 0, 0.3);
}

h1 {
    color: #333;
    margin-bottom: 30px;
    font-size: 2.5rem;
    text-align: center;
}

.input-section {
    display: flex;
    gap: 10px;
    margin-bottom: 30px;
}

#todoInput {
    flex: 1;
    padding: 12px 18px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 1rem;
    transition: border-color 0.3s;
}

#todoInput:focus {
    outline: none;
    border-color: #667eea;
}

#addButton {
    padding: 12px 30px;
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border: none;
    border-radius: 10px;
    font-size: 1rem;
    font-weight: 600;
    cursor: pointer;
    transition: transform 0.2s, box-shadow 0.2s;
}

#addButton:hover {
    transform: translateY(-2px);
    box-shadow: 0 5px 20px rgba(102, 126, 234, 0.4);
}

#addButton:active {
    transform: translateY(0);
}

#todoList {
    display: flex;
    flex-direction: column;
    gap: 10px;
}

.todo-item {
    background: #f8f9fa;
    padding: 15px 20px;
    border-radius: 10px;
    display: flex;
    justify-content: space-between;
    align-items: center;
    animation: slideIn 0.3s ease;
    transition: all 0.3s;
}

.todo-item:hover {
    background: #f1f3f5;
    transform: translateX(5px);
}

.todo-text {
    font-size: 1.1rem;
    color: #333;
}

.delete-btn {
    background: #ff6b6b;
    color: white;
    border: none;
    padding: 6px 15px;
    border-radius: 8px;
    cursor: pointer;
    font-size: 0.9rem;
    transition: background 0.3s, transform 0.2s;
}

.delete-btn:hover {
    background: #ff5252;
    transform: scale(1.05);
}

#loading {
    text-align: center;
    color: #667eea;
    font-size: 1.2rem;
    margin-top: 20px;
}

@keyframes slideIn {
    from {
        opacity: 0;
        transform: translateY(-10px);
    }
    to {
        opacity: 1;
        transform: translateY(0);
    }
}

.empty-state {
    text-align: center;
    color: #999;
    padding: 40px 0;
    font-size: 1.1rem;
}
```

### `frontend/app.js`
```javascript
// API Configuration
const API_URL = 'https://your-api-id.execute-api.region.amazonaws.com/prod/todos';

// DOM Elements
const todoInput = document.getElementById('todoInput');
const addButton = document.getElementById('addButton');
const todoList = document.getElementById('todoList');
const loading = document.getElementById('loading');

// Fetch all todos from the API
async function fetchTodos() {
    try {
        loading.style.display = 'block';
        const response = await fetch(API_URL);
        
        if (!response.ok) {
            throw new Error('Failed to fetch todos');
        }
        
        const todos = await response.json();
        renderTodos(todos);
    } catch (error) {
        console.error('Error fetching todos:', error);
        todoList.innerHTML = '<div class="empty-state">⚠️ Failed to load todos. Please try again.</div>';
    } finally {
        loading.style.display = 'none';
    }
}

// Add a new todo
async function addTodo() {
    const text = todoInput.value.trim();
    if (!text) {
        alert('Please enter a todo item');
        return;
    }

    try {
        loading.style.display = 'block';
        const response = await fetch(API_URL, {
            method: 'POST',
            headers: {
                'Content-Type': 'application/json',
            },
            body: JSON.stringify({ text, completed: false }),
        });

        if (!response.ok) {
            throw new Error('Failed to add todo');
        }

        todoInput.value = '';
        await fetchTodos(); // Refresh the list
    } catch (error) {
        console.error('Error adding todo:', error);
        alert('Failed to add todo. Please try again.');
    } finally {
        loading.style.display = 'none';
    }
}

// Delete a todo
async function deleteTodo(id) {
    if (!confirm('Are you sure you want to delete this todo?')) {
        return;
    }

    try {
        loading.style.display = 'block';
        const response = await fetch(`${API_URL}/${id}`, {
            method: 'DELETE',
        });

        if (!response.ok) {
            throw new Error('Failed to delete todo');
        }

        await fetchTodos(); // Refresh the list
    } catch (error) {
        console.error('Error deleting todo:', error);
        alert('Failed to delete todo. Please try again.');
    } finally {
        loading.style.display = 'none';
    }
}

// Render todos to the DOM
function renderTodos(todos) {
    if (!todos || todos.length === 0) {
        todoList.innerHTML = '<div class="empty-state">✨ No todos yet. Add one above!</div>';
        return;
    }

    todoList.innerHTML = todos.map(todo => `
        <div class="todo-item" data-id="${todo.id}">
            <span class="todo-text">${escapeHtml(todo.text)}</span>
            <button class="delete-btn" onclick="deleteTodo('${todo.id}')">Delete</button>
        </div>
    `).join('');
}

// Helper function to escape HTML
function escapeHtml(text) {
    const div = document.createElement('div');
    div.textContent = text;
    return div.innerHTML;
}

// Event Listeners
addButton.addEventListener('click', addTodo);
todoInput.addEventListener('keypress', (e) => {
    if (e.key === 'Enter') {
        addTodo();
    }
});

// Initialize the app
fetchTodos();
```

## 2. Backend (Lambda Function)

### `backend/lambda-function/index.js`
```javascript
const AWS = require('aws-sdk');
const dynamodb = new AWS.DynamoDB.DocumentClient();
const { v4: uuidv4 } = require('uuid');

// Replace with your DynamoDB table name
const TABLE_NAME = 'TodosTable';

exports.handler = async (event) => {
    try {
        const httpMethod = event.httpMethod;
        const path = event.path;
        const pathParameters = event.pathParameters || {};

        // CORS headers
        const headers = {
            'Access-Control-Allow-Origin': '*',
            'Access-Control-Allow-Methods': 'GET, POST, DELETE, OPTIONS',
            'Access-Control-Allow-Headers': 'Content-Type',
        };

        // Handle preflight OPTIONS request
        if (httpMethod === 'OPTIONS') {
            return {
                statusCode: 200,
                headers,
                body: '',
            };
        }

        // Route handling
        if (httpMethod === 'GET' && path === '/todos') {
            // GET all todos
            const result = await dynamodb.scan({
                TableName: TABLE_NAME,
            }).promise();

            return {
                statusCode: 200,
                headers,
                body: JSON.stringify(result.Items),
            };
        }

        if (httpMethod === 'POST' && path === '/todos') {
            // POST new todo
            const body = JSON.parse(event.body);
            const todo = {
                id: uuidv4(),
                text: body.text,
                completed: body.completed || false,
                createdAt: new Date().toISOString(),
            };

            await dynamodb.put({
                TableName: TABLE_NAME,
                Item: todo,
            }).promise();

            return {
                statusCode: 201,
                headers,
                body: JSON.stringify(todo),
            };
        }

        if (httpMethod === 'DELETE' && path.startsWith('/todos/')) {
            // DELETE todo by ID
            const id = pathParameters.id;
            
            await dynamodb.delete({
                TableName: TABLE_NAME,
                Key: { id },
            }).promise();

            return {
                statusCode: 200,
                headers,
                body: JSON.stringify({ message: 'Todo deleted successfully' }),
            };
        }

        // Return 404 for unknown routes
        return {
            statusCode: 404,
            headers,
            body: JSON.stringify({ error: 'Not found' }),
        };
    } catch (error) {
        console.error('Error:', error);
        return {
            statusCode: 500,
            headers: {
                'Access-Control-Allow-Origin': '*',
                'Content-Type': 'application/json',
            },
            body: JSON.stringify({ error: 'Internal server error' }),
        };
    }
};
```

### `backend/lambda-function/package.json`
```json
{
  "name": "todo-lambda",
  "version": "1.0.0",
  "description": "Lambda function for Todo app",
  "main": "index.js",
  "dependencies": {
    "aws-sdk": "^2.1500.0",
    "uuid": "^9.0.0"
  }
}
```

## 3. Infrastructure

### `infrastructure/s3-bucket-policy.json`
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "PublicReadGetObject",
      "Effect": "Allow",
      "Principal": "*",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::your-bucket-name/*"
    }
  ]
}
```

## 4. DynamoDB Table Schema

Create a DynamoDB table with:
- **Table Name**: `TodosTable`
- **Partition Key**: `id` (String)

## 5. API Gateway Configuration

1. Create a REST API in API Gateway
2. Create resources and methods:
   - `GET /todos` → Lambda integration
   - `POST /todos` → Lambda integration
   - `DELETE /todos/{id}` → Lambda integration
3. Enable CORS on all methods
4. Enable the `Lambda proxy integration`, which Send the request to your Lambda function as a structured event.
5. Deploy the API to a stage (e.g., `prod`)
6. Copy the API endpoint URL

**under the API Gateway, you can use the `Test` tab when you click the methods to test whether the api can connect to the backend Lambda function. And confirm the pipeline is good. This is a good way to troubleshoot the issue.**

## 6. Deployment Steps

### Step 1: Create S3 Bucket
```bash
# Create bucket (replace with your unique bucket name)
aws s3 mb s3://your-todo-app-bucket --region us-east-1

# Enable static website hosting
aws s3 website s3://your-todo-app-bucket \
    --index-document index.html \
    --error-document error.html

# Apply bucket policy
aws s3api put-bucket-policy \
    --bucket your-todo-app-bucket \
    --policy file://infrastructure/s3-bucket-policy.json
```

### Step 2: Upload Frontend Files
```bash
aws s3 sync frontend/ s3://your-todo-app-bucket/ \
    --acl public-read
```

### Step 3: Deploy Lambda Function
```bash
cd backend/lambda-function
npm install
zip -r function.zip index.js node_modules package.json

# Create Lambda function
aws lambda create-function \
    --function-name todo-api \
    --runtime nodejs18.x \
    --role arn:aws:iam::your-account-id:role/lambda-execution-role \
    --handler index.handler \
    --zip-file fileb://function.zip \
    --environment Variables={TABLE_NAME=TodosTable}

# Or update existing function
aws lambda update-function-code \
    --function-name todo-api \
    --zip-file fileb://function.zip
```

### Step 4: Update Frontend URL
Replace `API_URL` in `frontend/app.js` with your actual API Gateway endpoint:
```javascript
const API_URL = 'https://your-api-id.execute-api.us-east-1.amazonaws.com/prod/todos';
```

## 7. Testing Your App

1. Open your S3 bucket website URL: `http://your-todo-app-bucket.s3-website-us-east-1.amazonaws.com`
2. Add todos using the input field
3. Delete todos using the delete button
4. All data is persisted in DynamoDB

## 8. Environment Variables

Configure these in your Lambda function:
- `TABLE_NAME`: Your DynamoDB table name
- `AWS_REGION`: Your AWS region

## 9. IAM Permissions

You need to create the role `lambda-execution-role` with the following permissions

- AWS managed permission: `AWSLambdaBasicExecutionRole`
- AWS managed permission: `AWSLambdaVPCAccessExecutionRole`
- Customzied permission: `connect-to-dynamodb`. The following is very important

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "dynamodb:Scan",
                "dynamodb:PutItem",
                "dynamodb:DeleteItem",
                "dynamodb:GetItem",
                "dynamodb:UpdateItem",
                "dynamodb:Query"
            ],
            "Resource": "arn:aws:dynamodb:us-east-1:369759349891:table/TodosTable"
        }
    ]
}
```

This is a complete, working serverless application! The S3 bucket hosts your static website, Lambda handles the backend logic, and DynamoDB stores the data. All components are fully managed by AWS.




