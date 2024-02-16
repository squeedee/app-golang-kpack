# app-golang-kpack

forced a build

## Creating the Workload

```
tanzu apps workload create app-golang-kpack \
  --namespace dev \
  --git-branch main \
  --git-repo https://github.com/carto-run/app-golang-kpack \
  --label apps.tanzu.vmware.com/has-tests=true \
  --label app.kubernetes.io/part-of=app-golang-kpack \
  --param-yaml testing_pipeline_matching_labels='{"apps.tanzu.vmware.com/pipeline":"golang-pipeline"}' \
  --type web \
  --yes
```

### Golang Pipeline

```
apiVersion: tekton.dev/v1beta1
kind: Pipeline
metadata:
  labels:
    apps.tanzu.vmware.com/pipeline: golang-pipeline
  name: developer-defined-golang-pipeline
  namespace: dev
spec:
  params:
  - name: source-url
    type: string
  - name: source-revision
    type: string
  tasks:
  - name: test
    params:
    - name: source-url
      value: $(params.source-url)
    - name: source-revision
      value: $(params.source-revision)
    taskSpec:
      params:
      - name: source-url
        type: string
      - name: source-revision
        type: string
      stepTemplate:
        securityContext:
          runAsUser: 1000
          runAsNonRoot: true
          allowPrivilegeEscalation: false
          capabilities:
            drop:
            - ALL
          seccompProfile:
            type: RuntimeDefault
      steps:
      - image: golang
        name: test
        env:
        - name: HOME
          value: /go
        script: |
          cd `mktemp -d`
          wget -qO- $(params.source-url) | tar xvz -m
          go test ./...
```

## Logs

```
tanzu apps workload tail app-golang-kpack
```

## Configuration

| Item            | Config                                                                                |
| --------------- | ------------------------------------------------------------------------------------- |
| Scan Policy     | [default](resources/scan-policy.yaml)                                                 |
| Pipeline        | [developer-defined-golang-pipeline](resources/developer-defined-golang-pipeline.yaml) |
| tap-values.yaml | na                                                                                    |
| Supply Chain    | source-test-scan-to-url                                                               |

