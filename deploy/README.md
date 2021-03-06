# NEO-Crisis Provisioner

This is a small Ansible script to setup environment needed for our awesome NEO-crisis because the world is ending and we have no time to be shelling into servers to do all the hard work manually :)

This provisioner expects the target machine to be a Ubuntu18.04 but does not validates that(feel free to try on different flavors/versions). Stack:

- Python3.6 + virtualenv
- Postgres
- NGINX

## Development

The provided `Vagrantfile` will run the Ansible provisioner when the machine is created having the full project setup.

Requirements:
- Vagrant 2.0.2
- VirtualBox 5.2.10

TLDL:
```
vagrant up
vagrant ssh
cd /vagrant
virtualenv -p python3.6 dev_venv
source dev_venv/bin/activate
pip install -r api/requirements.txt
python api/api.py
```
Open browser at `http://localhost:5000` to see you development server running.

The `/vagrant` folder is synced with the host machine so you can edit files on your computer and run on the VM from there.

The `/var/www/neocrisis/` is the "production" code pulled from Github during deployment and served by the web server. On your VM this can be seen at `http://localhost:8000`

## Deployment

With Ansible 2.5.1 installed on your computer, update provisioner/inventory to match your username/credentials(if you want to deploy it to a diff server, just change the IP to any other Ubuntu18 server). Run `sudo ansible-playbook provisioner/playbook.yml -i provisioner/inventory`. This will setup users create DB, install all requirements, etc but it does NOT run DB migrations(for now). So if it is your 1st deployment, ssh into the server and run:

```
cd /var/www/neocrisis/sql
sudo -u pynyc psql nc < model.sql
```

## Helpful commands

```
sudo systemctl daemon-reload
sudo systemctl restart neocrisis_gunicorn
sudo nginx -t
sudo systemctl restart nginx
sudo /var/www/neocrisis/venv/bin/python
```

### To be continued...

- Add tags to roles and triggers to restart NGINX, Systemd and neocrisis_gunicorn properly
- Add provisioning task that runs models.sql script
- add deployment script that just do service related tasks
