# Settting up SSH from gitlab action
The current setup forwards ssh requests to the Promox node that serves as the kubernetes control node(192.168.1.104)
## If necessary create a new ssh key-pair
Most likely the last one you used will still work as expected, but if necessary create a new one using the command below. Preferable create it without a passphrase
```
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
```

## Set the private key as secret on github action

## Add the public key to the remote host
```
ssh-copy-id -i ~/.ssh/id_rsa.pub user@remote-server
```

Alternatively, manually add the content of key.pub to the ~/.ssh/authorized_keys file on the remote server(192.168.1.104).

## Setup pipeline
Example Pipeline:
```
  jobs: 
    build:
      runs-on: ubuntu-latest

      steps: 
        - name: Install SSH Key
          uses: shimataro/ssh-key-action@v2
          with:
            key: ${{ secrets.SSH_PRIVATE_KEY }} 
            known_hosts: 'just-a-placeholder-so-we-dont-get-errors'
            
        - name: Adding Known Hosts
          run: ssh-keyscan -H ${{  secrets.SSH_HOST }} >> ~/.ssh/known_hosts

        - name: Debug SSH
          run: |
            ssh -vvv sofien@${{  secrets.SSH_HOST }} ls -la

```
