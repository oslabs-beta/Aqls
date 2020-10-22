# Aqls - Server


## Overview
GraphQL analytics toolkit and dashboard that integrate with Apollo Client and Apollo Server Express. It is an easy to use analytics suite that monitors GraphQL subscriptions concurrency, latency, errors, and resolver frequency. Our integrated dashboard displays your GraphQL analytics on dynamic and interactive charts. 

This package is for setting up your server. For the client-side package please refer to the [Aqls-client](https://github.com/oslabs-beta/Aqls-client) package.

**Note:** Aqls is currently in BETA and improvements will continue to be implemented. If any issues are encountered while using our application, please submit a PR. 


## Requirements
- **Node.js** *version* 12.18.3+
- **apollo-server-express** *version* 2.18.1+

## Install
With npm:

```
npm install --save @aqls/server
```

## Getting Started

####
- [ ] **1**. Import Traql, traqlAudit, and analyticsRouter into your server file:

```javascript
const { Traql, traqlAudit, analyticsRouter } = require('@aqls/server');
```
#### 

- [ ] **2**. Initialize a constant for your resolvers:
```javascript
const resolvers = { Mutation, Query, Subscription };
 ```
####
- [ ] **3**. Create a new instance of Traql passing in the resolvers and your user token:
```javascript
const traql = new Traql(resolvers, 'INSERT USER TOKEN HERE IN QUOTES');
```
This will keep track of the number of subscription resolvers in the system and will calculate the number of current subscribers. You can get your User Token from [Aqls.io](https://www.aqls.io/) by signing up through Github OAuth. This token is needed to view your analytics in the developer dashboard at Aqls.io. 
####
- [ ] **4**. Add to your Apollo Server: 
```javascript
const server = new ApolloServer({
  typeDefs,
  resolvers,
  context: { db, pubsub, traql },
});
```
Make sure that *context* is added to your server. Be sure to pass in **BOTH** *pubsub* and *traql* to server context.
 ####
- [ ] **5**. Invoke the traqlAudit function passing in traql: 
```javascript
setInterval(() => traqlAudit(traql), 5000);
```
traqlAudit is invoked every 5 seconds to audit each traql entry and ensure that there are no errors before sending the data to be displayed on the dashboard.
 ####
- [ ] **6**. Instantiate an endpoint to send analytics to Aqls server:
```javascript
app.use('/aqlsanalytics', analyticsRouter(traql));
```
**Note:** Please ensure the endpoint is *'/aqlsanalytics'* and invoke *analyticsRouter* passing in *traql*.
###
###
## Update Schema and Mutation Tracking
- [ ] **1**. Add AQL and AQLInput to schema file:
```graphQL
type AQL {
  mutationSendTime: String,
  mutationReceived: String,
  subscriberReceived: String,
  mutationId: ID,
  resolver: String,
  userToken: String,
}
input AQLInput {
  mutationSendTime: String,
  mutationReceived: String,
  subscriberReceived: String,
  mutationId: ID,
  resolver: String,
  userToken: String,
}
```
###
- [ ] **2**. Add AQLInput type to any mutation type that you want to track:
```graphQL
type Mutation {
  newColor(colorArg: String, aql: AQLInput): Color
  newLuckyNumber(numberArg: Int, aql: AQLInput): LuckyNumber
}
```
###
- [ ] **3**. Add AQL type to any payload that you want to track:
```graphQL
type UpdatedColorPayload {
  cssColor: String!
  id: ID!
  aql: AQL
}
```
###
###
### Mutation Tracking
- [ ] **1**. Import newAqlPayload in mutation resolver file: 
```javascript
const { newAqlPayload } = require('@aqls/server');
```
- [ ] **2**. Pass invocation of newAqlPayload to pubsub publish property:
```javascript
function newColor(parent, args, { db, pubsub, traql }, info) {
  db.color.cssColor = args.colorArg;
  const payload = {
    updatedColor: {
      ...db.color,
    },
  };
  pubsub.publish('COLOR_MUTATED', newAqlPayload(payload, args, traql, pubsub));
  return db.color;
}
```
**Note:** Pass in payload as first argument, be sure to include resolver arguments and the traql and pubsub from server context.
####
####
- [ ] **Lastly, Connect with the Aqls Team!**

aqlorgteam@gmail.com

Case Simmons: [Case's Github](https://github.com/casesimmons) and [Case's LinkedIn](https://www.linkedin.com/in/case-simmons/)

Julie Pinchak: [Julie's Github](https://github.com/jpinchak) and [Julie's LinkedIn](https://www.linkedin.com/in/julie-pinchak/)

Michael O'Halloran: [Michael's Github](https://github.com/LordRegis22) and [Michael's LinkedIn](https://www.linkedin.com/)

Rocio Infante: [Rocio's Github](https://github.com/Rocio-Infante) and [Rocio's LinkedIn](https://www.linkedin.com/in/rocio-infante/)
