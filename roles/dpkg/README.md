# `homelab_alloy.dpkg`

## Description

This role creates a small pipeline for processing Debian Package Manager (`dpkg`) logs.

### Reasoning

As users utilize the a host, it is sometimes useful to know what got installed, and when. In particular, it is often good to know if a machine has something installed on it that it doesn't need or was added by accident.

You can get much of the way there by monitoring `apt` logs instead, but you can catch other installation methods (such as using `.deb` files).

### Method

The resulting configuration file will have a `loki.source.file` and a `loki.process` component. The output is forwarded to a `loki.write` component after being processed.

## Example Playbook

```yaml
- name: Run homelab_alloy
  hosts: all
  become: true
  roles:
    - role: yjohnsondev.homelab_alloy.dpkg
      when: ansible_facts['os_family'] == 'Debian'
```

## All Variables

> [!NOTE]
> You can set the `config_name` variable to change the name of the config file itself. It will be added as "`{{ config_name }}.alloy`" if provided, otherwise it will be added as "`{{ role_name }}.alloy`. This **must** be added as a variable for the role and not the play; otherwise, every subsequent role will overwrite it.

The following variables can configure what the role does. Of note, the Grafana Alloy component labels should be changed if you need to reference them from elsewhere.

```yaml
dpkg_log_path: "/var/log/dpkg.log"
loki_source_file_dpkg_label: "dpkg_source"
loki_source_file_dpkg_max_freq: "15m"
loki_source_file_dpkg_job_name: "files/dpkg"
loki_process_dpkg_label: "dpkg_process"
```

## Relevant Documentation

- [dpkg(1) — dpkg — Debian bookworm — Debian Manpages](https://manpages.debian.org/bookworm/dpkg/dpkg.1.en.html)
