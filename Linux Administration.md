# Linux
#linux


By default `apt-key add` adds a key to `/etc/apt/trusted.gpg` . To list all the keys:
```
 sudo apt-key list
```

To remove gpg key from it issue the command:

```
sudo apt-key del <last-2-chuncks-id>
```

To remove a repository from apt packege manager:

```bash
sudo add-apt-repository -r <package-repo-name>
```

