# FFMPEG Linux Alpine 3.7 Docker images

These images were heavily inspired by [this image](https://hub.docker.com/r/jrottenberg/ffmpeg/).
However with a lot changes like:

 - no more single Dockerfile (3 image layers: dependencies, build-stage, runtime)
 - no more forced multi-stage builds except those cases when final stage is vanilla operating system
 - alpine 3.7
 - ffmpeg 4.0 by default

## Dependencies

[Dependencies image](dependencies) contains all necessary libraries for FFMPEG built from source.

## Build stage

[Build-stage image](build-stage) contains only build of an FFMPEG with a copy of shared objects from the dependency image.
Image size: 564MB

## Runtime

[Runtime image](runtime) contains a copy of binaries, includes, libraries and shares, i.e all necessary pieces for an FFMPEG.
Image size: 69.5MB

## Example application

[ffmpeg tool image](example/ffmpeg-cli) is basically a containerized version of ffmpeg CLI tool. More to come.
Image size: 69.5MB at minimum

## Custom runtime

In order to get all necessary ffmpeg pieces you need to create a Dockerfile with the following instructions:
```dockerfile
FROM denismakogon/ffmpeg-alpine:4.0-runtime as build-stage
FROM <your-final-stage-image>

COPY --from=build-stage /tmp/fakeroot/bin /usr/local/bin
COPY --from=build-stage /tmp/fakeroot/share /usr/local/share
COPY --from=build-stage /tmp/fakeroot/include /usr/local/include
COPY --from=build-stage /tmp/fakeroot/lib /usr/local/lib
```
