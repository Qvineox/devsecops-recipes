### Golang Docker build cache setup

```yaml
.docker_golang_build_setup:
  image: docker:27.3.1
  variables:
    GOPATH: $CI_PROJECT_DIR/.go
  before_script:
    - mkdir -p .go
  cache:
    paths:
      - .go/pkg/mod/
      - go.sum
  tags:
    - docker
```