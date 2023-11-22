
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
 * @property {string} classNamePath - Path of the field which defines the classname of section.
 */
export interface configDataType {
  url?: string;
  blocksPath?: string;
  sectionPath?: string;
  HTMLPathInBlock?: string;
  visibilityPath?: string;
  classNamePath?: string

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
    visibilityPath : "ele.Active",
    classNamePath : "ele.ClassName"
}
```
#### See Paths in the Image.
![PathsStrapi](https://github.com/dippinchachan123/Strapi_FrontEnd/assets/96717673/2f34372f-7858-46fe-a862-853f65a3f2d0)


### Set Default Strapi Configuration

In your entry point file (e.g., Index.tsx), set the default Strapi configuration using the Strapi.setConfig(config) method. This will configure the Strapi instance globally for your application.

```javascript

//Strapi Code
import { Strapi } from 'strapi-frontend';
import { config } from './Strapi/strapi';
Strapi.setConfig(config);

const root = ReactDOM.createRoot(
  document.getElementById('root') as HTMLElement
);

root.render(
  <React.StrictMode>
    <Homepage />
  </React.StrictMode>
);

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

```typescript
const [components, setComponents] = useState(null);
```


#### Mount Components for Sections.

You can use the mountComponent function to dynamically render section components in your React application. This function takes two arguments:

    Section ID: This is a unique identifier for the section you want to render. It helps the function know which component to render.

    Component Function: This is a JavaScript function that returns a React component. It takes two arguments:

        Data: This is the data Object fetched from the API for the specific section. It's the object which has content you want to display in the component.

        Address (Addrs): This is the domain address of your Strapi Portal. It's used to construct URLs for any additional resources you might need in your component, like images or links.

Here's an example of how you can use the mountComponent function:

