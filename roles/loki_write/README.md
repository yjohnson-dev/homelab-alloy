# `homelab-alloy.loki_write`

## Description

This role creates a `loki.write` component for other components to forward to. It doesn't do anything by itself, but it can serve the function of defining a target for other components (perhaps declared in other roles in this collection) to use.

### Endpoints

The `loki_endpoints` list contains dictionaries, and each entry must have the `url` key set. For Grafana Alloy, the `url` field is a required value, but the rest of the fields are optional; that said, you can include them here if you wish to change the way the endpoint is configured.

> [!WARNING]
> Note that these values are passed as is using Jinja, so they can sometimes be misinterpreted as strings rather than objects. Alloy is picky when it comes to this.

### External Labels

You can optionally set the `loki_external_labels` variable to add the `external_labels` block to the components.

## Example Playbook

This playbook will create multiple endpoints within the same `loki.write` component; thus, they will share the `external_labels` values. If this is undesired, run the role multiple times, each with a separate `config_name` and `loki_write_label` to avoid overwriting and errors, respectively.

```yaml
- name: Run homelab_alloy
  hosts: all
  become: true
  roles:
    - role: yjohnsondev.homelab_alloy.loki_write
      vars:
        loki_endpoints:
          - url: "https://telemetry.example.com/loki/api/v1/push"
            name: "Main Telemetry Server"
          - url: "https://offsite-telemetry.example.com/loki/api/v1/push"
            name: "Secondary Telemetry Server"
        loki_external_labels:   # optional
          - environment: "production"
          - address: "{{ ansible_default_ipv4.address }}"

    # If you want multiple, different, endpoints.
    - role: yjohnsondev.homelab_alloy.loki_write
      vars:
        loki_endpoints:
          - url: "http://test-telemetry.example.com/loki/api/v1/push"
            name: "Testing Telemetry Server"
        loki_external_labels:   # optional
          - environment: "testing"
        
        # The following are required if you want to have multiple independent enpoint targets.
        config_name: "write.test"  # Output is "write.test.loki.alloy"
        loki_write_label: "testing"
```

## All Variables

You can set the `config_name` variable to change the name of the config file itself. It will be added as "`{{ config_name }}.loki.alloy`" if provided, otherwise it will be added as "`write.loki.alloy`. **This must to be added as a variable for the role and not the play**, otherwise every subsequent role will overwrite it.

The following variables can configure what the role does. Of note, the Grafana Alloy component labels should be changed if you need to reference them from elsewhere.

| Variable               | Function                                          | Default     |
| ---------------------- | ------------------------------------------------- | ----------- |
| `config_name`                   | Name prepended to the configuration file.                  | `write` |
| `loki_write_label`     | Label for the `loki.write` component (string)     | `"default"` |
| `loki_external_labels` | External labels to append to logs (list of dicts) | `[]`        |
| `loki_endpoints`       | Endpoints for `loki.write` (list of dicts)        | `[]`        |

## Relevant documentation

- [loki.write | Grafana Alloy documentation](https://grafana.com/docs/alloy/latest/reference/components/loki/loki.write/)
