# `homelab-alloy.prometheus_remote_write`

## Description

This role creates a `prometheus.remote_write` component for other components to forward to. It doesn't do anything by itself, but it can serve the function of defining a target for other components (perhaps declared in other roles in this collection) to use.

### Endpoints

The `prometheus_endpoints` list contains dictionaries, and each individual entry must have the `url` key set. For Grafana Alloy, the `url` field is a required value, but the rest of the fields are optional; that said, you can include them here if you wish to change the way the endpoint is configured.

> [!WARNING]
> Note that these values are passed as is using Jinja, so they can sometimes be misinterpreted as strings rather than objects. Alloy is picky when it comes to this.

### External Labels

You can optionally set the `prometheus_external_labels` variable to add the `external_labels` block to the components. The `prometheus_external_labels` variable is a dictionary.

> [!WARNING]
> Note that these values are passed as is using Jinja, so they can sometimes be misinterpreted as strings rather than objects. Alloy is picky when it comes to this.

## Example Playbook

This playbook will create multiple endpoints within the same `prometheus.remote_write` component; thus, they will share the `external_labels` values. If this is undesired, run the role multiple times, each with a separate `config_name` and `prometheus_remote_write_label` to avoid overwriting and errors, respectively.

```yaml
- name: Run homelab_alloy
  hosts: all
  become: true
  roles:
    - role: yjohnsondev.homelab_alloy.prometheus_remote_write
      vars:
        prometheus_endpoints:
          - url: https://telemetry.example.com/api/v1/write
            name: "Main Telemetry Server"
          - url: "https://offsite-telemetry.example.com/api/v1/write"
            name: "Secondary Telemetry Server"
        prometheus_external_labels:   # optional
          - environment: production
          - address: "{{ ansible_default_ipv4.address }}"

    # If you want multiple, different, endpoints.
    - role: yjohnsondev.homelab_alloy.prometheus_remote_write
      vars:
        prometheus_endpoints:
          - url: http://test-telemetry.example.com/api/v1/write
            name: "Testing Telemetry Server"
        prometheus_external_labels:   # optional
          - environment: testing
        
        # The following are required if you want to have multiple independent enpoint targets.
        config_name: "remote_write.test"  # Output is "remote_write.test.prometheus.alloy"
        prometheus_remote_write_label: "testing"
```

## All Variables

You can set the `config_name` variable to change the name of the config file itself. It will be added as "`{{ config_name }}.prometheus.alloy`" if provided, otherwise it will be added as "`remote_write.prometheus.alloy`". **This must to be added as a variable for the role and not the play**, otherwise every subsequent role will overwrite it.

The following variables can configure what the role does. Of note, the Grafana Alloy component labels should be changed if you need to reference them from elsewhere.

| Variable                        | Function                                                   | Default        |
| ------------------------------- | ---------------------------------------------------------- | -------------- |
| `config_name`                   | Name prepended to the configuration file.                  | `remote_write` |
| `prometheus_remote_write_label` | Label for the `prometheus.remote_write` component (string) | `"default"`    |
| `prometheus_external_labels`    | External labels to append to metrics (list of dicts)       | `[]`           |
| `prometheus_endpoints`          | Endpoints for `prometheus.remote_write` (list of dicts)    | `[]`           |

## Relevant documentation

- [prometheus.remote\_write | Grafana Alloy documentation](https://grafana.com/docs/alloy/latest/reference/components/prometheus/prometheus.remote_write/)
