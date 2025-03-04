### Publish tagged package to Gitlab

```yaml
publish package:
  stage: publish
  image: alpine/curl:latest
  only:
    refs:
      - tags
  variables:
    PACKAGE_NAME: ${CI_PROJECT_NAME}
    VERSION: ${CI_COMMIT_TAG:1}
    PACKAGE_FILE_NAME: ${CI_PROJECT_NAME}.${CI_COMMIT_TAG:1}.tar.gz
  script:
    - tar -czf ${PACKAGE_FILE_NAME} .
    - 'curl --header "JOB-TOKEN: ${CI_JOB_TOKEN}" --upload-file ${PACKAGE_NAME}.${VERSION}.tar.gz ${CI_API_V4_URL}/projects/${CI_PROJECT_ID}/packages/generic/${PACKAGE_NAME}/${VERSION}/${PACKAGE_FILE_NAME}'
```