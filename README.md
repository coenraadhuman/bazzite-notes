# bazzite-notes
My notes on setting up Bazzite as a console with Docker running in the background to host services. Making most use of the hardware available on the machine!

## Docker

### Installation 

```bash
# Installing it:
rpm-ostree install docker-compose

# Enabling it:
sudo systemctl enable docker.service
```

[Source](https://www.reddit.com/r/Bazzite/comments/1jrjqrv/docker_en_bazzite/?tl=en)

### Configure User

> Due to how rpm-ostree handles user + group entries, it may not be possible to use usermod -a -G to add a user to a group successfully. Until rpm-ostree moves to using systemd sysusers, users will have to populate the /etc/group file from the /usr/lib/group file before they can add themselves to the group.

```bash
grep -E '^docker:' /usr/lib/group | sudo tee -a /etc/group

sudo usermod -aG docker $USER
```

[Source](https://docs.fedoraproject.org/en-US/fedora-silverblue/troubleshooting/#_unable_to_add_user_to_group)

### Bind Mounts - SELinux

> If you use SELinux, you can add the z or Z options to modify the SELinux label of the host file or directory being mounted into the container. This affects the file or directory on the host machine itself and can have consequences outside of the scope of Docker.

> The z option indicates that the bind mount content is shared among multiple containers.

> The Z option indicates that the bind mount content is private and unshared.

> Use extreme caution with these options. Bind-mounting a system directory such as /home or /usr with the Z option renders your host machine inoperable and you may need to relabel the host machine files by hand.

```yaml
services:
  something:
    ...
    volumes:
      # Recommended to use z if you are using multiple containers with one/many persistant drives.
      - "${MEDIA_DRIVE_ONE}:/srv/one:z"
      - "${MEDIA_DRIVE_TWO}:/srv/two:z"
      - "${MEDIA_DRIVE_THREE}:/srv/three:z"
      - "${MEDIA_DRIVE_FOUR}:/srv/four:z"
```

[Source](https://docs.docker.com/engine/storage/bind-mounts/#configure-the-selinux-label)
