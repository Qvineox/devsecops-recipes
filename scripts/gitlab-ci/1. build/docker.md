### Build generic Docker image

```yaml
build test docker image:
  stage: build
  extends: .docker_golang_build_setup # from docker.md
  variables:
    IMAGE_NAME: ${CI_REGISTRY_IMAGE}/${CI_PROJECT_NAME}:${CI_COMMIT_BRANCH}
  script:
    - docker build --build-arg CI_JOB_TOKEN=${CI_JOB_TOKEN} . -t ${IMAGE_NAME} -f ./build/docker/Dockerfile

    - docker login -u $CI_REGISTRY_USER -p $CI_REGISTRY_PASSWORD $CI_REGISTRY
    - docker push ${IMAGE_NAME}

```


### Build tagged Docker image

```yaml
build tagged docker image:
  stage: build
  rules:
    - if: $CI_COMMIT_TAG
  extends: .docker_golang_build_setup # from docker.md
  variables:
    IMAGE_NAME: ${CI_REGISTRY_IMAGE}/${CI_PROJECT_NAME}:${CI_COMMIT_BRANCH}
  script:
    - docker build --build-arg CI_JOB_TOKEN=${CI_JOB_TOKEN} . -t ${IMAGE_NAME} -f ./build/docker/Dockerfile

    - docker login -u $CI_REGISTRY_USER -p $CI_REGISTRY_PASSWORD $CI_REGISTRY
    - docker push ${IMAGE_NAME}

```