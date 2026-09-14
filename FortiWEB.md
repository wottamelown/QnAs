Forti Web

Forti web is a web application firewall it protects the DMZ servers from web security vulnerabilities, it also protect through OWASP

### Basic Configuration steps.
First we will create the Virtual IP then the virtual server. 

Virtual server is the group IP address of multiple nodes. of Virtual IPs. In f5 we create Pool

then we create the pool. in f5 we create virtual server. 

then we create the policy in the policy we can specify everything including web protection profile. Here we have web cache as well. for faster loading times. DOS rules, URL rewriting. 

then we need to create signature profile and then web protection profile. 

so basically in FortiWeb we bind or combine VS with Server Pool and security profiles so it works as a glue. 

### Health Check Configuration

There are three health check: ICMP, TCP (confirms the 3 way handshake), HTTP (web browser code).

### Load Balancing Methods

1. Round Robin, Weighted (Higher value), Least connection, Ratio based, Persistence Source IP.

### persistence

Keeping a client connecting to the same server. 
we have two types:
Source IP based, cookie based. 

### Content Re-Writing

FortiWeb will distribute the traffic according to the user need for example text Pool1 for images Pool2. 

### SSL Offloading

This will slowdown the webserver because it creates SSL conenction between user and FWB. So it decrypts the data coming from the user. 
ZeroSSL certificates have default validity 90 days. 

### API Protection

we need to enable API Gateway from features. 

then replace /front/api with /xyz/api 

we basically hide the URL

we can also set API gateway rate limit so if someone refresh the browser of website it will alert and deny the api 

we can also configure user and key to protect the API gateway. 





