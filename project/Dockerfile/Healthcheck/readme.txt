Setting Up a Custom Health Check for an Nginx Web Service in Docker
You want to set up a health check for an Nginx web service running inside a Docker container. 
The health check should use a custom path (e.g., devops.com/health-check) instead of the default home page.

Your web service runs on port 9000, so the health check should verify that http://localhost:9000/health-check is working.





Build the Image--------------------------------------------
docker build -t nginx-healthcheck .

Run the Container------------------------------------------------
docker run -d -p 80:80 --name nginx-health nginx-healthcheck

Check Container Health----------------------------------------
docker inspect --format='{{json .State.Health}}' nginx-health | jq


