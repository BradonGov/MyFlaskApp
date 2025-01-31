pre-requisites:
1. generate ssh key pair
'''
ssh-keygen -t rsa -b 4096
'''
this will create the keys.

1.1 Move RSA Key to local server
copy the key to the server you want to connect to.
'''
cat .ssh/id_rsa.pub | ssh SERVERNAME@SERVER_IP_ADDRESS 'cat >> .ssh/authorized_keys'
'''

this will copy the rsa keys to the server, but with using ssh, you will need to have port 22 enables or change the ssh port number.

1.2 Move RSA Key to github
open repository, click setting tab, click secrets and variables, then click "new repository secret"
The names don't have to be this, as long as they match the names in the github action script.
DEV_SERVER_IP: ACTUALIPADRESS the ip address of the server you want to connect to
DEV_USERNAME: SERVERNAME the username of the server you want to connect to
SSH_PRIVATE_KEY: RSAKEY the private key generated in step 1

2. Create a batch file to run on the server
on the server, create a .bat file that will pull from the repository and run the server.
'''
cd C:\Users\blbarfuss\Documents\myFlaskApp\MyFlaskApp
git pull origin main
echo add a space at the end of a file
echo Flask app updated

pause
'''



1. Set up github actions to connect to server
github action script:
'''
name: Update Production Server

on:
    push:
        branches:
            - main

jobs:
    deploy:
        runs-on: ubuntu-latest

        steps:
            - name: Checkout code
              uses: actions/checkout@v3

            - name: Connect to production server and run batch script
              uses: appleboy/ssh-action@master
              with:
                    host: ${{ secrets.DEV_SERVER_IP }}
                    username: ${{ secrets.DEV_USERNAME }}
                    key: ${{ secrets.SSH_PRIVATE_KEY }}
                    script: |
                        cd C:\Users\blbarfuss\Desktop\batchfile.bat #location of the batch file on the server
'''


