# DB Stack Runbook

## Deployment
1. Install Ansible on the control node and Docker on the target hosts (refer to official documentation for your distribution)
2. Copy `db_stack.zip` to your Ansible server
3. Extract the archive with command: `unzip db_stack.zip`
4. Navigate to the directory: `cd db_stack-main/`
5. Edit the PostgreSQL password: `ansible-vault edit db_stack/vars/vault.yaml` (vault password: `12345678`)
6. (Optional) Change the vault password with command: `ansible-vault rekey db_stack/vars/vault.yaml`
7. Generate SSH keys for the Ansible user: `ssh-keygen` and copy the public key to the target hosts in `/{user}/.ssh/authorized_keys`
8. Update the inventory file: `nano inventory.yaml` with your target hosts and credentials
9. Verify connection with command: `ansible db_servers -m ping -i inventory.yaml`
   Expected output:
   ```
   db1 | SUCCESS => {
       "ansible_facts": {
           "discovered_interpreter_python": "/usr/bin/python3"
       },
       "changed": false,
       "ping": "pong"
   }
   ```
10. Deploy the stack: `ansible-playbook site.yaml --vault-id @prompt -i inventory.yaml` (enter your vault password when prompted)

## Verification
- Run on the target hosts `docker ps` to verify that 2 healthy containers are running
- Also you can use command `ansible db_servers -m shell -a "docker ps" -i inventory.yaml` on ansible server to verify
- Check backups on the target hosts: `ls -la /srv/backups/postgres/` after the cron job executes

## Restore from Backup on target host
1. Create a new database:
   ```
   docker exec postgres psql -U {user} -d postgres -c "CREATE DATABASE {new_db};"
   ```
2. Restore the backup:
   ```
   docker exec -i postgres psql -U {user} -d {new_db} < /srv/backups/postgres/{your_dump}.sql
   ```
   Replace `{user}`, `{new_db}`, and `{your_dump}` with your actual values
3. Your restored database will be acceptable for connections with name {new_db}