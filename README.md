# Flask Docker CI

A small Flask app, dockerized, with a GitHub Actions workflow that builds the Docker image on every push.

## Project files
- `app.py` - Flask app with `/` and `/health` routes
- `requirements.txt` - Python dependencies (`flask`)
- `Dockerfile` - builds the container image
- `.github/workflows/build.yml` - CI workflow

## Build and run
```
docker build -t flask-devops .
docker run -d -p 5000:5000 --name flaskapp flask-devops
```

## Test
```
curl localhost:5000/
curl localhost:5000/health
```
Output:
```
Hello from DevOps!
{"status":"ok"}
```

## CI
`.github/workflows/build.yml` runs on every push. It checks out the code and builds the Docker image with `docker build -t flask-devops .`. Pushing to a registry is not required.

## Task 4: Bug identification and troubleshooting

The Flask application supplied in the assignment (`app.py`, `requirements.txt`) was reviewed for syntax errors, route configuration, dependency requirements and network binding. No code-level defects were found: the app binds to `0.0.0.0` on port 5000 and the `/` and `/health` routes work.

### Causes of "app can't be reached" that were checked
|               Issue                     |                 Resolution                    |
|-----------------------------------------|-----------------------------------------------|
| Application inaccessible outside Docker | Flask must listen on `0.0.0.0` (it does)      |
| Host cannot access port 5000            | Publish the port with `-p 5000:5000`          |
| Container fails during startup          | Check dependencies and `docker logs flaskapp` |
| Incorrect startup command               | Dockerfile runs `CMD ["python", "app.py"]`    |

### Result
The image built successfully and the container ran. `curl localhost:5000/health` returned `{"status":"ok"}`.
