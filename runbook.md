# DB Stack Runbook

## Deployment
1. Install ansible on host and install docker on client
2. Copy archive db_stack.tar.gz on your ansible server
3. Unpackage archive db_stack.tar.gz with command `tar -zcf db_stack.tar.gz`
4. Change password with command `ansible-vault edit db_stack/vars/vault.yaml` vault password is `12345678`
5. Change defaulth vault password if it needed with command `ansible-vault rekey db_stack/vars/vault.yaml`
4. Run `ansible-playbook site.yml --vault-id @prompt` (enter your vault password).

## Verification
- Check Postgres: `docker ps` should show healthy containers.
- Test PgBouncer: `psql -h 127.0.0.1 -p 6432 -U myuser -d mydb`.
- Backups: Check /srv/backups/postgres after cron run.

## Restore from Backup
1. Stop stack: `docker-compose -f /etc/docker-compose/db_stack.yml down`.
2. Restore: `psql -h 127.0.0.1 -p 6432 -U myuser -d mydb`.