# chrome_v80_password_grabber
Grab passwords from Chrome > v80 using their new AES encryption with Master Key.\
This script was tested against chrome v80 on a Windows machine.\
Passwords saved on Chrome before v80, seem to remain with the old encryption mechanism - using Windows DPAPI (“CryptProtectData”) and are not re-encrypted\
Only newly saved passwords, after the update was done, will be saved with the new encryption mechanism.

The new machanism is as follows (from https://xenarmor.com/how-to-recover-saved-passwords-google-chrome/ ):\
New Chrome version (v80.0 & higher) uses Master Key based encryption to store your web login passwords.

Here is how it generates the Master Key. First 32-byte random data is generated. Then it is encrypted using Windows DPAPI (“CryptProtectData”) function. To this encrypted key, it inserts signature “DPAPI” in the beginning for identification.

Finally this key is encoded using Base64 and stored in “Local State” file in above “User Data” folder.

Below is the sample entry of encrypted master key.

**"os_crypt":{"encrypted_key":"RFBBUEkBAAAA0Iyd3wEA0RGbegD...opsxEv3TKNqz0gyhAcq+nAq0"},**

Now to store the web login password, Chrome encrypts it using AES-256-GCM algorithm with the above master key and 12-byte random IV data. Finally, it inserts signature “v10” to the encrypted password and stores it in above “Login Data” file.

Below is the structure of new encrypted password,

**struct WebPassword\
{\
	BYTE signature[3] = "v10";\
	BYTE iv[12];\
	BYTE encPassword[...] \
}**

