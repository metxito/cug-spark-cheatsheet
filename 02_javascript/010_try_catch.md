# new Try-Catch operator ?=

## Old way

```javascript
async function loadConfig(){
    try{
        const fileContents = await readFile("config.json");
        try {
            const config = JSON.parse(fileContents);
            return config;
        }
        catch (parseError){
            console.error('Error parsing JSON: ', parseError);
        }
    }
    catch (readError){
        console.error('Error reading file: ', readError);
    }
}
```

## New Way

```javascript
const [readError, fileContents] ?= await readFile("config.json");
const [parseError, config] = fileContents ? JSON.parse(fileContents) ?= : [new Error("No file contents"), null];

if (readError){
    console.error("Error reading file: ', readFile);
}
else if (parseError){
    console.error("Error parsing JSON: ', parseError);
}
else{
    console.log('Configuration loaded successfully: ', config);
}

```

## Why ?= is a Game-Changer for JavaScript

- Cleaner Code: With ?=, you can handle multiple potential errors in a single line, without using extra try-catch blocks.
- Centralized Error Handling: Instead of spreading error-handling code across different parts of your function, you keep everything together, making it easier to read.
- Better Performance: With fewer try-catch layers, the code runs more efficiently.
- Easy Async Handling: For async functions, the ?= operator lets you handle errors more simply without sacrificing functionality, especially helpful for APIs and web applications.
- Before and After: A Side-by-Side Comparison