```typescript
strapi.mountComponent("Section-1", (data, addrs) => {
  // Define how to render Section-1 based on the data
  // Return JSX for Section-1
});

// Repeat the above step for other sections (Section-2, Section-3, etc.)
```
![Strapi_Sections](https://github.com/dippinchachan123/Strapi_FrontEnd/assets/96717673/feab51f8-56c0-4c9b-adeb-134b3c659191)


##### Example : 

```typescript
function mountComponents(starpi : Strapi){

  strapi.mountComponent("Section-5", (data: any, addrs: string) => {
          return (
              <Grid container>
                  {data.Relations.comments.data.map((item: any) => {
                    let rating = item.attributes.rating;
                    let classname = rating + " mt-60";
                    classname = "review_div " + classname;
                    return (
                      <Grid xs={6}>
                          <div className={classname}>
                        <span><img src={addrs + item.attributes.PersonImage.data.attributes.formats.thumbnail.url} alt="" /></span>
                        <p>{item.attributes.Comment}</p>
                          </div>
                      </Grid>)			}
                  )}
              </Grid>
          )
  })
  //Mount more components ...
}
```

#### Mount Components for Non-Block Data.

You can use the mountComponent function to dynamically render Non-Block components in your React application. This function takes two arguments:

    Path To Data : This is a unique identifier for the data you want to render.Provide proper Path to the data.

    Component Function: This is a JavaScript function that returns a React component. It takes two arguments:

        Data: This is the data Object fetched from the API for the specific path given at first Argument.

        Address (Addrs): This is the domain address of your Strapi Portal. It's used to construct URLs for any additional resources you might need in your component, like images or links.

        shift : 1 or -1(default) (1 will render the components above the Block Data | -1 below the Block data)

Here's an example of how you can use the mountComponent function:

```typescript
function mountComponents(starpi : Strapi){
  //....Others Components
  //Non-Block Components
  strapi.mountComponent("data.attributes.Blocks[7].Content.content", (data, addrs) => {
    // Define how to mount Componet based on the data
    // Return JSX for data
  });
  
  strapi.mountComponent("data.attributes.Blocks[0].Label", (data, addrs) => {
    // Define how to mount Componet based on the data
    // Return JSX for data
  },1);
}
```

##### Example : 

```typescript
function mountComponents(starpi : Strapi){

  strapi.mountComponent("data.attributes.Blocks[5]", (data: any, addrs: string) => {
          return (
              <Grid container>
                  {data.Relations.comments.data.map((item: any) => {
                    let rating = item.attributes.rating;
                    let classname = rating + " mt-60";
                    classname = "review_div " + classname;
                    return (
                      <Grid xs={6}>
                          <div className={classname}>
                        <span><img src={addrs + item.attributes.PersonImage.data.attributes.formats.thumbnail.url} alt="" /></span>
                        <p>{item.attributes.Comment}</p>
                          </div>
                      </Grid>)			}
                  )}
              </Grid>
          )
  })
  strapi.mountComponent("data.attributes.Blocks[0].Label", (data, addrs) => {
        return (
          <div className = "Facts">
                Hello {data.title}!!
          </div> 
        )
  },1);
  //Mount more components ...
}
```


#### Create a Strapi Instance and provide the path of corresponding page and fetch Data and Render Components.

```typescript
useEffect(() => {
  const strapi = new Strapi('/api/pages/6?populate=deep');
  mountComponents(starpi)//To mount all components
  strapi.configure()
    .then(res => setComponents(strapi.render()))
    .catch(err => console.log("Error in fetching data from Strapi portal:", err))
}, []);
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

## Example of Page Integrated with Strapi-frontend
```javascript
import React from 'react';
import Grid from "@mui/material/Unstable_Grid2";
import { Box, Link, Rating } from "@mui/material";
import './Homepage.scss'

//Added
import { useEffect, useState } from "react";
import axios from "axios";

//Strapi Integration
import { Strapi } from 'strapi-frontend';

function Homepage() {
    const [components, setComponents] = useState<any>(null);
    //...................................................................................................................
    //Strapi-Integration

    function mountComponents(strapi : any){

        strapi.mountComponent("Section-1", (data: any) => {
            return (
                <div className="header_outer">
                    <Grid container spacing={0}>
                        <Grid xs={12}>
                            {/* header */}
                            <Grid container spacing={0}>
                                <Grid xs={12} className="header">
                                    <h1>{data.Title[0].text.split(":")[0]}<span>{data.Title[0].text.split(":")[1]}</span></h1>
                                    <Grid container spacing={0}>
                                        <Grid xs={6}>
                                            <Grid container spacing={3}>
                                                <Grid xs={6} >
                                                    <Link className="product_outer" onClick={handleOpenHealth}>
                                                        <div className="product_sec health">
                                                            <h5>Health<br /> Insurance</h5>
                                                        </div>
                                                    </Link>
                                                </Grid>
                                                <Grid xs={6}>
                                                    <Link className="product_outer" onClick={handleOpen}>
                                                        <div className="product_sec term">
                                                            <h5>Term<br /> Insurance</h5>
                                                        </div>
                                                    </Link>
                                                </Grid>
                                            </Grid>
                                            <Grid container spacing={3}>
                                                <Grid xs={12} className='pt-6'>
                                                    <Link className="product_outer" onClick={handleOpenTravel}>
                                                        <div className="travel_product_sec">
                                                            <h5>Air<br /> Insurance</h5>
                                                        </div>
                                                    </Link>
                                                </Grid>
                                            </Grid>
                                        </Grid>
                                        <Grid xs={6}>
                                            <Grid container spacing={3}>
                                                <Grid xs={6} >
                                                    <Link className="product_outer" onClick={handleOpenCar}>
                                                        <div className="car_product_sec">
                                                            <h5>Car<br /> Insurance</h5>
                                                        </div>
                                                    </Link>
                                                </Grid>
                                                <Grid xs={6}>
                                                    <Link className="product_outer" onClick={handleOpenBike}>
                                                        <div className="product_sec bike">
                                                            <h5>Bike<br /> Insurance</h5>
                                                        </div>
                                                    </Link>
                                                    <Link className="product_outer" onClick={handleOpenInvestment}>
                                                        <div className="product_sec investment  mt-6">
                                                            <h5>Family<br /> Insurance</h5>
                                                        </div>
                                                    </Link>
                                                </Grid>
                                            </Grid>

                                        </Grid>
                                    </Grid>
                                    <Grid container >
                                        <Grid xs={12} className="others_product_sec ml-3 mr-3">
                                            <ul>
                                                <li>Others:</li>
                                                {
                                                    [<li><a>Marine Insurance</a></li>,
                                                    <li><a>Fuel Insurance</a></li>,
                                                    <li><a>Fire Insurance</a></li>]
                                                }
                                            </ul>
                                        </Grid>
                                    </Grid>
                                </Grid>
                            </Grid>
                        </Grid>
                    </Grid>
                </div>
            )
        })

        strapi.mountComponent("Section-2", (data: any) => {
            console.log("Data - Section-2 : ", data)

            return (
                <Grid container className="factsheet">
                    <Grid xs={12} className="textCenter">
                        <Grid container>
                            <Grid xs={12} className="mb-10">
                                <h2>{data.Title[0].text.split(":")[0]}</h2>
                                <p>{data.Title[0].text.split(":")[1]}</p>
                            </Grid>
                        </Grid>
                        <Grid container spacing={3}>
                            {
                                data.Cards.map(
                                    (item: any) => (
                                        <Grid xs={3}>
                                            <div className="counter_sec">
                                                <h4><span className="counter">{item.title}</span>+</h4>
                                                <p className="mb-0">{item.text}</p>
                                            </div>
                                        </Grid>
                                    )
                                )
                            }
                        </Grid>
                    </Grid>
                </Grid>
            )
        })

        strapi.mountComponent("Section-3", (data: any) => {
            return (
                <Grid container className="feature_sec">
                    <Grid xs={12}>
                        <Grid container spacing={3}>
                            <Grid xs={3}>
                                <img src="./images/feature_sec_img.svg" alt="" className="img-fluid" />
                            </Grid>
                            <Grid xs={9}>
                                <h2>{data.Title[0].text.split(":")[0]}<br /> {data.Title[0].text.split(":")[1]}</h2>
                                <Grid container spacing={0}>
                                    <Grid xs={12} className='divider' padding={0}></Grid>
                                </Grid>
                                <Grid container spacing={0}>
                                    {
                                        data.Cards.map(
                                            (item: any) => (
                                                <Grid xs={4}>
                                                    <div className="feature_desc mb-6">
                                                        <span className={item.title.split(":")[0]}></span>
                                                        <h5>{item.lable} <br />{item.title.split(":")[1]}</h5>
                                                        <p>{item.text}</p>
                                                    </div>
                                                </Grid>
                                            )
                                        )
                                    }
                                </Grid>
                            </Grid>
                        </Grid>
                    </Grid>

                </Grid>
            )
        })

        strapi.mountComponent("Section-4", (data: any) => {
            return (
                <div className='awards_sec_outer'>
                    <Grid container>
                        <Grid xs={12}>
                            <Grid container className="mb-5">
                                <Grid xs={3}>
                                    <h3>Awards &<br /> Recognition </h3>
                                </Grid>
                                <Grid xs={9}>
                                    {
                                        data.Relations.awards.data.map(
                                            (item: any) => (
                                                <Grid xs={9}>
                                                    <p>{item.attributes.Award}</p>
                                                </Grid>

                                            )
                                        )
                                    }
                                </Grid>
                            </Grid>
                            <Grid container>
                                <Grid xs={12} className="divider"></Grid>
                            </Grid>

                        </Grid>
                    </Grid>
                </div>
            )
        })

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

        strapi.mountComponent("Section-6", (data: any, addrs: string) => {
            return (

                <Grid container className='partners_section'>
                    <Grid xs={12}>
                        <Grid container>
                            <Grid xs={12} className="mb-10">
                                <h2>Our Partners</h2>
                            </Grid>
                        </Grid>
                        <Grid container>
                            <Grid xs={12}>
                                <ul className="partnersLogo">
                                    {
                                        data.Relations.partners.data.map(
                                            (item: any) => {
                                                return (<li><img src={addrs + item.attributes.Image.data.attributes.url} alt="insurer" /></li>)
                                            }
                                        )
                                    }
                                </ul>
                            </Grid>
                        </Grid>
                    </Grid>
                </Grid>

            )
        })
    }

    useEffect(() => {
        const strapi = new Strapi('/api/pages/6?populate=deep')//Using the global config we set in App.ts file
        mountComponents(strapi);
        strapi.configure()
            .then(res => setComponents(strapi.render()))
            .catch(err => console.log("Error in fetching data from strapi portal :", err))
    }, [])

    //...................................................................................................................



    return (
        <Box className="homePageWrapper">
            {components}
        </Box>
    )
}

export default Homepage

```












