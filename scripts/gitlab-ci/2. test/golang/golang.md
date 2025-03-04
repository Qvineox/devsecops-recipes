### Run Golang unit tests with Gitlab coverage

```yaml
.go-cache:
  variables:
    GOPATH: $CI_PROJECT_DIR/.go
  before_script:
    - mkdir -p .go
  cache:
    paths:
      - .go/pkg/mod/
      - go.sum

unit tests:
  stage: test
  image: golang:1.23.3
  environment:
    name: Test
  extends:
    - .go-cache
  script:
    - go mod tidy
    - go run gotest.tools/gotestsum@latest --junitfile report.xml --format testname

    - go test -v -coverprofile=profile.cov ./...
    - go tool cover -func profile.cov
  coverage: '/\(statements\)(?:\s+)?(\d+(?:\.\d+)?%)/'
  artifacts:
    reports:
      junit: report.xml
    expire_in: 2 days
    when: always

```