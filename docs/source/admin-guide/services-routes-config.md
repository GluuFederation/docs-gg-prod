# Services and Routes Configurations

This sections describe you how to configure your upstream app into Gluu Gateway. GG has Kong and it is the proxy and it has very simple flow as below. You just need to add your upstream application in kong.

[![info](../img/how-kong-proxy-works.png)](../img/how-kong-proxy-works.png)
 
## Step-1 : Create a new Service

First step is to add your Upstream Application in Kong. Kong provides the service entity which represent the `Upstream Application`. You just need to add your Upstream Application URL.

- Open the `Services` section, left side in the navigation menu.

- Click on `+ ADD NEW SERVICE` button.

    [![3_services](../img/3_services.png)](../img/3_services.png)

- It will open a below form.  

    [![3_service_add](../img/3_service_add.png)](../img/3_service_add.png)

- Enter the `name` and `URL`. 
    
    For Example: If your upstream service url is `http://localhost:5555`, then set URL `http://localhost:5555` and name `test-service`.
    
    Kong also allows Services to be configured using `Protocol`, `Host`, `Path`, and `Port` (optional). To configure the Service using this method, you just need to leave URL field and enter values in this field one by one. URL field is just quick and short way to configure service. 

- Click on `SUBMIT SERVICE` at the bottom of the form

If successful, it will show it in list. Click on name of the service or pencil icon to see full details about service. 

## Step-2 : Create a new Route attached to the Service

Second step is to create a route in the same service which you created above. Route represent the actual request to kong proxy endpoint to reach at Kong service.

- To create a new Route, click on service name or pencil icon and it will open below view.

     [![3_service_manage](../img/3_service_manage.png)](../img/3_service_manage.png) 
     
- Click on `Routes`. It will show you already created routes. Click on `+ ADD ROUTE` button to add new route. 

     [![3_service_routes](../img/3_service_routes.png)](../img/3_service_routes.png)

- After clicking on button, it will open below form. The Route will also need a name, and at least one of the following fields: `Host`, `Methods`, or `Paths`. Set name `test-service-route` and hosts `test.com`. Press enter when you enter values in hosts field.

     [![4_route_plugins_add](../img/4_route_add.png)](../img/4_route_add.png) 

- Click on `SUBMIT ROUTE` button at the bottom of form to create route.

If successful, it will shows it in listing in `Routes` section.

## Step-3: Test the new Service and Route

Use below curl command which request to kong proxy endpoint and verify that kong is properly forwarding request to Service. As per default setup kong proxy endpoint is exposed on `:443` port.

- Run this below command on same machine where you installed Gluu-Gateway, I am using `https://localhost` because of I am on same machine.

     ```
     curl -k -X GET \
       https://localhost \
       -H 'Host: test.com'
     ```

- Run this below command on any other machine, the condition is you just need to expose your proxy endpoint globally with domain. For Example: Suppose I installed GG on `dev.gluu.org` then request will be 

     ```
     curl -k -X GET \
       https://dev.gluu.org \
       -H 'Host: test.com'
     ```

- Suppose you have `/users` endpoint then you just need to add your endpoint path in URL like below example.

     ```
     curl -k -X GET \
       https://dev.gluu.org/users \
       -H 'Host: test.com'
     ```

One important thing is here, I am passing `Host: test.com` in header this is the filter for our above created route which we configured with same host details. So when you make this request, kong match all the request with kong route configurations. In above case, it is matching `host`. There are several possibilities for routing.

## Routes configurations

The route configuration is just like we define API in any programming language where we add HTTP Method and Endpoint Path. Here kong has more powerful tools for routing. The Route provides to add `Hosts`, `HTTP Methods`, `Paths` and `Headers`. Let's see some example

- GREEN requests are valid and allowed by Kong.
- RED requests are invalid and deny by Kong.

| Route Configuration | Requests |
|---------------------|---------|
| <ul><li>Hosts: test.com</li></ul>|<ul style="color:green;padding:5px;border:1px solid green;"><li>`curl -k -X GET  https://localhost -H 'Host: test.com'`</li><li>`curl -k -X POST  https://localhost -H 'Host: test.com'`</li><li>`curl -k -X PUT  https://localhost -H 'Host: test.com'`</li><li>Allow request which has host `test.com`</li></ul><ul style="color:red;padding:5px;border:1px solid red;"><li>`curl -k -X GET  https://localhost`</li><li>`curl -k -X GET  https://localhost -H 'Host: anyother.com'`</li></ul>|
| <ul><li>Hosts: test.com</li><li>Methods: GET</li></ul>|<ul style="color:green;padding:5px;border:1px solid green;"><li>`curl -k -X GET https://localhost -H 'Host: test.com'`</li></ul><ul style="color:red;padding:5px;border:1px solid red;"><li>`curl -k -X GET  https://localhost`</li><li>`curl -k -X GET  https://localhost -H 'Host: anyother.com'`</li><li>`curl -k -X POST  https://localhost -H 'Host: anyother.com'`</li></ul>|
| <ul><li>Methods: GET</li></ul>|<ul style="color:green;padding:5px;border:1px solid green;"><li>`curl -k -X GET https://localhost`</li><li>`curl -k -X GET https://localhost -H 'Host: test.com'`</li><li>Adding extra Host header will not affect because kong only checking HTTP `GET` request</li></ul><ul style="color:red;padding:5px;border:1px solid red;"><li>`curl -k -X POST https://localhost`</li></ul>|
| <ul><li>Methods: GET, POST</li></ul>|<ul style="color:green;padding:5px;border:1px solid green;"><li>`curl -k -X GET https://localhost`</li><li>`curl -k -X POST https://localhost`</li><li>Allow only HTTP `GET` and `POST` request</li></ul><ul style="color:red;padding:5px;border:1px solid red;"><li>`curl -k -X PUT https://localhost`</li></ul>|
| <ul><li>Paths: /v1 </li></ul> | <ul style="color:green;padding:5px;border:1px solid green;"><li>`curl -k -X GET https://localhost/v1`</li><li>`curl -k -X POST https://localhost/v1`</li><li>For your `/users` endpoint: `curl -k -X POST https://localhost/v1/users`</li><li>Allow request which has `/v1` in URL.</li></ul> |

## Configure multiple Upstream Application

Yes, you can configure multiple upstream application in the Kong. You just need to organization and configure the routes.

[![info](../img/how-kong-proxy-works-multiple-upstreams.png)](../img/how-kong-proxy-works-multiple-upstreams.png)

For Example: we have three upstream applications **http://localhost:5001**, **http://localhost:5002** and **http://localhost:5003** then the configuration is as below

1. **http://localhost:5001**

      - **Service:** `name: test-service-5001`, `URL: http://localhost:5001`
      - **Route:** `Hosts: test.com`
      
1. **http://localhost:5002**

      - **Service:** `name: test-service-5002`, `URL: http://localhost:5002`
      - **Route:** `Hosts: test.com`, `Path: /v2`      

1. **http://localhost:5003**

      - **Service:** `name: test-service-5003`, `URL: http://localhost:5003`
      - **Route:** `Hosts: test.com`, `Path: /v3`      

I am using `PATHs` field of Route to configure and differentiate the request from one another services. 

For `http://localhost:5001` case, you just need to request with `Host: test.com` in header.

For `http://localhost:5002` case, you need to request with `Host: test.com` and `/v2` in URL.

For `http://localhost:5003` case, you need to request with `Host: test.com` and `/v3` in URL.

