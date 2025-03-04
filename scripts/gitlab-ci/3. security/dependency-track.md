### Upload SBOM file to Dependency Track

```yaml
upload to dependency track:
  stage: test
  image: alpine/curl:latest
  allow_failure: true
  needs:
    - job: trivy container scan
      artifacts: true
    - job: trivy filesystem scan
      artifacts: true
  script:
    - |
      curl --fail -X "POST" "$DT_API_URL/api/v1/bom" \
        -H 'Content-Type: multipart/form-data' \
        -H "X-Api-Key: $DT_API_KEY" \
        -F "autoCreate=true" \
        -F "projectName=$CI_PROJECT_NAME" \
        -F "bom=@$CI_PROJECT_DIR/gl-code-sbom.cdx.json"
    - |
      curl --fail -X "POST" "$DT_API_URL/api/v1/bom" \
        -H 'Content-Type: multipart/form-data' \
        -H "X-Api-Key: $DT_API_KEY" \
        -F "autoCreate=true" \
        -F "parentName=$CI_PROJECT_NAME" \
        -F "projectName=$CI_PROJECT_NAME-image" \
        -F "bom=@$CI_PROJECT_DIR/gl-container-sbom.cdx.json"
  only:
    refs:
      - master
```