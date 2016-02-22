This playbook (`test.yml`) installs pip, uses pip to install python dependencies,
copies a compressed docker image to the target, loads that docker image, from the
tar file, then starts a container from the new image.

# Instructions
You may need to change the hosts group name from `- hosts: aws` to the hosts name
you defined in the `/etc/ansible/hosts` file, then run:

```
ansible-playbook test.yml
```

# Details

## Install pip using a third party role:
This section will execute the `main.yml` task in `./roles/ansible-pip/tasks/`
directory.
```yaml
roles:
    - ansible-pip
```

## Install the docker-py python library
`"{{ item }}"` is replaced with items from the `with_items` array.
```yaml
- name: Docker-PY
      become: yes
      pip:
        name: "{{ item }}"
      with_items:
      - six==1.4
      - docker-py==1.1.0
```

## Copy docker image tar file to target machine
```yaml
- name: Copy docker image to target 
      copy: src=./myimage.docker.tar dest=/tmp/
```

## Import docker image tar file
```yaml
- name: Import docker image
      shell: docker load -i /tmp/myimage.docker.tar
```

## Run the imported image
```yaml
- name: Run docker image
      docker:
        name: imageinst
        image: myimage
        state: present
```
