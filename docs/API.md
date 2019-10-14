# ![logo](/docs/branding.png) Telemetry Analytics Data Persistence

### Table of Contents
- [**Introduction**](/README.md)<br>
- [**Installation**](/docs/Installation.md)<br>
- [**Seeding Data**](/docs/SeedData.md)<br>
- **API**<br>
- [**Previous Versions**](/docs/PreviousVersions.md)<br>

Influx Writer API is documented using Swagger and is available at ```<your-influx-writer-sevice-addresss>/swagger/index.html```. Example: ```http://localhost/swagger/index.html```. Refer to that for specific endpoints, models. 
Depending on the version of Swagger, the UI might look slightly different, but when you visit it you should be presented with a list of endpoints. Each endpoint will allow provide you with example model if one is used and explanation for each property of the model. Swagger also allows for trying out the endpoint in the browser. If any authentication is necessary, it can be usually set above the first listed endpoint on the right.

#### Flow of the API
1. Create a StreamingConfiguration 
2. Create a TopicConfiguration and reference the previously created StreamingConfiguration
3. Start/Stop TopicConfiguration if you haven't specified it to automatically start in step 2

With that set up, once you have data coming in the topic it should be written to the configured storages.