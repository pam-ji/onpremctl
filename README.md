# onpremctl
This is the controller for the pamji onprem infra. It servers as a low spec router, failover vpn access point and secrets manager. 

## requirements
- predeployed openwrt router

## creates
 - a tailscale-  and a vault docker container
 - those are the minimum requirements to deploy the basis infra via IaC.

## deployment
- generate a tailscale oauth key
![oauth](https://private-user-images.githubusercontent.com/117015142/431810233-65bbfed3-f9cc-44a6-b7c8-012b813f9fa1.png?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3NDQxOTcxODAsIm5iZiI6MTc0NDE5Njg4MCwicGF0aCI6Ii8xMTcwMTUxNDIvNDMxODEwMjMzLTY1YmJmZWQzLWY5Y2MtNDRhNi1iN2M4LTAxMmI4MTNmOWZhMS5wbmc_WC1BbXotQWxnb3JpdGhtPUFXUzQtSE1BQy1TSEEyNTYmWC1BbXotQ3JlZGVudGlhbD1BS0lBVkNPRFlMU0E1M1BRSzRaQSUyRjIwMjUwNDA5JTJGdXMtZWFzdC0xJTJGczMlMkZhd3M0X3JlcXVlc3QmWC1BbXotRGF0ZT0yMDI1MDQwOVQxMTA4MDBaJlgtQW16LUV4cGlyZXM9MzAwJlgtQW16LVNpZ25hdHVyZT1mZTEzYWNjYjM1M2U4YTNlMzNhYjk1YTcxZDY2Yzc1Mzk0MTdjZmI4YWM3NGQ0NTM3MTc4MzYwYmFlNmNjOWI3JlgtQW16LVNpZ25lZEhlYWRlcnM9aG9zdCJ9.JCuxistGcHa9t7hCpEeKEIyyzyXHEnUxQ6f1tD8fZs0)
- ssh into the openwrt router
- get the files
  ```bash
  wget --no-check-certificate --content-disposition https://github.com/pam-ji/onpremctl/archive/refs/heads/main.zip
  unzip onpremctl-main.zip
  ```
- cd into `onpremctl-main/docker`
- export your authkey
  ```bash
  export TS_AUTHKEY="tskey-client-..."
  ```
- edit your tailscale flags in the compose file under `TS_EXTRA_ARGS=`
  - mine are for routing and ssh: 
    ```yml
    TS_EXTRA_ARGS=--ssh --advertise-routes=200.0.0.0/8,192.168.0.0/16 --advertise-tags=ci
    ```
- now you can deploy with `docker compose up`

## onprem infra (in progress)

> This is our dev environment which is scalable via AWS.
> Development is done locally, AWS is simulated locally too using localstack.
> The development containers stored in gitlab.
![pamjiInfra2](https://github.com/user-attachments/assets/237f4526-e4e2-4520-959d-11a2e667623f)
