Ansible playbook to install and setup the Java client for Signal messenger from [AsamK](https://github.com/AsamK/signal-cli/) which allows sending signal messages from the command line.


## Preconditions
- ssh to target host properly configured
- Ansible > 1.5
- Java on the target host must support unlimited strength crypto
- specify IP address of the target host within `./inventory.yml`

## Unlimited strength crypto
- For **Zuul JRE** installation of corresponding policies can be done by the playbook like so:
```
$> ansible-playbook site.yml --inventory inventory.yml --user <remoteuser> --tags "zulucrypto"
```
Download URL as well as JRE home dir on the host is set to default values in `./roles/zulu_crypto_extension/defaults/main.yml`. If other values are needed these can be passed as part of the `--extra-vars` argument.


- For **Oracle JRE**, the required policies can be downloaded from
the Oracle website (e.g. [here](https://www.oracle.com/technetwork/java/javase/downloads/jce8-download-2133166.html) for Java 8) and installed manually.

## Installation  
- Source: https://github.com/AsamK/signal-cli/
- Installation of the signal client
```
$> ansible-playbook site.yml --inventory inventory.yml --user <remoteuser> --tags "setupsignal"
```
This will download the signal client from Github and install it on the target host. Version of the client, installation directory and owner are set to default values in `./roles/signal_client/defaults/main.yml`.

## Registration
- Before a number can be used to send messages with signal it has to be registered which can be done like so:
```
$> ansible-playbook site.yml --inventory inventory.yml --user <remoteuser> --tags "registernumber" --extra-vars="phone_number=<phone_no_with_country_code>"
```  
- If the registration succeeded you will be called on the registered phone number to get a verification code
- this code is needed to complete the registration process by executing:
```
$> ansible-playbook site.yml --inventory inventory.yml --user <remoteuser> --tags "verifynumber" --extra-vars="phone_number=<phone_no_with_country_code> verification_code=<verification_code>"
```  

## Testing
- Now it should be possible to send messages from the registered number to other signal accounts
```
$> signal-cli --config /var/lib/signal-cli/ -u "<registered_number>" send -m "This is a message from neverland" "<number_of_receiver>"
```  
- Details about usage of the client can be found at [Github](https://github.com/AsamK/signal-cli/) 

