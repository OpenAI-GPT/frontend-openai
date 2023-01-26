# How to setup React Next APP

## Setup

- create react next app

```
mkdir appname
cd appname
npx create-next-app .
```

## 20. ChatGPT API Key for OpenAI and Environment Security Protocol

- '.env'
```
OPENAI_API_KEY="copy your OpenAI API key here"
```

- install dotenv `npm i dotenv`

- update `generate.js`
```
import * as dotenv from 'dotenv';

dotenv.config({path:__dirname + '/.env'});

const { Configuration, OpenAIApi } = require("openai");

// ...
// code before
```

## 21. Important: CSS File Needed to Continue with This Section

-   [index.module.css](https://beatlesm.s3.us-west-1.amazonaws.com/openai-front/index.module.css)

## 24. Call and Response Example for OpenAI Implementation and Customizations

1. install openai - `npm i openai`

1.  move `generate.js` under api then update it
```
import * as dotenv from 'dotenv';
import { Configuration, OpenAIApi  } from 'openai';

dotenv.config({path:__dirname + '/.env'});

const configuration = new Configuration({
  apiKey: process.env.OPENAI_API_KEY,
});

const openai = new OpenAIApi(configuration);

export default async function(req, res) {

  if(!configuration.apiKey) {
    res.status(500).json ({
      error: {
        message: "OpenAI API key not configured"
      }
    });
    return;
  }
  
  const animal = req.body.animal || '';

  const response = await openai.createCompletion({
    model: "text-davinci-003",
    prompt: `suggest three pet names for the follow ${animal}`,
    temperature: 0.8,
    max_tokens: 100,    
  });
  res.status(200).json({result: response.data.choices[0].text})
}
```

3.  `index.jsx`
```
import Head from 'next/head';
import { useState } from 'react';
import styles from "./index.module.css";

export default function Home() {

  const [count, setCounter] = useState(0);
  const [animalInput, setAnimalInput] = useState("");
  const [result, setResult] = useState();

  async function onSubmit(e) {    

      e.preventDefault();

      try {
        if (count == 10) {
          return console.log('you have reached your limit');
        };      
  
        const response = await fetch("/api/generate", {
          method: "POST",
          headers: {
            "Content-Type": "application/json",
          },
          body: JSON.stringify({animal: animalInput})
        });
  
        const data = await response.json();
        if(response.status !== 200) {
          throw data.error || new Error(`Requst failed with status ${response.status}`);
        }
         
        setResult(data.result);
        setCounter(count + 1);
        setAnimalInput("");

      } catch(error){
        console.error(error)
        alert(error.message)
      }
      
    }

  return (
      <div  className={styles.body} >
        <Head>
          <title>Create Next App</title>
          <link rel="icon" href="/favicon.ico" />
        </Head>
        <main className={styles.main} >
          <img src='/favicon.ico' className={styles.icon} />
          <h3>Name My Pet</h3>
          <form onSubmit = {onSubmit}>
            <input 
            type = 'text'
            name = 'animal'
            value = {animalInput}
            onChange = {(e)=>{
                setAnimalInput(e.target.value);
                console.log(animalInput);
              }
            }
            placeholder = 'Enter an animal'
            />
            <input
              type = "submit" 
              value="Generate names" />
          </form>
          <div className={styles.result} >{result}</div>
        </main>        
      </div>      
  )
}
```
