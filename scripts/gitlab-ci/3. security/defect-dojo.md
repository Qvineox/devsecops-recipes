### Export trivy findings file to DefectDojo

```yaml
upload trivy findings to defectdojo:
  stage: test
  image:
    name: alpine/curl:latest
  needs:
    - build docker image
    - trivy image scan
  variables:
    DEFECTDOJO_ENGAGEMENT_NAME: $CI_PROJECT_NAME:$CI_COMMIT_BRANCH $CI_COMMIT_SHORT_SHA
    DEFECTDOJO_API_URL: "https://defectdojo.domain.com/api/v2"
    
    AUTH_HEADER: "Authorization: Token ${DEFECTDOJO_API_TOKEN}"
    CONTENT_TYPE: "Content-Type: multipart/form-data"
  script:
   - curl -X POST '${DEFECTDOJO_API_URL}/engagements/' -H '$AUTH_HEADER' -H '$CONTENT_TYPE' -F 'name=${DEFECTDOJO_ENGAGEMENT_NAME}' -F 'product=1' -F 'build_id=${CI_JOB_ID}' -F 'commit_hash=${CI_COMMIT_SHA}' -F 'branch_tag=${CI_COMMIT_BRANCH}' -F 'target_start=$(date +"%Y-%m-%d")' -F 'target_end=$(date +"%Y-%m-%d")' -F 'engagement_type=Interactive' -F 'deduplication_on_engagement=true'

   - curl -X POST '${DEFECTDOJO_API_URL}/import-scan/' -H '$AUTH_HEADER' -H '$CONTENT_TYPE' -F 'minimum_severity=Warning' -F 'active=true' -F 'verified=true' -F 'scan_type=Trivy Scan' -F 'close_old_findings=true' -F 'push_to_jira=false' -F 'product_name=Watcher App' -F 'engagement_name=${DEFECTDOJO_ENGAGEMENT_NAME}' -F 'file=@$CI_PROJECT_DIR/trivy_report_full.json'
 tags:
   - docker
```
