### Trivy multiscan with reports and SBOM

```yaml
trivy image scan:
  stage: test
  allow_failure: false
  image:
    name: aquasec/trivy:latest
    entrypoint: [ "" ]
  variables:
    GIT_STRATEGY: none

    TRIVY_USERNAME: "$CI_REGISTRY_USER"
    TRIVY_PASSWORD: "$CI_REGISTRY_PASSWORD"
    TRIVY_AUTH_URL: "$CI_REGISTRY"

    TRIVY_NO_PROGRESS: "true"
    TRIVY_CACHE_DIR: ".trivycache/"
    TRIVY_INSECURE: "true"
  script:
    # trivy preparation: cache cleanup, vulnerability database update
    - trivy -v
    - trivy image --download-db-only --no-progress --cache-dir .trivycache/

    # search for critical and high vulnerabilities and configuration errors, saving to a file in GitLab format
    - trivy
      --cache-dir .trivycache/
      --scanners vuln,misconfig,secret,license
      --timeout 15m
      --format json
      --output "$CI_PROJECT_DIR/trivy_report_full.json"
      fs .

    # formatting the original trivy scan file into all formats required for work
    - trivy convert --format spdx-json --output gl-sbom-report.cdx.json "$CI_PROJECT_DIR/trivy_report_full.json"
    - trivy convert --format table --output trivy_report_full.txt "$CI_PROJECT_DIR/trivy_report_full.json"
    - trivy convert --format template --template "@/contrib/gitlab.tpl" --output "$CI_PROJECT_DIR/gl-container-scanning-report.json" "$CI_PROJECT_DIR/trivy_report_full.json"
  cache:
    paths:
      - .trivycache/
  artifacts:
    when: always
    expire_in: 2 week
    paths:
      - "$CI_PROJECT_DIR/gl-container-scanning-report.json"
      - "$CI_PROJECT_DIR/gl-sbom-report.cdx.json"
      - "$CI_PROJECT_DIR/trivy_report_full.json"
      - "$CI_PROJECT_DIR/trivy_report_full.txt"
    reports:
      container_scanning: gl-container-scanning-report.json
      cyclonedx: gl-sbom-report.cdx.json
```