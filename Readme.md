## Prerequisites
- Vagrant hostmanager plugin

## Installation Guide
- `vagrant up` to begin
- after all machines have finished provisioning you can feed index with `elasticdump`. Assuming that the file with your index dump is called `index.json` ssh to `es-client-01` with `vagrant ssh es-client-01` and run 
`elasticdump --bulk=true --input=/vagrant/index.json --output=http://localhost:9200/` 
to start feeding the index
- you can go to `http://localhost:9201/_plugin/head/` to check cluster status (it won't show anything until a first master node have finished provisioning)

## TODO
- move provisioning to chef
