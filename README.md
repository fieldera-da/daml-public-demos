# Daml Public Demos of Daml Shell and Daml Export by Andrew Fielder

Purpose is the highlight the usefulness of Daml Shell and Daml Export for investigation of issues resulting from complex application state.

## Demo Steps

### Deploy ledger and initailise with contracts

Checkout the demo:

```
git clone https://github.com/wallacekelly-da/daml-public-demos.git --branch pqs-simple-docker-compose --single-branch pqs-simple-docker-compose
```

Get the required images:

```
docker login digitalasset-docker.jfrog.io
docker compose pull
```

Stop local postgres if it is running [It is on Andrew's machine]
```
sudo systemctl stop postgresql
```

Run the demo:

```
daml build
--docker compose up participant1 --detach
docker compose up scripts
```

See that the contracts are created:
```
docker compose up pqs1_scribe --detach
docker compose run --rm daml_shell
```

### Export contracts

Run Daml export:
```
daml ledger export script --host localhost --port 5003 --output export --sdk-version 2.8.3 --all-parties
```

### Bring up clean ledger and import pre-existing contracts

Restart system:
```
docker compose down
docker compose up participant1 --detach
```

Restart ledger as ledger 
```
docker compose up participant1 --detach
```

Allocate parties 
```
daml ledger allocate-parties --host localhost --port 5003 alice
daml ledger allocate-parties --host localhost --port 5003 bob
```

Update partyIds in second colomn of ./export/args.json. Only the participant id will be different.
```
nano -w ./export/args.json
```

Load contracts using export daml:
```
daml build --project-root ./export/
daml script --ledger-host localhost --ledger-port 5003 --dar ./export/.daml/dist/export-1.0.0.dar --script-name Export:export --input-file ./export/args.json
```

Prove contracts were loaded:
```
docker compose up pqs1_scribe --detach
docker compose run --rm daml_shell
```

### Cleanup

Shutdown system:
```
docker compose down
```

### Navigator alternative [deprecated]
daml navigator server localhost 5003 --feature-user-management false