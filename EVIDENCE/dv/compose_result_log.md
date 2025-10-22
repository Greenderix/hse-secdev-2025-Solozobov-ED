```angular2html

/usr/local/bin/docker compose -f /Users/evgenysolozobov/IdeaProjects/hh2/docker-compose.yml -p hh2 up -d
WARN[0000] /Users/evgenysolozobov/IdeaProjects/hh2/docker-compose.yml: the attribute version is obsolete, it will be ignored, please remove it to avoid potential confusion 
[+] Running 0/0
[+] Running 0/1 Building0.1s 
[+] Building 80.0s (17/17) FINISHED                                                                docker:default
 => [app internal] load build definition from Dockerfile                                                     0.0s
 => => transferring dockerfile: 441B                                                                         0.0s
 => [app internal] load metadata for docker.io/library/openjdk:21-jdk-slim                                   2.5s
 => [app internal] load metadata for docker.io/library/gradle:8.7-jdk21                                      2.5s 
 => [app auth] library/openjdk:pull token for registry-1.docker.io                                           0.0s 
 => [app auth] library/gradle:pull token for registry-1.docker.io                                            0.0s 
 => [app internal] load .dockerignore                                                                        0.0s
 => => transferring context: 2B                                                                              0.0s
 => [app build 1/5] FROM docker.io/library/gradle:8.7-jdk21@sha256:afba668dbed80665ed2cee8b16010c51e9639be3  0.0s
 => => resolve docker.io/library/gradle:8.7-jdk21@sha256:afba668dbed80665ed2cee8b16010c51e9639be3424c3f8976  0.0s 
 => [app stage-1 1/3] FROM docker.io/library/openjdk:21-jdk-slim@sha256:7072053847a8a05d7f3a14ebc778a90b38c  0.0s
 => => resolve docker.io/library/openjdk:21-jdk-slim@sha256:7072053847a8a05d7f3a14ebc778a90b38c50ce7e8f1993  0.0s
 => [app internal] load build context                                                                        0.1s
 => => transferring context: 5.56MB                                                                          0.1s
 => CACHED [app build 2/5] WORKDIR /app                                                                      0.0s
 => [app build 3/5] COPY build.gradle.kts settings.gradle.kts ./                                             0.0s
 => [app build 4/5] COPY --chown=gradle:gradle . .                                                           0.1s
 => [app build 5/5] RUN gradle dependencies --refresh-dependencies &&     gradle build -x test --no-daemon  75.9s
 => CACHED [app stage-1 2/3] WORKDIR /app                                                                    0.0s
 => [app stage-1 3/3] COPY --from=build /app/build/libs/*.jar app.jar                                        0.0s
 => [app] exporting to image                                                                                 1.0s
 => => exporting layers                                                                                      0.8s
 => => exporting manifest sha256:11bdee30d33502b12de4403937f45943fb96022873b007c2f2afb72229648e16            0.0s
 => => exporting config sha256:81efb4affd16add626679fa84e494d37ee7467784328dd26a91235f543de24d2              0.0s
 => => exporting attestation manifest sha256:a131f0219a5481faf9e8e91422b55df1f334b2f75dbb3749d816e541f250ee  0.0s
 => => exporting manifest list sha256:5470a7f63f9e1486f229ecbe69e92b12d46592416c1253e3426dc5b003bd4e14       0.0s
 => => naming to docker.io/library/hh2-app:latest                                                            0.0s
[+] Running 3/3g to docker.io/library/hh2-app:latest                                                         0.2s
 ✔ Service app              Built                                                                           80.1s 
 ✔ Network hh2_app-network  Created                                                                          0.0s 
 ✔ Container hh2-app-1      Started                                                                          0.2s
```