# Templates

## `__local_file.j2`

Example:

```yaml
- name: Generate local file components
  ansible.builtin.set_fact:
    local_file_templates: "{{ local_file_templates | default('') + lookup('template', '__local_file.j2') }}"
  vars:
    __local_file_comment_header: '{{ item }}'
    __local_file_label: '{{ item | replace(" ", "_") | lower }}'
    __local_file_path: '{{ plex_logs_dir + "/" + item + ".log" }}'
    __local_file_detector: '{{ local_file_detector }}'
    __local_file_poll_frequency: '{{ local_file_poll_frequency }}'
    __local_file_is_secret: '{{ local_file_is_secret }}'
  loop: '{{ plex_logs }}'
```
