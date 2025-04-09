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
- unfortunately you need to click the login link in the docker compose output
![compose output](https://private-user-images.githubusercontent.com/117015142/431870433-73881dfc-f984-4b07-9c9f-1bd1f274fa59.png?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3NDQyMDY2NjQsIm5iZiI6MTc0NDIwNjM2NCwicGF0aCI6Ii8xMTcwMTUxNDIvNDMxODcwNDMzLTczODgxZGZjLWY5ODQtNGIwNy05YzlmLTFiZDFmMjc0ZmE1OS5wbmc_WC1BbXotQWxnb3JpdGhtPUFXUzQtSE1BQy1TSEEyNTYmWC1BbXotQ3JlZGVudGlhbD1BS0lBVkNPRFlMU0E1M1BRSzRaQSUyRjIwMjUwNDA5JTJGdXMtZWFzdC0xJTJGczMlMkZhd3M0X3JlcXVlc3QmWC1BbXotRGF0ZT0yMDI1MDQwOVQxMzQ2MDRaJlgtQW16LUV4cGlyZXM9MzAwJlgtQW16LVNpZ25hdHVyZT1jODA3OWU4MGE3ZTUwZWI0ZTBkOTEzNzUxZTJjNjQzODgyOWU5MjEzM2NhMDhjZDAzZDIyZGY0YTYzNzJhMmUyJlgtQW16LVNpZ25lZEhlYWRlcnM9aG9zdCJ9.jknH1j98R_mkBQziRk7-u8u-v2wWWkOZI1cyNGH8Edk)
> its on of the first lines of the tailscale logs

## changing the avertised routes
- ssh into the container
  - since we are having ssh access via tailscale and wifi access via the router, we dont need to worry about breaking something
```bash
tailscale set --advertise-routes=200.0.0.0/8,192.168.0.0/16 
```
> this way you dont need to use tailscale down, or "" for the advertise routes to delete them

## issues
### initial login required
- tailscale should use the ts_authkey which is available as a env var in the container and can be used via $TS_AUTHKEY, but somehow tailscale requires an initial login. the weird thing is tho, when you rerun compose, it does not ask for the login again and uses the authkey.

### networking issues
- make sure that openwrt allows your bridge to use the eth0 interface
this is my current setup:
![image](https://github.com/user-attachments/assets/dc67c094-8140-4c9c-b15d-eb6dd97296bc)

under interfaces -> devices -> docker0

![image](https://github.com/user-attachments/assets/07a3a945-e7b6-4fa6-ba6e-23d6d8856e69)

firewall

![image](https://github.com/user-attachments/assets/16daf153-5f39-4dd2-a4db-5d01cc64c320)

## onprem infra (in progress)

> This is our dev environment which is scalable via AWS.
> Development is done locally, AWS is simulated locally too using localstack.
> The development containers stored in gitlab.
![pamjiInfra2](https://github.com/user-attachments/assets/237f4526-e4e2-4520-959d-11a2e667623f)
