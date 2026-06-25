# QuakeWatch - Dockerized Version

This submission includes:

- `README.md`
- `docker-compose.yml`
- A zip file containing the full QuakeWatch project files

The application can be run in two ways:

1. Pull the image from Docker Hub and run it using `docker run`
2. Unzip the project files and run the application using Docker Compose

---

## Option 1: Pull and Run from Docker Hub

Pull the image:

```bash
docker pull rawiassy97/quakewatch:v1
```

Run the container:

```bash
docker run -p 5000:5000 rawiassy97/quakewatch:v1
```

Open the application:

```text
http://localhost:5000
```

---

## Option 2: Run Using Docker Compose

First, unzip the project files:

```bash
unzip Project Phase 1 - Rawi Assy.zip
```

Enter the project directory:

```bash
cd QuakeWatch
```

Make sure `docker-compose.yml` is inside the same directory.

Run the application:

```bash
docker compose up
```

Or in the background:

```bash
docker compose up -d
```

Open the application:

```text
http://localhost:5000
```

Stop the application:

```bash
docker compose down
```

---

## Build the Image Locally

After unzipping the project and entering the project directory:

```bash
docker build -t quakewatch:v1 .
```

Run the locally built image:

```bash
docker run -p 5000:5000 quakewatch:v1
```

---

## Docker Hub Image

```text
rawiassy97/quakewatch:v1
```
