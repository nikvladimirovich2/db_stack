# DB Stack Runbook

## Deployment
1. Install ansible on host and install docker on client (use official documentation for your distributive)
2. Copy archive db_stack.tar.gz on your ansible server or clone git repository with command `git clone https://github.com/nikvladimirovich2/db_stack.git`
3. Unpackage archive db_stack.tar.gz with command `tar -zcf db_stack.tar.gz`
4. Change current directory to `cd db_stack/`
5. Change postgres password with command `ansible-vault edit db_stack/vars/vault.yaml` vault password is `12345678`
6. Change defaulth vault password if it needed with command `ansible-vault rekey db_stack/vars/vault.yaml`
7. Make ssh-key for ansible user `ssh-keygen` and put in on client server in file /{user}/.ssh/authorized_keys
8. Change inventory file `nano inventory.yaml` with your credentials
9. Check connection with command `ansible db_servers -m ping` answer has to be `db1 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}`
4. Run `ansible-playbook site.yaml --vault-id @prompt` (enter your vault password).

## Verification
- Check: `docker ps` should show 2 healthy containers.
- Backups: Check /srv/backups/postgres after cron run.

## Restore from Backup
1. Create new database: `docker exec postgres psql -U {user} -d {db} -c "create database {new_db};"` put your creds instead of {..}
2. Restore: `docker exec -ti postgres bash` and `psql -h 127.0.0.1 -p 5432 -U {user} -d {db}</backups/postgres/{your_dump}.sql`.