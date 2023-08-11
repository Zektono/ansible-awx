# Conjur integration with Ansible
This repository contains Ansible modules for interacting with Conjur.

## Pre-requisites
- Unix-like operating system (macOS, Linux, etc.)
  - Fedora (maintained versions)
  - Ubuntu LTS (18, 20)
  - Red Hat Enterprise Linux 8, CentOS Stream 8
  - macOS (11+)
- Docker / Docker Compose
- Ansible (cli)
    
## Installation

### Conjur OSS
##### Generate and store a master data key that will be used (later) to encrypt the database:
``` bash
# Generate key and store in environment variable;
export CONJUR_DATA_KEY="$(docker-compose run --no-deps --rm conjur data-key generate)"
```

##### Start all the services of Conjur OSS:
``` bash
docker-compose up -d openssl postgres conjur pgadmin proxy client
```
Your conjur environment is now ready to use.

##### Alternatively you can use the `run.sh` script to start conjur OSS:
``` bash
sh ./run.sh conjur-oss
```

### Create admin account in Conjur OSS
##### Set your account name in an environment variable:
``` bash
export CONJUR_ADMIN_USERNAME=myConjurAccount
```

##### Create a new admin account in the conjur server:
``` bash
docker-compose exec conjur conjurctl account create $CONJUR_ADMIN_USERNAME | tee admin.out
```

In your console output and in admin.out you will see the certificate and API key for the admin account.


##### Initialize the admin account in the Conjur client (CLI):
``` bash
docker-compose exec client conjur init -u https://proxy -a $CONJUR_ADMIN_USERNAME --self-signed
```

### Ansible AWX
Run:
``` bash
sh ./run.sh ansible-awx
```

This will start postgres, redis and the AWX backend.
Moreover this will setup the database scheme for AWX.
