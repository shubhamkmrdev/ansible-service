---
- name: Ensure time synchronization services are running
  hosts: all
  tasks:
    - name: Check NTP service status on Linux
      when: ansible_os_family == "Debian"
      become: yes
      command: systemctl status ntp
      register: ntp_status
      ignore_errors: yes  # Continue if this command fails

    - name: Debug NTP status on Linux
      when: ansible_os_family == "Debian"
      debug:
        var: ntp_status
      no_log: true  # Hide debug output

    - name: Start NTP service if it is stopped (Linux)
      when:
        - ansible_os_family == "Debian"
        - ntp_status is defined
        - ntp_status.stdout is defined
        - "'inactive (dead)' in ntp_status.stdout"
      become: yes
      command: systemctl start ntp

    - name: Restart NTP service if it is running (Linux)
      when:
        - ansible_os_family == "Debian"
        - ntp_status is defined
        - ntp_status.stdout is defined
        - "'active (running)' in ntp_status.stdout"
      become: yes
      command: systemctl restart ntp

    - name: Check Windows Time Service status
      when: ansible_os_family == "Windows"
      win_service:
        name: w32time
      register: w32time_status

    - name: Debug Windows Time Service status
      when: ansible_os_family == "Windows"
      debug:
        var: w32time_status
      no_log: true  # Hide debug output

    - name: Start Windows Time Service if it is stopped
      when:
        - ansible_os_family == "Windows"
        - w32time_status.state != 'running'
      win_service:
        name: w32time
        state: started

    - name: Restart Windows Time Service if it is running
      when:
        - ansible_os_family == "Windows"
        - w32time_status.state == 'running'
      win_service:
        name: w32time
        state: restarted
