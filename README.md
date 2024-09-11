# Kittygram
## Description
A small practice project to store pictures of cats.
Practiced CI/CD, Docker and DRF
## Build status
[![Kittygram Workflow](https://github.com/alisher-nil/kittygram/actions/workflows/main.yml/badge.svg?branch=main&event=push)](https://github.com/alisher-nil/kittygram/actions/workflows/main.yml)
## Differences Between Production and Development
- Production:
  Use `docker-compose.production.yml` to use images uploaded to docker hub.
- Development:
  Use `docker-compose.yml` to build and run containers from local files.
## Deployment

### Run locally
- Install docker
On linux:
```bash
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh ./get-docker.sh
```
On other platforms:
[Docker Desktop](https://www.docker.com/products/docker-desktop/)
- Clone the repo and move to the directory
```bash
git@github.com:alisher-nil/kittygram_final.git
cd kittygram_final/
```
- Deploy using docker:
```bash
docker compose up -d
```
- Apply migrations and move static files to static volume:
```bash
docker compose exec backend python manage.py migrate
docker compose exec backend python manage.py collectstatic
docker compose exec backend cp -r /app/collected_static/. /backend_static/static/
```
Deployed project should be available on `127.0.0.1:9000`. Port can be adjusted in .env file.
### Run on a remote vm:
- Move docker compose files and .env to remote vm using scp
```bash
scp /kittygram_final/docker-compose.production.yml <username>@<address>:/kittygram
scp /kittygram_final/.env <username>@<address>:/kittygram
```
- log in to remote vm using ssh
```bash
ssh -i path/to/privatekey <username>@<address>
```
- Install docker and nginx web server
```bash
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh ./get-docker.sh
sudo apt install nginx -y
sudo systemctl start nginx
```
- Run containers, apply migrations, move static files:
```bash
cd kittygram/
sudo docker compose -f docker-compose.production.yml up -d
sudo docker compose -f docker-compose.production.yml exec backend python manage.py migrate
sudo docker compose -f docker-compose.production.yml exec backend python manage.py collectstatic --noinput
sudo docker compose -f docker-compose.production.yml exec backend cp -r /app/collected_static/. /backend_static/static/
```
- adjust the nginx config
```bash
sudo nano /etc/nginx/sites-enabled/default
```

```properties
server {
    server_name <your host address>;
        ...
        location / {
            proxy_pass http://127.0.0.1:9000/;
            proxy_set_header Host $http_host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;
        }
        ...
    }
```
Check config and reload nginx daemon
```bash
sudo nginx -t
sudo systemctl reload nginx
```
## Author
- Alisher Nildibaev alisher.nil@gmail.com

## Technologies Used
- Python
- Django
- Docker
- Nginx
- CSS
- HTML
