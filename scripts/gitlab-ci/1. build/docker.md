### Build generic Docker image

```yaml
build test docker image:
  stage: build
  extends: .docker_golang_build_setup # from docker.md
  before_script:
    - export IMAGE_TAG=${CI_REGISTRY_IMAGE}/${CI_PROJECT_NAME}:${CI_COMMIT_BRANCH}
  script:
    - docker build --build-arg CI_JOB_TOKEN=${CI_JOB_TOKEN} . -t ${IMAGE_TAG} -f ./build/docker/Dockerfile

    - docker login -u $CI_REGISTRY_USER -p $CI_REGISTRY_PASSWORD $CI_REGISTRY
    - docker push ${IMAGE_TAG}

```


### Build tagged Docker image

```yaml
build tagged docker image:
  stage: build
  rules:
    - if: $CI_COMMIT_TAG
  extends: .docker_golang_build_setup # from docker.md
  before_script:
    # https://docs.gitlab.com/user/project/releases/release_cicd_examples/#create-a-release-when-a-git-tag-is-created
    - export IMAGE_TAG=${CI_REGISTRY_IMAGE}/${CI_PROJECT_NAME}:${CI_COMMIT_TAG:1}
  script:
    - docker build --build-arg CI_JOB_TOKEN=${CI_JOB_TOKEN} . -t ${IMAGE_TAG} -f ./build/docker/Dockerfile

    - docker login -u $CI_REGISTRY_USER -p $CI_REGISTRY_PASSWORD $CI_REGISTRY
    - docker push ${IMAGE_TAG}

```