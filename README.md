## ansible-collection-test

This ansible script is successfully tested on virtual machine by means of **Vagrant**

## Vagrant

This vagrant box used for this test is **"bento/ubuntu-22.04"**, more details can be seen in **Vagrantfile**. 

## Test environment

This test is tested successfuly on **macos arm64 M2**, please find the suitable vagrant box for your own environment.

## Run bash
```bash
vagrant destroy
vagrant up --provider=vmware_fusion
```
