# CMPS-394-Day 11

Lab 1: Hello CI
- Clone the lab repository for this class
- Follow the quickstart guide: https://docs.github.com/en/actions/get-started/quickstart
- Push your YAML file, do not add it via GitHub's web interface
- You should see a green check mark in the "Actions" tab on your repo

Lab 2: Frontend & Backend Docker Builds
- Create a "frontend" folder
- Create "frontend/index.html:
```
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Frontend App</title>
</head>
<body>
  <h1>Hello from the frontend!</h1>
</body>
</html>
```
- Create "frontend/Dockerfile"
```
FROM nginx:alpine
COPY index.html /usr/share/nginx/html/index.html
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```
- Create a "backend" folder
- Create "backend/main.go"
```
package main

import (
	"fmt"
	"net/http"
)

func main() {
	http.HandleFunc("/", func(w http.ResponseWriter, r *http.Request) {
		fmt.Fprintln(w, "Hello, World from Go backend!")
	})

	fmt.Println("Starting server at :8080")
	http.ListenAndServe(":8080", nil)
}
```
- Create "backend/Dockerfile"
```
FROM golang:alpine AS builder
WORKDIR /app
COPY . .
RUN go build -o app

FROM alpine
WORKDIR /app
COPY --from=builder /app/app ./app
EXPOSE 8080
CMD ["./app"]
```
- Now, write a frontend and backend github workflow (separate files, preferably) to build the containers and test that they are working
- LLMs are good at getting you started given a detailed prompt
    - NOTE: The LLM will likely use the "actions/checkout" and "docker/setup-buildx-action" vendor actions. Find their repositories and make sure the version it gave you is up to date as it will likely give you the version active at the time of their training data cutoff.
- Do not blindly copy-paste. If something looks funky or unnecessary to you, figure out why it is needed or omit it
- By the end, you should have a workflow for both the frontend and backend that:
    - builds the container
    - runs the container
    - verifies that the application has started properly and gives an expected result
    - shuts down the container

Assignment: Concluding Your Project
Take the code you have been working on for the last several assignments and send it off with a nice final touch: build pipelines
- Your frontend container should have a build pipeline
- Your backend container should have a build pipeline
    - Note that if starting your backend is dependent on having an active database, you will need to modify the application to either support no database on startup, or have a minimal run mode just for a health check of some kind
- Bonus: Set up a trivy scan action for both the frontend and backend
