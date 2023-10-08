
# Strapi Frontend Integrater

Unlock the full potential of your Strapi-powered backend by seamlessly integrating its API data into your React frontend using the strapi-frontend  Library. 

This versatile toolkit empowers you to craft dynamic, user-friendly web experiences with minimal effort, thanks to a range of powerful features:

## Features
- Fetch and render content from the Strapi API with ease.
- Dragging feature to change the position of the sections on the homepage just by dragging the sections in strapi portal.
- Visibility feature to set the Visibility of the sections.
- Adding new sections using HTML or raw text and Image.

## Installation

```bash
npm install strapi-frontend
```

## Configuration

Provide a config object of type `configDataType` interface (which is one of the exported member of Library) to the individual object or you can also provide it to the class which will be used by all objects by Default.Outlining the available configuration options:

```typescript
/**
 * @interface configDataType
 * @description This interface represents the fields used in the Strapi configuration object. Default values are provided for each field.
 * @property {string} url - The domain name (e.g., "https://strapi.evervent.in/"). If using localhost, set it to 'https://localhost:{port}'.
 * @property {string} blocksPath - The path to the Blocks Array in your Strapi data.
 * @property {string} sectionPath - The path to the field that defines the section of a block.
 * @property {string} HTMLPathInBlock - The path to the field containing the HTML of an added block.
 * @property {string} visibilityPath - The path to the field that determines the visibility of a section.
 * @property {string} ... - Additional properties can be added here.
 */
export interface configDataType {
  url?: string;
  blocksPath?: string;
  sectionPath?: string;
  HTMLPathInBlock?: string;
  visibilityPath?: string;
}
```

## Usage

To integrate the Strapi Frontend Integrater library into your React application.

### Create a config object of type {configDataType}

Provide the corresponding fields based on strapi portal paths.
```typescript

import { configDataType } from "strapi-frontend/dist/Strapi";

export const config : configDataType = {
    url : "https://strapi.evervent.in",
    blocksPath : "data.attributes.Blocks",
    sectionPath : "ele.Section",
    HTMLPathInBlock : "ele.Content[0].content",
    visibilityPath : "ele.Active"
}

```

### Set Default Strapi Configuration

In your entry point file (e.g., App.jsx), set the default Strapi configuration using the Strapi.setConfig(config) method. This will configure the Strapi instance globally for your application.

```javascript
import React from 'react';
import './App.scss';
import { Strapi } from 'strapi-frontend';
import { config } from './Strapi/strapi';

// Set the default Strapi configuration here


function App() {
  Strapi.setConfig(config);
  // Your application code here
}

export default App;

```

### Integrate the strapi API with the page

#### Import the Necessary Modules: At the beginning of your file, import the necessary modules and components.

```typescript
import React, { useState, useEffect } from 'react';

//Strapi class
import { Strapi } from 'strapi-frontend';

import { TermForm, NonTermForm, TravelForm, HealthForm, CarForm, BikeForm } from './YourFormComponents';
import Navbar from './Navbar';
// Import any other components you might need
```

#### Set Up State for fetched Data through Strapi API.

```
const [components, setComponents] = useState(null);
```

#### Create a Strapi Instance and provide the path of corresponding page.

```
const strapi = new Strapi('/api/pages/6?populate=deep');
```

#### Fetch Data and Render Components.

```
useEffect(() => {
  strapi.configure()
    .then(res => setComponents(strapi.render()))
    .catch(err => console.log("Error in fetching data from Strapi portal:", err))
}, []);
```


#### Mount Components for Sections.

```
strapi.mountComponent("Section-1", (data, addrs) => {
  // Define how to render Section-1 based on the data
  // Return JSX for Section-1
});

// Repeat the above step for other sections (Section-2, Section-3, etc.)
```

##### Example : 

```
strapi.mountComponent("Section-5", (data: any, addrs: string) => {
        return (

            <Grid container className='customer_review'>
                <Grid xs={12}>
                    <Grid container className="mb-10">
                        <Grid xs={12}>
                            <div className="top-heading">
                                <h2>What customer say<br /> about us</h2>
                            </div>
                        </Grid>
                    </Grid>
                    <Grid container>
                        {
                            data.Relations.comments.data.map(
                                (item: any) => {
                                    let rating = item.attributes.rating;
                                    let classname = rating + " mt-60";
                                    classname = "review_div " + classname;
                                    return (
                                        <Grid xs={6}>
                                            <div className={classname}>
                                                <span><img src={addrs + item.attributes.PersonImage.data.attributes.formats.thumbnail.url} alt="" /></span>
                                                <p>{item.attributes.Comment}</p>
                                            </div>
                                        </Grid>)
                                }
                            )
                        }
                    </Grid>
                </Grid>
            </Grid>

        )
    })
```


#### Render the components.

```typescript
return (
  <div>
    {/* Render your various forms */}
    <Navbar />
    
    {/* Render the data received from Strapi */}
    {components}

  </div>
);
```









