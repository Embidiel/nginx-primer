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
## Deployment 
## Proxy vs Reverse Proxy
## Load Balancer
## Useful Links
### Information

 - https://www.aosabook.org/en/nginx.html

### Installation
 - **Windows** :  http://nginx.org/en/docs/windows.html
 - **Ubuntu :**  https://www.digitalocean.com/community/tutorials/how-to-install-nginx-on-ubuntu-18-04 (Halos kumpleto na rin dito, pati deployment haha)
