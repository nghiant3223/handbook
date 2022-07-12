# Web Hosting vs Web Server vs Application Server vs Web Service
## Web Server
- Web servers are mainly computers that deliver web pages.
- Each web server has a Unique IP address and a domain name.
- If you type the name of a specific URL, the server searches for that URL on the internet, fetches it, and delivers it to your web browser. All these activities are done within a matter of a few seconds. 
- The web server is connected to the browser through a trans-coder. You can visualize web servers as computers that offer web pages.
- There are different types of web servers available: 
  - File Server: file server is a storage device or computer designed to store files.
  - Database Server: A database server refers to the server or a device dedicated server hosting to processing database queries.
- To process a request, a Web server may respond with a static HTML page or image, send a redirect, or delegate the dynamic response generation to some other program such as CGI scripts, JSPs (JavaServer Pages), servlets, ASPs (Active Server Pages), server-side JavaScripts, or some other server-side technology.
- Whatever their purpose, such server-side programs generate a response, most often in HTML, for viewing in a Web browser.
- The Web server doesn't provide any functionality beyond simply providing an environment in which the server-side program can execute and pass back the generated responses.
## Web Hosting
- A company offering a web hosting service
- They are primarily responsible for hosting the website on a server.
- A web host can sell or rent the server machine and ensure that it runs smoothly and efficiently. 
## Web Host vs Web Server
- A Web server is a computer that runs your website whereas Web hosts make those websites accessible to the user.
- The web server provides the services to other devices on the internet. On the contrary, web hosts allow organizations and businesses to run their websites online.
- Web servers’ main functions include storing, processing, and delivering web pages. Whereas, the main objectives of a web host include hosting the website, taking care of the setup and technical problems, and keeping the entire site on their servers.
- It is vital to know that you need both to run your website efficiently.
## The application server
- An application server exposes business logic to client applications through various protocols, possibly including HTTP
- Such application server clients can include GUIs (graphical user interface) running on a PC, a Web server, or even other application servers.
- In most cases, the server exposes this business logic through a component API,
## Example
- An online store that provides real-time pricing and availability information
- The site will provide a form with which you can choose a product
- When you submit your query, the site performs a lookup and returns the results embedded within an HTML page.
### Scenario 1: Web server without an application server
- The Web server takes your request, then passes it to a server-side program able to handle the request.
- The server-side program looks up the pricing information from a database or a flat file.
- Once retrieved, the server-side program uses the information to formulate the HTML response, then the Web server sends it back to your Web browser.
- To summarize, a Web server simply processes HTTP requests by responding with HTML pages.
### Scenario 2: Web server with an application server
- You can now put the business logic for the pricing lookup onto an application server
- Instead of the script knowing how to look up the data and formulate a response, the script can simply call the application server's lookup service.
- The script can then use the service's result when the script generates its HTML response.
- The pricing logic becomes far more reusable between applications
## Web service
- A Web service is a way for two machines to communicate with each other over a network.
- An application that is run by a web server and that results in a series of URLs that will return their data in a format that is intended to be parsed by a 'generic' computer program instead of by a browser.
- As a result web services can use any data format they want (as long as both sides agree), but typically something like XML (specifically SOAP or so) or JSON is used.
## Web APIs
- API stands for Application Programming Interface. 
- It is a collection of communication conventions and subroutines used by various programs to communicate between them.
# Ref
- https://www.youtube.com/watch?v=WDY8j-nSwi4
- https://go4hosting.com/howto/web-hosting/how-to-differentiate-between-a-web-server-and-web-host/#:~:text=Web%20server%20vs.&text=you%20server%20vs.-,hosting%3A,to%20run%20their%20websites%20online.
- https://www.infoworld.com/article/2077354/app-server-web-server-what-s-the-difference.html#:~:text=Q%3A%20What%20is%20the%20difference,through%20any%20number%20of%20protocols.
- https://www.quora.com/What-is-the-difference-among-web-service-web-server-web-host-and-web-client
- https://www.geeksforgeeks.org/differences-between-web-services-and-web-api/#:~:text=Web%20services%20are%20a%20type,and%20XML%2DRPC%20for%20communication.