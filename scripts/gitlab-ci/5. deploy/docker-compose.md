### Deploy tagged image with docker-compose file

```yaml
deploy to production:
  stage: deploy
  needs:
    - build tagged docker image
  before_script:
    - export IMAGE_TAG=${CI_REGISTRY_IMAGE}/${CI_PROJECT_NAME}:${CI_COMMIT_TAG:1}
    - docker login -u $CI_REGISTRY_USER -p $CI_REGISTRY_PASSWORD $CI_REGISTRY
  script:
    - docker pull ${IMAGE_TAG}
    - docker compose -f build/compose/docker-compose.production.yml up -d
  tags:
    - shell
  only:
    refs:
      - tags
```