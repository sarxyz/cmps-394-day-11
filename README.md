# CMPS-394-Day 10

## Lab 1: Scanner Setup
We will be using a series of container scanning tools to identify vulnerabilities in our container images. The first step is to set up these tools on your local machine.
- Trivy: `docker pull aquasec/trivy:0.69.3`
    - https://trivy.dev/docs/latest/getting-started/
    - We are using version 0.69.3 because it is latest and not specifying a version will cause the image not to be found as the Trivy team has removed the latest tag from their repository. Had to dig through GitHub issues to find this information.
- Grype: `docker pull anchore/grype`
    - https://oss.anchore.com/docs/guides/vulnerability/getting-started/
    - https://oss.anchore.com/docs/guides/vulnerability/interpreting-results/#reading-table-output
- Now, we are going to run this against the alpine image we used last class for the lab
    - `docker run aquasec/trivy:0.69.3 image nginx:1.29-alpine`
    - `docker run anchore/grype nginx:1.29-alpine`
- Notice the difference between what the tools report by default. Add the `--only-fixed` flag to the grype command to see only the vulnerabilities that have fixes available. Now the two tools will report similar results. 
- All scanning tools have their own features and capabilities, so it is worthwhile to dive into whichever tool customization options you may want in a production environment. Most of these are easily integrated into CI/CD pipelines to automate the scanning process.

## Lab 2: Creating a Hardened Image
In this lab, we will create a hardened version of the nginx:1.29-alpine and python:3.12-alpine images.
- We will focus on fixable vulnerabilities first, then take a look at the unfixable ones and see if we can mitigate them in other ways.
- Start by creating a Dockerfile for each image and doing a container-wide package update (Alpine uses apk for package management).
- Re-scan the image after the update to see how many vulnerabilities have been fixed (note: you need to specify the image created from your Dockerfile, not the base image).
- Use the following command for Trivy to scan the image you built (replace the name) as it is running inside a container and needs access to the Docker socket to see the images on your local machine: 
```
docker run --rm \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v $HOME/Library/Caches:/root/.cache \
  aquasec/trivy:0.69.3 image my-nginx
```
- Use this for Grype. Same concept:
```
docker run --rm \
  -v /var/run/docker.sock:/var/run/docker.sock \
  anchore/grype my-nginx
```
- By the end, you should have no fixable vulnerabilities in the images.

## Assignment: Hardened Dockerfiles
Use the dockerfiles you have been using for the last few assignments, scan them with the tools we used today (and others if you feel so inclined), and create hardened versions of those images.
- When scanning with Trivy and Grype (with the --only-fixed flag), you should have no fixable vulnerabilities in your images.

Piep waz hear! 
