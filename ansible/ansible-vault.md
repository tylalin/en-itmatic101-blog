---
cover: >-
  https://images.unsplash.com/photo-1609358905581-e5381612486e?crop=entropy&cs=srgb&fm=jpg&ixid=M3wxOTcwMjR8MHwxfHNlYXJjaHwxfHx2YXVsdHxlbnwwfHx8fDE2OTkxNzc3NDd8MA&ixlib=rb-4.0.3&q=85
coverY: 0
---

# Ansible Vault

### Setting Ansible vault default editor

```bash
$ nano ~/.bashrc

# add the following to the end of .bashrc file then save it.
export EDITOR=nano

# source the modified .bashrc file as below
$ . ~/.bashrc

# verify the new environmental variable
$ echo $EDITOR
```

### Creating new encrypted files

```bash
# ansible-vault command to create new vault.yml file
$ ansible-vault create vault.yml

# Output as below
# Input the new vault password to encrypt it
New Vault password: 
Confirm New Vault password:

# New vault.yml file will be open in nano
# Input some text encrypted

# Then check the contect of vault.yml as below
$ cat vault.yml

# Output
$ANSIBLE_VAULT;1.1;AES256
65316332393532313030636134643235316439336133363531303838376235376635373430336333
3963353630373161356638376361646338353763363434360a363138376163666265336433633664
30336233323664306434626363643731626536643833336638356661396364313666366231616261
3764656365313263620a383666383233626665376364323062393462373266663066366536306163
31643731343666353761633563633634326139396230313734333034653238303166
```

### Encrypting the existing file&#x20;

```bash
# Create a dummy text file
$ echo 'unencrypted stuff' > encrypt_me.txt

# Encrypt the text file with the ansible vault command as below
$ ansible-vault encrypt encrypt_me.txt

# Output
New Vault password: 
Confirm New Vault password:
Encryption successful

# Verify the encrypted file
$ cat encrypt_me.txt

# Output
$ANSIBLE_VAULT;1.1;AES256
66633936653834616130346436353865303665396430383430353366616263323161393639393136
3737316539353434666438373035653132383434303338640a396635313062386464306132313834
34313336313338623537333332356231386438666565616537616538653465333431306638643961
3636663633363562320a613661313966376361396336383864656632376134353039663662666437
39393639343966363565636161316339643033393132626639303332373339376664
```

### Viewing encrypted files

```bash
# Use the following command to view the encrypted file
$ ansible-vault view vault.yml

# Output
Vault password:
Secret information
```

### Editing encrypted files

```bash
# Use the following command to edit the encrypted file
$ ansible-vault edit vault.yml

# Output
Vault password:

# It will open vault.yml file in nano
```

### Manually decrypting encrypted files

```bash
# Use the command below to decrypt encrypted file
$ ansible-vault decrypt vault.yml

# Output
Vault password:
Decryption successful

# It will decrypt the vault.yml file into plain text now 
```

&#x20;**Note:** Because of the increased likelihood of accidentally committing sensitive data to your project repository, the `ansible-vault decrypt` command is only suggested for when you wish to remove encryption from a file permanently. If you need to view or edit a vault encrypted file, it is usually better to use the `ansible-vault view` or `ansible-vault edit` commands, respectively.

### Changing the password of encrypted files

```bash
$ ansible-vault rekey encrypt_me.txt

# Output 
Vault password:
New Vault password: 
Confirm New Vault password: 
Rekey successful
```

### Running Ansible with Vault-Encrypted Files <a href="#running-ansible-with-vault-encrypted-files" id="running-ansible-with-vault-encrypted-files"></a>

```bash
# Use --ask-vault-pass option to get interactive prompt for vault password
$ ansible --ask-vault-pass -bK -m copy -a 'src=secret_key dest=/tmp/secret_key mode=0600 owner=root group=root' localhost

# Use --vault-password-file=.vault_pass for hidden password file
# Ensure that .vault_pass file is added to .gitignore
$ ansible --vault-password-file=.vault_pass -bK -m copy -a 'src=secret_key dest=/tmp/secret_key mode=0600 owner=root group=root' localhost
```

#### Reading the Password File Automatically <a href="#reading-the-password-file-automatically" id="reading-the-password-file-automatically"></a>

```bash
# Method 1
# Add the variable ANSIBLE_VAULT_PASSWORD_FILE to .bashrc 
export ANSIBLE_VAULT_PASSWORD_FILE=./.vault_pass

# Run ansible again without --vault-password-file
$ ansible -bK -m copy -a 'src=secret_key dest=/tmp/secret_key mode=0600 owner=root group=root' localhost

# Method 2
# Add vault_password_file to ansible.cfg
[defaults]
. . .
vault_password_file = ./.vault_pass

# Run ansible again without --vault-password-file
$ ansible -bK -m copy -a 'src=secret_key dest=/tmp/secret_key mode=0600 owner=root group=root' localhost
```

&#x20;Now, when you run commands that require decryption, you will no longer be prompted for the vault password. As a bonus, `ansible-vault` will not only use the password in the file to decrypt any files, but it will apply the password when creating new files with `ansible-vault create` and `ansible-vault encrypt`.
