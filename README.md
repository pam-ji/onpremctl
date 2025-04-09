# onpremctl
This is the controller for the pamji onprem infra. It servers as a low spec router, failover vpn access point and secrets manager. 

## requirements
- predeployed openwrt router

## creates
 - a tailscale-  and a vault docker container
 - those are the minimum requirements to deploy the basis infra via IaC.


## onprem infra (in progress)

> This is our dev environment which is scalable via AWS.
> Development is done locally, AWS is simulated locally too using localstack.
> The development containers stored in gitlab.
![pamjiInfra2](https://github.com/user-attachments/assets/237f4526-e4e2-4520-959d-11a2e667623f)
