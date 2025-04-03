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

## `__loki_source_file.j2`

Variables:

```yaml
__loki_source_file_comment_header:                # Optional, preppended to the template

__loki_source_file_label:                         # Required
__loki_source_file_path_list:                     # List of strings, where each string is the absolute path to the file to log
__loki_source_file_target_labels_dict:            # Optional, defines the labels to be appended to loglines for the given path
__loki_source_file_receiver_list:                 # Required, must be in the format of loki.<COMPONENT>.<LABEL>.receiver

__loki_source_file_encoding:                      # String, see docs
__loki_source_file_legacy_positions_file:         # String
__loki_source_file_tail_from_end:                 # Boolean

__loki_source_file_decompression:                 # Boolean, required if other related settings are used 
__loki_source_file_decompression_enabled:         # Boolean
__loki_source_file_decompression_format:          # String, see docs
__loki_source_file_decompression_initial_delay:   # String

__loki_source_file_file_watch:                    # Boolean, required if other related settings are used
__loki_source_file_file_watch_max_poll_frequency: # String
__loki_source_file_file_watch_min_poll_frequency: # String
```

