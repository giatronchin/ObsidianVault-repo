*Guida per installazione e configurazione server Vault*

```bash
sudo apt-get update
sudo apt-get install -y software-properties-common

# Add Hashicorp repository
wget -O- https://apt.releases.hashicorp.com/gpg | sudo gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg
echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list

sudo apt-get update
sudo apt install vault -y

# Test correct installation
vault --version
```

*Creare cartella per i dati che Vault andrà a memorizzare su disco in modo cifrato, minimizzando la decrittazione dei segreti e la loro permanenza in memoria*
```bash
sudo mkdir -p /var/lib/vault
sudo chown -R vault:vault /var/lib/vault
```

 *Creare cartella per i dati di configurazione di Vault* (**vault.hcl**)
 ```bash
sudo mkdir -p /etc/vault
sudo nano /etc/vault/vault.hcl
```

```
storage "file" {
  path = "/var/lib/vault/data" --> Where secrets are going to be stored
}

listener "tcp" {
  address = "100.85.32.121:8200" --> Socket the Vault server will listen to
  tls_disable = 1 --> If you need secure connection to the server
}

api_addr="http://100.85.32.121:8200" --> Socket the Vault server will listen to API calls

ui=true --> Render web ui for browser experience
```
*Impostare i permessi corretti per il file di configurazione*
```bash
sudo chown -R vault:vault /etc/vault
sudo chmod -R 750 /etc/vault
```

----
Il server Vault necessità di essere avviato ad ogni boot del sistema. Un service può essere comodo per l'avvio automatico del Vault.

`/etc/systemd/system/vault.service`

```
[Unit]
Description=Vault
Documentation=https://www.vaultproject.io/docs/
Requires=network-online.target
After=network-online.target

[Service]
User=vault
Group=vault
ExecStart=/usr/local/bin/vault server --config=/etc/vault/vault.hcl
ExecReload=/bin/kill --signal HUP $MAINPID
Restart=on-failure
RestartSec=30s
StartLimitIntervalSec=300
StartLimitBurst=5
CapabilityBoundingSet=CAP_SYSLOG CAP_IPC_LOCK
LimitMEMLOCK=infinity
Environment=VAULT_ADDR=http://100.85.32.121:8200

[Install]
WantedBy=multi-user.target
```

```bash
sudo systemctl daemon-reload
sudo systemctl enable vault
sudo systemctl start vault
```

Al primo avvio il Vault deve essere inizializzato, producendo 5 chiavi di sblocco e un token di autenticazione Root.
```bash
user@hostname:~$ vault operator init

Unseal Key 1: abcdef1234567890abcdef1234567890abcdef1234567890abcdef1234567890
Unseal Key 2: 1234567890abcdef1234567890abcdef1234567890abcdef1234567890abcdef
Unseal Key 3: 7890abcdef1234567890abcdef1234567890abcdef1234567890abcdef123456
Unseal Key 4: 4567890abcdef1234567890abcdef1234567890abcdef1234567890abcdef123
Unseal Key 5: 90abcdef1234567890abcdef1234567890abcdef1234567890abcdef12345678

Initial Root Token: s.abcdef1234567890abcdef1234567890

Vault initialized with 5 key shares and a key threshold of 3. Please securely
distribute the key shares printed above. When the Vault is re-sealed,
restarted, or stopped, you must supply at least 3 of these keys to unseal it
before it can start servicing requests.

Vault does not store the generated root key. Without at least 3 keys to
reconstruct the root key, Vault will remain permanently sealed!

It is possible to generate new unseal keys, provided you have a quorum of
existing unseal keys shares. See "vault operator rekey" for more information.
```

Utilizzo:
- Unseal Key - sono 5 di default. 3 devono essere fornite ogni volta che il Vault viene bloccato (sealed) per esempio al riavvio dell'host, o per un blocco manuale.
- Token - serve per l'autenticazione verso il Vault una volta che questo è stato sbloccato (unsealed)

Il processo è iterativo e interattivo, in quanto devono essere eseguite 3 istruzioni - 3 chiavi differenti - per lo sblocco, prima di poter operare con il Vault.
```bash
vault operator unseal <chiave-di-sblocco-1>
vault operator unseal <chiave-di-sblocco-2>
vault operator unseal <chiave-di-sblocco-3>
vault login <token>
```

Per verificare lo status del Vault:
```bash
$ vault status
Key                Value
---                -----
Seal Type          shamir
Initialized        true
Sealed             false --> Vault is unlocked!
Total Shares       5
Threshold          3
Version            1.18.3
Build Date         2024-12-16T14:00:53Z
Storage Type       file
Cluster Name       vault-cluster-885f05e4
Cluster ID         2d4767be-3b3d-ebd7-ef35-423d921c9df7
HA Enabled         false
```

Bonus: Free version of Vault does integrate with HSM for autounsealing of the Vault. Therefore a couple of additional Service can come handy.

`/etc/systemd/system/unseal-vault.service`

```
[Unit]
Description=Unseal Vault
After=vault.service

[Service]
Type=oneshot
ExecStart=/usr/local/bin/unseal_vault.sh
```

`/usr/local/bin/unseal_vault.sh`

```bash
#!/bin/bash

# Log di debug
echo "Avvio dello script di sblocco..." >> /var/log/vault_unseal.log
echo "Variabili d'ambiente:" >> /var/log/vault_unseal.log
env >> /var/log/vault_unseal.log

# Carica le variabili d'ambiente dal file .env (se necessario)
if [ -f /etc/vault/.env ]; then
  echo "File .env trovato. Caricamento variabili..." >> /var/log/vault_unseal.log
  export $(grep -v '^#' /etc/vault/.env | xargs)
  echo "Variabili d'ambiente caricate." >> /var/log/vault_unseal.log
else
  echo "File .env non trovato." >> /var/log/vault_unseal.log
fi

# Indirizzo di Vault
export VAULT_ADDR="http://100.85.32.121:8200"
echo "VAULT_ADDR impostato a $VAULT_ADDR" >> /var/log/vault_unseal.log

# Attendi che Vault sia avviato
echo "Attesa di 10 secondi per l'avvio di Vault..." >> /var/log/vault_unseal.log
sleep 10
echo "Attesa completata." >> /var/log/vault_unseal.log

# Sblocca Vault
echo "Sblocco in corso..." >> /var/log/vault_unseal.log
vault operator unseal "$UNSEAL_KEY_1" >> /var/log/vault_unseal.log 2>&1
vault operator unseal "$UNSEAL_KEY_2" >> /var/log/vault_unseal.log 2>&1
vault operator unseal "$UNSEAL_KEY_3" >> /var/log/vault_unseal.log 2>&1
echo "Sblocco completato." >> /var/log/vault_unseal.log
```

`/etc/systemd/system/unseal-vault.timer`

```
[Unit]
Description=Run unseal-vault.service 1 minute after boot and every 1 minutes

[Timer]
OnBootSec=1m
OnUnitActiveSec=1m

[Install]
WantedBy=timers.target
```

1. Service `unseal-vault.service` is executed after `vault.service` is completed (`After=vault.service`)
	- it recalls script `unseal_vault.sh` which unlock the vault using 3 keys hardcoded in an .env file properly secured
2. Time make the service unseal-vault run 1 minute after boot and every 1 minutes