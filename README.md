# app-golang-kpack

## Creating the Workload

```
tanzu apps workload create app-golang-kpack \
  --namespace dev \
  --git-branch main \
  --git-repo https://github.com/carto-run/app-golang-kpack \
  --label apps.tanzu.vmware.com/has-tests=true \
  --label app.kubernetes.io/part-of=app-golang-kpack \
  --type web \
  --yes
```

## Logs

```
tanzu apps workload tail app-golang-kpack
```

## Configuration

| Item            | Config                                                                                |
| --------------- | ------------------------------------------------------------------------------------- |
| Scan Policy     | [default](resources/scan-policy.yaml)                                                 |
| Pipeline        | [developer-defined-tekton-pipeline](resources/developer-defined-tekton-pipeline.yaml) |
| tap-values.yaml | na                                                                                    |
| Supply Chain    | source-test-scan-to-url                                                               |

