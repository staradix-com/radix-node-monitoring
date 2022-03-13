# radix-node-monitoring
Configures Grafana/Prometheus stack for monitoring Radix nodes. 

### How to set this up ###
Since I don't like to tinker with my Radix node as soon as it runs, I use a different server to monitor it.
I reccomend you do the same. If you don't, you will have to change the configuration in the compose file and change the ports.

### Security ###
As I like my security, and as we are talking running a Radix node here, I like to use Traefik. 
Traefik is a proxy that will take care of certificates so that all our traffic is encrypted.
In my situation I have also closed down the firewall a on the Radix node so that only my monitoring node can access the metrics. 
Using Traefik we can also narrow down who has acces to your monitoring stack, based on IP or username. 
You can find a list of supported DNS providers by Traefik [here] (https://doc.traefik.io/traefik/https/acme/#providers).

### Prerequisites ###
* A VPS/VM/Machine with Docker installed, running on Linux.
* Docker compose installed.
* Internet access
* A running Radix node.
* A domain name hosted at AWS Route53 or Hetzner. (Or add them yourself)
* API key for your DNS hoster.
* Slack workspace and an webhook for it. 
* Run `radixnode monitoring setup` on the Radix node.

### Slack ###
For now Slack is built in this repo as the only alerting mechanism.
Create a webhook in your workspace and paste the URL in the Alertmanager configuration later.
It will alert on:
* Node exporter (CPU Usage, RAM usage, Disk usage, etc.)
* Traefik (High error rate, etc.)
* When your Radix node is down.

### Setting up the monitoring stack ###
Clone the repository:
```
gh repo clone staradix-com/radix-node-monitoring && cd radix-node-monitoring
```
Edit the variables:
```
vim .env
```
Edit the Prometheus configuration file using the values in .env file:
```
vim prometheus/prometheus.yml
```
Edit the Alertmanager configuration file:
```
vim alertmanager/alertmanager.yml
```

Run the stack
```
docker-compose up -d
```
Wait two minutes for the initial certificates to be generated.

After that, these are the URLs, `$DOMAIN` should be replaced with your domain name.
* Prometheus: https://prom-$HOSTNAME.$DOMAIN
* Grafana: https://grafana-$HOSTNAME.$DOMAIN
* Alertmanager: https://alertmanager-$HOSTNAME.$DOMAIN
* Node exporter: https://node-exporter-$HOSTNAME.$DOMAIN/metrics
* Traefik admin: https://traefik-$HOSTNAME.$DOMAIN/dashboard/

### I will add later: ###
* Bash script to set the configuration values.
* Non Traefik values