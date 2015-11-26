# API Reference
This document lists the current available endpoints.

### POST /api/login 
##### Log in
### POST /api/reset-password 
##### Resets a users password
### POST /api/user 
##### Sign up
### GET /api
##### somethin
### GET /api/me 
##### Get current logged in user
### GET /api/models 
##### Get models available to a user
### GET /api/models/{id} 
##### Get model specified by ID
### GET /api/forecasts 
##### Get forecasts available to a user
### POST /api/forecasts 
##### Create a new forecast
### GET /api/forecasts/{id} 
##### retrieves the versions of a forecast
### GET /api/forecasts/{id}/latest 
##### Returns a forecast of the specified id and latest version
### GET /api/forecasts/{id}/{version} 
##### Returns a forecast of the specified id and version
### POST /api/forecasts/{id}/versions 
##### Creates a new version of this forecast with the specified updates and run it
### GET /api/forecasts/{id}/{version}/output/{type} 
##### Downloads an output of the given type
### GET /api/data/pre-sign 
##### Returns presigned aws URL for upload of input data
### GET /api/data/{category} 
##### get available data inputs by category
