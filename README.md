# Chainlink NodeJS External Adapter Template

This template provides a basic framework for developing Chainlink external adapters in NodeJS. Comments are included to assist with development and testing of the external adapter. Once the API-specific values (like query parameters and API key authentication) have been added to the adapter, it is very easy to add some tests to verify that the data will be correctly formatted when returned to the Chainlink node. There is no need to use any additional frameworks or to run a Chainlink node in order to test the adapter.

## Creating your own adapter from this template

Clone this repo and change "WeatherExternalAdapter" below to the name of your project

```bash
git clone https://github.com/PatrickAlphaC/CL-EA-NodeJS-Template.git -b weather-api WeatherExternalAdapter
```

Enter into the newly-created directory

```bash
cd WeatherExternalAdapter
```

You'll need an API key from the [OpenWeather API](https://home.openweathermap.org/api_keys). NOTE: It takes ~10 minutes for the key to register with the platform. 

See [Install Locally](#install-locallyquickstart) for a quickstart

See [Install Locally](#install-locally) for a quickstart

## Input Params

- `q `, `city`, or `town`: The city to get the weather for

## Output

```json
{"jobRunID":0,
"data":{
  "coord":{"lon":-71.06,"lat":42.36},
  "weather":[{"id":701,"main":"Mist","description":"mist","icon":"50n"}],
  "base":"stations",
  "main":{"temp":293.67,"feels_like":294.97,"temp_min":292.59,"temp_max":294.26,"pressure":1010,"humidity":94},
  "visibility":2414,
  "wind":{"speed":3.1,"deg":190},
  "clouds":{"all":90},
  "dt":1599095117,
  "sys":{"type":1,"id":3486,"country":"US","sunrise":1599041470,"sunset":1599088607},"timezone":-14400,"id":4930956,"name":"Boston","cod":200,"result":293.67},
  "result":293.67,
  "statusCode":200
}
```

# Install Locally/Quickstart

Install dependencies:

```bash
yarn
```

### Test

Run the local tests:

```bash
yarn test
```

Natively run the application (defaults to port 8080):

### Run

```bash
yarn start
```

### Call the external adapter/API server

```bash
curl -X POST -H "content-type:application/json" "http://localhost:8080/" --data '{"id": 0,"data":{"city": "Boston"}}'
```

## Docker

If you wish to use Docker to run the adapter, you can build the image by running the following command:

```bash
docker build . -t external-adapter
```

Then run it with:

```bash
docker run -p 8080:8080 -it external-adapter:latest
```

## Serverless hosts

After [installing locally](#install-locally):

### Create the zip

```bash
zip -r external-adapter.zip .
```

### Install to AWS Lambda

- In Lambda Functions, create function
- On the Create function page:
  - Give the function a name
  - Use Node.js 12.x for the runtime
  - Choose an existing role or create a new one
  - Click Create Function
- Under Function code, select "Upload a .zip file" from the Code entry type drop-down
- Click Upload and select the `external-adapter.zip` file
- Handler:
    - index.handler for REST API Gateways
    - index.handlerv2 for HTTP API Gateways
- Add the environment variable (repeat for all environment variables):
  - Key: API_KEY
  - Value: Your_API_key
  - Key: UNITS (optional)
  - Value: metric
- Save

#### To Set Up an API Gateway (HTTP API)

If using a HTTP API Gateway, Lambda's built-in Test will fail, but you will be able to externally call the function successfully.

- Click Add Trigger
- Select API Gateway in Trigger configuration
- Under API, click Create an API
- Choose HTTP API
- Select the security for the API
- Click Add

#### To Set Up an API Gateway (REST API)

If using a REST API Gateway, you will need to disable the Lambda proxy integration for Lambda-based adapter to function.

- Click Add Trigger
- Select API Gateway in Trigger configuration
- Under API, click Create an API
- Choose REST API
- Select the security for the API
- Click Add
- Click the API Gateway trigger
- Click the name of the trigger (this is a link, a new window opens)
- Click Integration Request
- Uncheck Use Lamba Proxy integration
- Click OK on the two dialogs
- Return to your function
- Remove the API Gateway and Save
- Click Add Trigger and use the same API Gateway
- Select the deployment stage and security
- Click Add

### Install to GCP

- In Functions, create a new function, choose to ZIP upload
- Click Browse and select the `external-adapter.zip` file
- Select a Storage Bucket to keep the zip in
- Function to execute: gcpservice
- Click More, Add variable (repeat for all environment variables)
  - NAME: API_KEY
  - VALUE: Your_API_key
  - NAME: UNITS (optional)
  - VALUE: metric
