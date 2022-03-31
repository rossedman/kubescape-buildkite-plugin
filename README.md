# Kubescape Buildkite Plugin

A [Buildkite plugin](https://buildkite.com/docs/plugins) that runs [Kubescape](https://github.com/armosec/kubescape) against static Kubernetes manifests.

Since Kubescape can also output JUnit format, the build can be annotated with remediation steps directly from the the Kubescape results.

## Example

The following pipeline will analyze a set of configuration placed in the `config` directory using the `nsa` framework and then outputting results in `junit` format. The `junit-annotate` plugin is then used to annotated the build for formatted output.

```yaml
steps:
- name: kubescape
  key: kubescape
  plugins:
  - rossedman/kubescape#main:
      directory: config
      framework: nsa
      format: junit
      results: results.xml
  artifact_paths:
    - "results.xml"
- wait: ~
  continue_on_failure: true
# using the junit annotate plugin will annotate the build
# with remediation steps if format is set to `junit` above
- name: annotate
  plugins:
    - junit-annotate#v2.0.2:
        artifacts: results.xml
```

## Configuration

### `directory`

Default: `config`

The directory of manifests to scan. This does a recursive glob through a directory and looks for any files that match a `*.yaml` pattern.

### `framework`

Default: `nsa`

Can be set to `nsa` or `mitre`

### `format`

Default: `json`

Can be set to `json`, `junit` or `pdf`. Determines the type of output Kubescape will return.

### `results`

Default: `results.json`

Set to any file or filepath to write the results to.
