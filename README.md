# Nginx Primer 


## Ano at bakit?

Ang **NGINX** ay isang web server na sino-solve ang [C10k Problem](https://www.quora.com/What-causes-the-C10k-problem). 
**TLDR**: Basically term lang to para sa isang problem sa pag handle ng 10,000 concurrent client requests / sabay-sabay na requests.

So, gamit ang **NGINX** pwede siya makapag serve ng files / resources sa different clients ng walang problem.

Di lang web server si **NGINX**, pwede rin siyang maging [Reverse Proxy](https://www.youtube.com/watch?v=ozhe__GdWC8), [Load Balancer](https://www.youtube.com/watch?v=S8J2fkN2FeI), at mag cache ng files.

## NGINX Architecture

Ito yung architecture ni **NGINX** base sa image. Break down natin yung mga laman ng image. Ang NGINX ay isang [Master-Slave](https://en.wikipedia.org/wiki/Master/slave_%28technology%29#:~:text=Master/slave%20is%20a%20model,serves%20as%20their%20communication%20hub.) at [Event Driven Architecture](https://en.wikipedia.org/wiki/Event-driven_architecture#:~:text=Event-driven%20architecture%20%28EDA%29,of,%20and%20reaction%20to%20events.&text=An%20event-driven%20system%20typically,sinks%29,%20and%20event%20channels.).

![enter image description here](https://res.cloudinary.com/practicaldev/image/fetch/s--QIrHFFjG--/c_limit,f_auto,fl_progressive,q_auto,w_880/https://cloudnweb.dev/wp-content/uploads/2019/05/Screen-Shot-2019-05-26-at-1.34.56-AM-1024x580.png)

 - **Master**: Ito yung tagabasa ng mga configuration files na gagawin natin. Tapos yung master rin yung taga-gawa ng mga **Worker** at taga stop ng mga to.
 - **Worker**: Ito yung mga taga process ng requests na nanggagaling sa client side. Bawat Worker kaya mag process ng maraming requests, asynchronously.
 - **Cache Loader:** Taga check ng mga naka cache na items sa disk at taga populate ng data sa **Worker** instance.
 - **Cache Manager**: Taga invalidate at taga check ng expiration ng cache.

## Installation (Ubuntu)

 1. Available si **NGINX** sa **apt** package system ni Ubuntu kaya pwedeng direkta mo na siya i-install gamit yung apt. First step update mo muna yung apt package system para ma fetch yung latest version ng mga packages tapos install mo na si NGINX.
 
	> sudo apt update
	> sudo apt install nginx

 2. Need natin si adjust yung firewall ni Ubuntu para i-allow yung access kay **NGINX** service. Pwede to ayusin gamit yung **ufw** ni Ubuntu.
First step type mo tong command na to:

	> sudo ufw app list

	Pagka-type mo dapat makikita mo tong mga to:
	
	> Available applications:   
	> Nginx Full  
	> Nginx HTTP   
	> Nginx HTTPS  
	> OpenSSH

 - **Nginx Full** : Inoopen nito yung port 80 (Normal na port hindi encrypted) at port 443 (TLS/SSL , encrypted na traffic)
 - **Nginx HTTP** : Unencrypted traffic, port 80.
 - **Nginx HTTPS** : Encrypted traffic, SSL, port 443.

	Para i-allow sa firewall yung specific na app, use this command:

	> sudo ufw allow 'Nginx HTTP'
	
	Verify mo kung enabled na gamit tong command:
	
	> sudo ufw status

	***NOTE***: Kung di pa enabled yung ufw mo, type mo muna to, tapos 			type mo yung command sa taas:
	
	> sudo ufw enable

 3. Check natin kung enabled ba talaga at tumatakbo yung web server natin:
	> systemctl status nginx

	Pag ganito yung result, ok na to:
`nginx.service - A high performance web server and a reverse proxy server
   Loaded: loaded (/lib/systemd/system/nginx.service; enabled; vendor preset: enabled)
   Active: active (running) since Fri 2018-04-20 16:08:19 UTC; 3 days ago
     Docs: man:nginx(8)
 Main PID: 2369 (nginx)
    Tasks: 2 (limit: 1153)
   CGroup: /system.slice/nginx.service
           ├─2369 nginx: master process /usr/sbin/nginx -g daemon on; master_process on;
           └─2380 nginx: worker process`

	Para mas sure, check din natin yung landing page ni NGINX using the server's IP Address, pag di mo alam yung IP Address ng server mo type mo to: 
	> ip addr show eth0 | grep inet | awk '{ print $2; }' | sed 's/\/.*$//'

	***NOTE***: Kung Amazon EC2 Instance gamit mo, may mga steps ka pa na gagawin, visit this link:
	https://docs.nginx.com/nginx/deployment-guides/amazon-web-services/ec2-instances-for-nginx/

	Pag na visit mo na yung ip address niyo, tapos ganito yung bumungad, ayos na:
![enter image description here](https://www.nginx.com/wp-content/uploads/2014/01/welcome-to-nginx-window-5x3.png)

## NGINX Files & Directories

 - **/etc/nginx** : Lahat ng config files nandito.
 - **/etc/nginx/nginx.conf**: Main nginx config file. Pwede baguhin para ayusin yung global config.
 - **/etc/nginx/sites-available**: Directory per site or server blocks. Hindi gagamitin ni nginx yung mga configs na nakalagay dito. Need muna i-link sa sites enabled na directory para magamit.
 - **/etc/nginx/sites-enabled** : Andito na yung mga sites na naka-expose sa internet.
 - **/var/log/nginx/access.log** : Every hit sa web server mo recorded sa log file na to.
 - **/var/log/nginx/error.log** : Every error na magaganap sa nginx naka record dito.
 - **/var/www/html** : Nandito yung nakita natin kanina na default site ni nginx.
## Deployment (Static Website)
1. Copy mo muna yung static content mo papunta sa server. Maraming ways gawin to tulad ng SCP o kaya pag Git clone ng project mo papunta sa server.
2. Enable NGINX to have read access sa content / app mo: sudo chmod 755 /home/user/Dropbox/subdir1
3. Punta ka sa **/etc/nginx/sites-available** sa server mo, tapos type mo to: 
	> sudo vim [sitename]; ***Example : sudo vim portfoliosite***
	
	Tapos niyan may lalabas na code editor i-paste mo tong nginx config 		file na to:

```
  server {
        listen       80;
        server_name  testinglang.tk www.testinglangmuna.tk;
    location / {
            root   /home/ubuntu/app/sample-site;
            index  index.html index.htm [Root file na i-seserve ni NGINX];
			try_files $uri $uri/ =404;
        }
```

4. Pag nagawa mo na yung file sa taas, gawa ka ng link papunta sa sites-available to sites-enabled. Gamitin mo tong command sample: 

> sudo ln -s /etc/nginx/sites-available/example.com
> /etc/nginx/sites-enabled/

5. Make sure na wala kang syntax errors sa conf file mo. Use this command :

> sudo nginx -t

6. Pag wala namang problems, restart mo na si NGINX:

> sudo systemctl restart nginx

## Deployment (Backend)
## Proxy vs Reverse Proxy
## Load Balancer
## Useful Links
### Information

 - https://www.aosabook.org/en/nginx.html

### Installation
 - **Windows** :  http://nginx.org/en/docs/windows.html
 - **Ubuntu :**  https://www.digitalocean.com/community/tutorials/how-to-install-nginx-on-ubuntu-18-04 (Halos kumpleto na rin dito, pati deployment haha)
 - **Deployment Guide EC2** : https://docs.nginx.com/nginx/deployment-guides/amazon-web-services/ec2-instances-for-nginx/
 - **Deployment Guide with Domain Mapping** : https://hackernoon.com/how-to-run-multiple-apps-on-a-single-ec2-instance-with-nginx-and-map-your-custom-domain-2640630ce1e7
