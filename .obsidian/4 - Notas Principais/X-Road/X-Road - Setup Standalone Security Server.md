2026-06-25 15:54

Status: #adult

Tags: [[x-road]]

Autor: Christopher Moreira e Davi Balan
- - -
# Rodando o Container Standalone do Security Server

https://hub.docker.com/r/niis/xroad-security-server-standalone

### Subindo o Container
	docker pull niis/xroad-security-server-standalone:noble-7.8.1

Publish the container ports (4000 and 8080) to localhost (loopback address).
	`docker run -p 127.0.0.1:4000:4000 -p 127.0.0.1:8080:8080 -d --name standalone --hostname ss niis/xroad-security-server-standalone:noble-7.8.1`

### Subindo o Receiver
Depois disso, subimos a aplicação GO que será o server que receberá o request /ping
git clone https://github.com/digitaliceland/xrd-rest-server.git

	cd xrd-rest-server
	
	go build main.go
	
	mv main rest-server
	
	./rest-server

O return deve ser:
INFO	2020/02/27 15:03:57 Starting server on :1234
### Configuring the server
"[](https://github.com/digitaliceland/Straumurinn/blob/master/DOC/Manuals/standalone_security_server_tutorial.md#configuring-the-server)
Leaving the SSS running, please point your Firefox browser to: [https://localhost:4000/](https://localhost:4000/)
[![login](https://github.com/digitaliceland/Straumurinn/raw/master/DOC/images/xrdlogin2.png)](https://github.com/digitaliceland/Straumurinn/blob/master/DOC/images/xrdlogin2.png
Please use the following credentials to log in:
- Username: xrd
- Password: secret
Now you shoul be greeted by the X-Road SS page.
Out of the box there is already configured a TestClient and a TestService. We will use these accounts for our tests.
Next we need to set up our new web service, to make it accessible to our clients.
Click TestService to access information about the service:
[![login](https://github.com/digitaliceland/Straumurinn/raw/master/DOC/images/xrd2b.png)](https://github.com/digitaliceland/Straumurinn/blob/master/DOC/images/xrd2b.png)
Then click SERVICES:
[![login](https://github.com/digitaliceland/Straumurinn/raw/master/DOC/images/xrd2c.png)](https://github.com/digitaliceland/Straumurinn/blob/master/DOC/images/xrd2c.png)
And then click ADD REST to add a new REST web service and fill out the form. Please note that you will need your network IP address on your local networok
[![login](https://github.com/digitaliceland/Straumurinn/raw/master/DOC/images/xrd2d.png)](https://github.com/digitaliceland/Straumurinn/blob/master/DOC/images/xrd2d.png)
To get this IP address please click the apple -> System Preferences -> Network. You will find your IP address there
[![login](https://github.com/digitaliceland/Straumurinn/raw/master/DOC/images/xrd9.png)](https://github.com/digitaliceland/Straumurinn/blob/master/DOC/images/xrd9.png)
Now that you have your IP address you are ready to add the new web service.
[![login](https://github.com/digitaliceland/Straumurinn/raw/master/DOC/images/xrd3a.png)](https://github.com/digitaliceland/Straumurinn/blob/master/DOC/images/xrd3a.png)
Next we need to enable the web service
[![login](https://github.com/digitaliceland/Straumurinn/raw/master/DOC/images/xrd4a.png)](https://github.com/digitaliceland/Straumurinn/blob/master/DOC/images/xrd4a.png)
So far nobody has access to this web service. We want to grant TestClient access to it
[![login](https://github.com/digitaliceland/Straumurinn/raw/master/DOC/images/xrd5a.png)](https://github.com/digitaliceland/Straumurinn/blob/master/DOC/images/xrd5a.png)
And please select Test Client and then Next
[![login](https://github.com/digitaliceland/Straumurinn/raw/master/DOC/images/xrd6a.png)](https://github.com/digitaliceland/Straumurinn/blob/master/DOC/images/xrd6a.png)
Next we have to add a service code. We select TEST123 and then "Add Selected"
[![login](https://github.com/digitaliceland/Straumurinn/raw/master/DOC/images/xrd7a.png)](https://github.com/digitaliceland/Straumurinn/blob/master/DOC/images/xrd7a.png)
Nice Work. We now have granted TestClient access to the service."


### Rodando o Caller
Por fim,  montamos o serviço que fará o request para o serviço exposto
	`git clone https://github.com/digitaliceland/xrd-rest-client.git`
	
	`cd xrd-rest-client`
	
	`go build main.go`
	
	`mv main rest-client`
	    	
	`./rest-client -cmd ping -service CS/ORG/1111/TestService/TEST123 -client CS/ORG/1111/TestClient -ss http://localhost:8080

O retorno deve ser:
Accessing:  http://localhost:8080/r1/CS/ORG/1111/TestService/TEST123/ping
Ping returned:  {"message": "pong"}

---
### Repo de config do Standalone
https://github.com/digitaliceland/Straumurinn/blob/master/DOC/Manuals/standalone_security_server_tutorial.md
 
- - -
# Referências
https://hub.docker.com/r/niis/xroad-security-server-standalone