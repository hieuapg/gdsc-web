summary: USF GDSC Fall 2024 Web Development Workshop
id: docs

# USF GDSC Fall 2024 Web Development Workshop

## Introduction

We're gonna make AI Chatbot with Gemini API and HTML/CSS/JS !

<img src="docs\img\intro.gif" width="270" height="600"> 
(Change image, add one for finished game)

## Setup screen

setup HTML file


index.html
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Gemini Chatbot</title>
</head>
<body>
    
</body>
</html> 
```

## Make HTML body

index.html:
1. add main components into <body> to build up the page structure (2 sections: input-button + response field)
2. assign ids to the elements

```html
    <header>
        <h1>Generative AI Chat</h1>
    </header>
    <main>
        <section>
            <h2>Ask a Question</h2>
            <form>
                <input type="text" id="query" placeholder="Type your question">
                <button type="button" id="myButton">Ask</button>
            </form>
        </section>
        <section>
            <h2>Response</h2>
            <div id="response-container">
                <!-- Response will be displayed here -->
            </div>
        </section>
    </main>
```

style.css:
1. create and link CSS file to HTML main page
2. edit CSS to format the styles of HTML elements

```css
body {
    font-family: Arial, sans-serif;
    margin: 0;
    padding: 0;
}

header {
    background: #333;
    color: #fff;
    padding: 1em;
    text-align: center;
}

main {
    padding: 1em;
}

form {
    margin-bottom: 1em;
}

input {
    padding: 0.5em;
    font-size: 1em;
}

button {
    padding: 0.5em;
    font-size: 1em;
    background: #007bff;
    color: #fff;
    border: none;
    cursor: pointer;
}

button:hover {
    background: #0056b3;
}

#response-container {
    border: 1px solid #ddd;
    padding: 1em;
    background: #f9f9f9;
}
```

## Setup Gemini API 

### Install and import Gemini API
1. Google AI for Developers (https://ai.google.dev/)
2. Navigate to Docs -> Quickstart -> Web(SDK)
3. Install Gemini API to HTML by CDN link
    
```html
<script type="importmap">
  {
    "imports": {
      "@google/generative-ai": "https://esm.run/@google/generative-ai"
    }
  }
</script>
```
4. Import Gemini API library  
```html
<script type="module">
    import { GoogleGenerativeAI } from "@google/generative-ai";

    // Fetch your API_KEY
    const API_KEY = "...";
</script>
```

### Retrieve API Key
1. Navigate to Google AI Studio -> Get API Key
2. Select "Generative Language Client" -> Create Key
3. Copy Key to API_KEY (keep API_KEY secret!)

## Setup structure to make the requests

index.html:
1. setup async function generate() for requests
2. add event listener for request command

```html
<script type="module">
    document.getElementById('myButton').addEventListener('click', generate); // Event listener
    import { GoogleGenerativeAI } from "@google/generative-ai";

    const API_KEY = "...";
    const genAI = new GoogleGenerativeAI(API_KEY);
    async function generate() <!-- requests function --> {
        const model = genAI.getGenerativeModel({ model: "gemini-1.5-flash" });
    
        const prompt = "Write a story about a magic backpack.";
    
        const result = await model.generateContent(prompt);
        const response = await result.response;
        let text = response.text(); 
    
        console.log(text);
            }
</script>
```

Test the function, it should display on the console if correct

## Modify to get users' prompts and display results on page

index.html:
1. modify prompt to value of id 'query'
2. add text to id 'response-container

```html
<script type="module">
    document.getElementById('myButton').addEventListener('click', generate); // Event listener
    import { GoogleGenerativeAI } from "@google/generative-ai";

    const API_KEY = "...";
    const genAI = new GoogleGenerativeAI(API_KEY);
    async function generate() <!-- requests function --> {
        const model = genAI.getGenerativeModel({ model: "gemini-1.5-flash" });
    
        const prompt = document.getElementById('query').value; // Take users' inputs as prompt
    
        const result = await model.generateContent(prompt);
        const response = await result.response;
        let text = response.text();

        document.getElementById('response-container').innerHTML = text; // Add response to page
        console.log(text);
            }
</script>
```

Test the functions. It should answers users' questions and display it on the screen

## Add formatting to answer 

```javascript:
  text = text
        .replace(/##\s*(.*?)\s*\n/g, "<h2>$1</h2><br>")
        .replace(/\*\*(.*?)\*\*/g, "<strong>$1</strong>")
        .replace(/\n/g, "<br>");

```
Script: 

```html
<script type="module">
        document.getElementById('myButton').addEventListener('click', generate);
        import { GoogleGenerativeAI } from "@google/generative-ai";
        const API_KEY = "...";
        const genAI = new GoogleGenerativeAI(API_KEY);

        async function generate() {
            const model = genAI.getGenerativeModel({ model: "gemini-pro" });

            const prompt = document.getElementById('query').value;

            const result = await model.generateContent(prompt);
            const response = await result.response;
            let text = response.text(); 

            // Replace for proper display
            text = text
                .replace(/##\s*(.*?)\s*\n/g, "<h2>$1</h2><br>")
                .replace(/\*\*(.*?)\*\*/g, "<strong>$1</strong>")
                .replace(/\n/g, "<br>");

            document.getElementById('response-container').innerHTML = text;
            console.log(text);
        }
    </script>
```

## All files check: 
index.html:
```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Generative AI Chat</title>
    <link rel="stylesheet" href="style.css">
</head>

<body>
    <script type="importmap">
        {
          "imports": {
            "@google/generative-ai": "https://esm.run/@google/generative-ai"
          }
        }
    </script>
    <header>
        <h1>Generative AI Chat</h1>
    </header>
    <main>
        <section id="query-section">
            <h2>Ask a Question</h2>
            <form id="query-form">
                <input type="text" id="query" placeholder="Type your question">
                <button type="button" id="myButton">Ask</button>
            </form>
        </section>
        <section id="response-section">
            <h2>Response</h2>
            <div id="response-container">
                <!-- Response will be displayed here -->
            </div>
        </section>
    </main>
    <script type="module">
        document.getElementById('myButton').addEventListener('click', generate);
        import { GoogleGenerativeAI } from "@google/generative-ai";
        const API_KEY = "...";
        const genAI = new GoogleGenerativeAI(API_KEY);

        async function generate() {
            const model = genAI.getGenerativeModel({ model: "gemini-pro" });

            const prompt = document.getElementById('query').value;

            const result = await model.generateContent(prompt);
            const response = await result.response;
            let text = response.text(); 

            // Replace newlines with <br> for proper display
            text = text
                .replace(/##\s*(.*?)\s*\n/g, "<h2>$1</h2><br>")
                .replace(/\*\*(.*?)\*\*/g, "<strong>$1</strong>")
                .replace(/\n/g, "<br>");

            document.getElementById('response-container').innerHTML = text;
            console.log(text);
        }
    </script>
</body>
</html>
```

style.css:
```css
body {
    font-family: Arial, sans-serif;
    margin: 0;
    padding: 0;
}

header {
    background: #333;
    color: #fff;
    padding: 1em;
    text-align: center;
}

main {
    padding: 1em;
}

form {
    margin-bottom: 1em;
}

input {
    padding: 0.5em;
    font-size: 1em;
}

button {
    padding: 0.5em;
    font-size: 1em;
    background: #007bff;
    color: #fff;
    border: none;
    cursor: pointer;
}

button:hover {
    background: #0056b3;
}

#response-container {
    border: 1px solid #ddd;
    padding: 1em;
    background: #f9f9f9;
}
```
Good job!!!.
