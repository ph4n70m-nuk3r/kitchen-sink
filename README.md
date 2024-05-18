# [ph4n70m-nuk3r/kitchen-sink](https://github.com/ph4n70m-nuk3r/kitchen-sink)
This container is an example of a multipurpose utility.

[YaCron](https://github.com/gjcarneiro/yacron) is used to schedule arbitrary jobs.  
[Nginx](https://github.com/nginx/nginx) is used to serve arbitrary documents.

## Prerequisites
- Update config/static files as desired
  - Nginx (`docker/resources/buildtime/config/nginx.conf`)
    - See Nginx repository for more details
  - Static files (`docker/resources/buildtime/static/**/*`)
    - Update or remove as desired (make sure to amend corresponding COPY in Dockerfile)
  - YaCron (`docker/resources/buildtime/config/yacron.yaml`)
    - **SMTP (mail: to/from/username) settings must be removed or updated to use your own email**
    - **SMTP_PASSWORD (ignore if you have removed SMTP config)**
      - Note: for gmail, you need to generate an 'app password' for this.
    - See YaCron repository for more details

## Docker Build
```shell
docker build -f "./docker/Dockerfile" -t kitchen-sink:latest "./docker"
```

## Docker Run
Make sure to have created the secret file (`./docker/resources/runtime/secret/smtp_password.txt`) before proceeding
```shell
docker run \
    --env=SMTP_PASSWORD="$(cat "./docker/resources/runtime/secret/smtp_password.txt")" \
    --interactive \
    --network=host \
    --tty \
    --rm \
    kitchen-sink:test
```

## Testing Nginx files.
### Nginx default index.html
http://localhost:8080/
### example.txt
http://localhost:8080/public/doc/example.txt
### index.html (created by YaCron startup job)
http://localhost:8080/public/doc/
### error page (/static/html/404.html)
curl http://localhost:8080/blah
