# Encryption and Decryption Project

In this project, we simulate that the ceu.edu website generates private and public keys and saves them in 2 different files named **ceu_key(private key)** and **ceu_key.pub(public key)**. It also stores the commands that will generate this key pair in a file named **key_generation.sh**. A visitor visits this website and ceu.edu shares its public key (ceu_key.pub) with this visitor. In this way, the visitor can encode its own message with this public key and after sending the encrypted message to Ceu.edu, Ceu.edu will be able to decode this message encrypted by the visitor with its own private key (ceu_key).

To do this, first of all, the following commands to generate this key pair are written into the key_generation.sh file.

```
# Setting the filenames of public and private key pair 
private_key_file="ceu_key"
public_key_file="ceu_key.pub"

# Generating an RSA key pair
ssh-keygen -t rsa -b 2048 -f "$private_key_file" -N ""

# Displaying a message to the screen showing that the key generation completed
echo "Key generation completed. Private key saved as $private_key_file and public key saved as $public_key_file."
```
	    
And it looks like the screenshot below.

<img width="688" alt="Screenshot 2023-11-21 at 16 57 20" src="https://github.com/ab-dum/Encryption-Decryption/assets/141356115/4f1888db-7eee-4048-bf76-80c44b987d8f">

 

To make key_generation.sh file executable we should write the below commands to the terminal. 

```chmod +x key_generation.sh```

After that, for generating ceu_key and ceu_key.pub key pair, we should run the script below in the terminal.

`./key_generation.sh`

When we run this script, a private key named ceu_key and a public key named ceu_key.pub are generated in the key_generation.sh directory.

Then, the ceu_key.pub file is sent to the visitor by e-mail so that the visitor could encrypt a message with this public key.

Visitor uses the following Python code block to encrypt and save the message it produces using the public key sent to it by ceu.edu.


```python
# Importing the libraries to encrypt the messages by using public key 
import rsa
from pathlib import Path
from Crypto.Cipher import PKCS1_OAEP
from Crypto.PublicKey import RSA
from datetime import datetime

def encrypt_and_save_message(message, public_key_path):
    # Reading the public key(ceu_key.pub)
    with open(public_key_path, 'rb') as public_key_file:
        public_key = RSA.import_key(public_key_file.read())

    # Creating a PKCS1_OAEP cipher object
    public_key_cipher = PKCS1_OAEP.new(public_key)

    # Encrypting the message by using public_key_cipher
    encrypted_message = public_key_cipher.encrypt(message.encode('utf-8'))

    # Generating a unique shell script filename for each encrypted message based on the current timestamp and save it to desktop
    timestamp = datetime.now().strftime("%Y%m%d%H%M%S")
    output_directory = '/Users/...'
    output_file_path = f'{output_directory}encrypted_message_{timestamp}.sh'

    # Writing the encrypted message to the shell script file
    with open(output_file_path, 'wb') as output_file:
        output_file.write(encrypted_message)
       
    return output_file_path

# Defining the message to be encrypted by the visitor
message = "Ve Galatasaray Şampiyonlar Ligi Şampiyonu"

# Pathing to the public key file
public_key_path = '/Users/...'

# Encrypting and saving the message by using encrypt_and_save_message function.
output_file_path = encrypt_and_save_message(message, public_key_path)

print(f"Encrypting Message saved to {output_file_path}")
```


Then the visitor sends the encrypted message below to ceu.edu via e-mail.

<img width="687" alt="Screenshot 2023-11-22 at 01 32 44" src="https://github.com/ab-dum/Encryption-Decryption/assets/141356115/6ae9e04d-2b42-44ae-b9f0-2b7571fb40de">

 
Ceu.edu uses the following python function and its private key to decode this encrypted message.

```python
# Importing the libraries to decode the encrypted messages by using private key
import rsa
from pathlib import Path
from Crypto.Cipher import PKCS1_OAEP
from Crypto.PublicKey import RSA
from datetime import datetime
def decrypt_message(encrypting _message_path, private_key_path):
    # Reading the encrypted message as a string
    with open(encrypted _message_path, 'rb') as encrypted_message_file:
        encrypted_message = encrypted_message_file.read()

    # Reading the private key from local
    with open(private_key_path, 'rb') as private_key_file:
        private_key = RSA.import_key(private_key_file.read())

    # Creating a PKCS1_OAEP cipher object with the private key
    private_key_cipher = PKCS1_OAEP.new(private_key)

    # Decoding the message using cipher
    decrypted_message = private_key_cipher.decrypt(encrypted_message)

    return decrypted_message.decode('utf-8')

# Pathing to the private key file
private_key_path = '/Users/...'

# Pathing to the file containing the encrypted message
encrypted_message_path = '/Users/...'

# Decrypting the message using the private key
decrypted_message = decrypt_message(encrypted_message_path, private_key_path)

print("Decrypted Message:", decrypted_message)
```


And thus, the **'Ve Galatasaray Şampiyonlar Ligi Şampiyonu'** message encrypted by the visitor is displayed on the screen.
