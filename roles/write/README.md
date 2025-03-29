# `homelab-alloy.write`

## Description

This role creates `prometheus.remote_write` and `loki.write` components for other components to forward to. It doesn't do anything by itself, but it can serve the function of defining a target for other components (perhaps declared in other roles in this collection) to use.

The only required values are:

- A list named `prometheus_endpoints` (when `enable_prometheus` is `true`)
- A list named `loki_endpoints` (when `enable_loki` is `true`)

### Endpoints

Each list contains dictionaries, and each entry must have the `url` key set. For Grafana Alloy, the `url` field is a required value, but the rest of the fields are optional; that said, you can include them here if you wish to change the way the endpoint is configured.

> [!warning] Current limitations
> Note that these values are passed as is using Jinja, so they can sometimes be misinterpreted as strings rather than objects. Alloy is picky when it comes to this.

### External Labels

You can optionally set the `prometheus_external_labels` or `loki_external_labels` variables to add the `external_labels` block to the components.

## Example Playbook

```yaml
- name: Run homelab_alloy
  hosts: all
  become: true
  roles:
    - role: yjohnsondev.homelab_alloy.write
      vars:
        prometheus_endpoints:
          - url: http://telemetry.example.com/api/v1/write
            name: "Telemetry Server"
          - url: "http://offsite-telemetry.example.com/api/v1/write"
            name: "secondary"
        prometheus_external_labels:   # optional
          - environment: production
          - address: "{{ ansible_default_ipv4.address }}"

        loki_endpoints:
          - url: http://telemetry.example.com/loki/api/v1/push
        loki_external_labels:         # optional
          - environment: production
```

## All Variables
The following variables can configure what the role does. Of note, the Grafana Alloy component labels should be changed if you need to reference them from elsewhere.

### Prometheus

| Variable                        | Function                                                   | Default     |
| ------------------------------- | ---------------------------------------------------------- | ----------- |
| `enable_prometheus`             | Enable Prometheus configs (boolean)                        | `true`      |
| `prometheus_remote_write_label` | Label for the `prometheus.remote_write` component (string) | `"default"` |
| `prometheus_external_labels`    | External labels to append to metrics (list of dicts)       | `[]`        |
| `prometheus_endpoints`          | Endpoints for `prometheus.remote_write` (list of dicts)    | `[]`        |

### Loki

| Variable               | Function                                          | Default     |
| ---------------------- | ------------------------------------------------- | ----------- |
| `enable_loki`          | Enable Loki configs (boolean)                     | `true`      |
| `loki_write_label`     | Label for the `loki.write` component (string)     | `"default"` |
| `loki_external_labels` | External labels to append to logs (list of dicts) | `[]`        |
| `loki_endpoints`       | Endpoints for `loki.write` (list of dicts)        | `[]`        |

## Relevant documentation

- [loki.write | Grafana Alloy documentation](https://grafana.com/docs/alloy/latest/reference/components/loki/loki.write/)
- [prometheus.remote\_write | Grafana Alloy documentation](https://grafana.com/docs/alloy/latest/reference/components/prometheus/prometheus.remote_write/)