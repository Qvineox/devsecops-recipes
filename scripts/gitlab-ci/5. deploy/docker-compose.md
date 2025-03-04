### Deploy tagged image with docker-compose file

```yaml
deploy to production:
  stage: deploy
  rules:
    - if: $CI_COMMIT_TAG
  needs:
    - build tagged docker image
  variables:
    IMAGE_NAME: ${CI_REGISTRY_IMAGE}/${CI_PROJECT_NAME}:${CI_COMMIT_TAG:1}
  before_script:
    - docker login -u $CI_REGISTRY_USER -p $CI_REGISTRY_PASSWORD $CI_REGISTRY
  script:
    - docker pull ${IMAGE_NAME}
    - docker compose -f build/compose/docker-compose.production.yml up -d
  tags:
    - shell
```