# bluetti-bluetooth-lib

## I. Bluetooth channel encryption programme description

In order to make Bluetooth communication more secure, Bluetti products implement the encryption for the Bluetooth channel, after the Bluetooth connection is established, the encryption and decryption secret key must be used before the communication can be carried out. Therefore, based on the function of Bluetooth encryption products, it is impossible to use plaintext for Bluetooth communication to obtain Bluetti product data information.

In order to solve the above problem, Bluetti provides Bluetooth channel decryption module. The encryption and decryption module provides APIs for external calls to realise the encryption and decryption functions of the Bluetooth channel.

## II. Using Bluetooth encryption and decryption module

### 1. Installation

Bluetooth encryption and decryption module has two files, when installing, you need to put these two files into the python installation directory (site-packages). 

- `bluetti_crypt.py`: Python package for bluetooth encryption and decryption module.
- `_bluetti_crypt.so`: Bluetooth encryption and decryption module library file to implement encryption and decryption functions.

Note: The file extension .so is for Linux and MacOS, and .py for windows.

# Installation Steps

1. Download the package: [bluetti-bluetooth-lib](https://github.com/bluetti-official/bluetti-bluetooth-lib/releases/)

2. Unpack the zip package

3. Copy the Bluetooth encryption module to the installation directory.

4. Copy and encrypt/decrypt the authorization file to the 'Program' runtime directory and name it bluetti_device_licence.csv.
   
   Note:The Bluetooth decryption module uses the Communication Board SN.  Viewing method: BLUETOOTH APP -> Settings -> About Device -> Communication Board SN

Linux environment can use the command to install, the command is as follows:

Unzip the archive:

`unzip bluetti_crypt.zip -C bluetti_crypt`.

Copy the bluetooth encryption module to the installation directory. 

`cp -rf bluetti_crypt.py _bluetti_crypt.so /home/cloud/.local/lib/python3.12/site-packages/`.

* The installation directory needs to be modified according to your own python directory. *

### 2. Using

The Bluetooth encryption and decryption module provides three APIs for calling, which are:

```c++
/*!
 * @brief Bluetooth encryption channel connection processing
 * @param data, input data, data sent from Bluetti device.
 * @param status Returns the status
            0: Initialisation in progress
            1~2: Authentication in progress
            3: Checking SN legitimacy
            4: Bluetooth connection completed
 * @return Successfully returns encrypted data, failure returns "".
 */
std::string BluettiCrypt::ble_crypt_link_handler(std::string data, int *status);

/*!
 * @brief Encrypt data
 * @param data The data to be encrypted.
 * @return Successfully returns the encrypted data, failure returns "".
 */
std::string BluettiCrypt::encrypt_data(std::string data);

/*!
 * @brief Decrypt data
 * @param data, the data to be decrypted.
 * @return Returns the decrypted data if it succeeds, or return "" if it fails.
 */
std::string BluettiCrypt::decrypt_data(std::string data);
```

- Bluetooth connection: `ble_crypt_link_handler()` is used for the secret key authentication phase after Bluetooth connect, and interactions such as querying data can only be performed after this phase is complete. In the secret key authentication phase, there is no need to handle additional Bluetooth data, direct transmission can be; that is, the Bluetooth encryption and decryption module and Bluetooth side of the direct data interaction. Until `ble_crypt_link_handler` returns `BLE_LINK_STATUS_COMEPLETE` the next step can be carried out.
- Bluetooth data encryption: `encrypt_data()` is the data encryption API, Bluetooth data sent to Bluetti device needs to be encrypted before it can be recognised. Bluetooth data decryption: `decrypt_data()` is the data decryption API.
- Bluetooth data decryption: `decrypt_data()` is the data decryption API. the Bluetooth data from Bluetti device is encrypted data, it needs to be decrypted before parsing the protocol data.

Usage on Home-Assistant and bluetti-mqtt open source, daemo has been provided for reference.