Download Link: https://assignmentchef.com/product/solved-cs3339-lab-2-symmetric-key-cryptography
<br>
In this lab, we will be investigating symmetric key encryption and how you can use it. This will require a system with OpenSSL 1.1.1 (or later). The Kali VM you installed in Lab 1 should have this already installed.




You can check this by opening the terminal and entering the command:




<strong># openssl version </strong>




The first thing we will do is look at the list of encryption algorithms provided by openssl.




<strong># openssl enc -ciphers </strong>

<strong> </strong>

To see the options available to you when encrypting, use the command:




<strong># openssl enc -help </strong>

<strong> </strong>

<strong>Part 1:  </strong>

Now we will use OpenSSL for symmetric key encryption with a password. This mean one key is used for both encryption and decryption. Lets encrypt plaintext.txt, which can be found in the same .zip file as this document. To encrypt with OpenSSL we select one of the ciphers and specify the input and the output files. We will use AES in cipher block chaining mode with a 256 bit key. This will ask for a password, which you need to remember to decrypt. The password is converted to a key of the appropriate length (256 bits).




<strong># openssl aes-256-cbc -e -in plaintext.txt -pbkdf2 -out ciphertext </strong>

<strong> </strong>

Use the following commands to compare the files:

<strong> </strong>

<strong># ls -l plaintext.txt ciphertext </strong>

<strong># file plaintext.txt ciphertext </strong>

<strong># xxd plaintext.txt </strong>

<strong># xxd ciphertext </strong>

<strong> </strong>

<strong>How are they different? Is there anything notable about the ciphertext file? </strong>










To decrypt, we use a similar command, with a <strong>–</strong>​ <strong>d</strong> instead of ​ <strong>–</strong>​ <strong>e: </strong>

<strong> </strong>

<strong># openssl aes-256-cbc -d -in ciphertext -pbkdf2 -out decrypted.txt </strong>

<strong> </strong>

Verify that decrypted.txt is the same as plaintext.txt







<strong>Part 2: </strong>

Now, instead of using a password that is converted to a key, we will be generating the key and initialization vector ourselves. We can do this using the pseudo-random number generator in OpenSSL. The following command will generate 1 hex digits, which is 8 bits.




<strong># openssl rand -hex 1 </strong>

<strong> </strong>

To generate our key, we will generate 256 bits and assign the output to an environmental variable. To generate the initialization vector, we only need to generate 128 bits, because that is the block size of AES.

<strong> </strong>

<strong># key=$(openssl rand -hex 32) </strong>

<strong># echo $key </strong>

<strong># iv=$(openssl rand -hex 16) </strong>

<strong># echo $iv </strong>

<strong># openssl aes-256-cbc -e -K $key -iv $iv -in plaintext.txt -out ciphertext2 </strong>

<strong> </strong>

Again, to decrypt:




<strong># openssl aes-256-cbc -d -K $key -iv $iv -in ciphertext2 -out decrypted2.txt  </strong>

<strong> </strong>

<strong>decrypted2.txt </strong>should look identical to ​ <strong>plaintext.txt</strong>​ , or something went wrong.​







<strong>Why might we want to use a key generated in this manner rather than a password as in part 1? </strong>

<strong> </strong>

<strong> </strong>

<strong> </strong>

<strong> </strong>

<strong> </strong>

<strong> </strong>

<strong> </strong>

<strong> </strong>

<strong> </strong>




<strong>Part 3: </strong>

In this section, we will be experimenting with different modes of operation for block ciphers. We will be encrypting <strong>Tux.bmp</strong>​         that is included in this lab with both Electronic Codebook mode and​           Cipher Block Chaining mode and comparing the differences.




<strong># openssl aes-256-ecb -in Tux.bmp -out cipher_ecb -pbkdf2 </strong>

<strong># openssl aes-256-cbc -in Tux.bmp -out cipher_cbc -pbkdf2 </strong>

<strong> </strong>

Even if we examine these with the <strong>xxd</strong>​            ​ command, we can already clearly start to see how they are different. To further illustrate this, we need to look at the encrypted images. However, we cannot do that as the encryption process has mangled the headers that need to recognize and display these as <strong>.bmp</strong>​      images. So what we are going to do is copy the 54 bit header from​   Tux.bmp and replace the first 54 bits of the encrypted files.

<strong>$ dd if=Tux.bmp of=header bs=1 count=54 </strong>

<strong>$ dd if=cipher_cbc of=cipherbody_cbc bs=1 skip=54 </strong>

<strong>$ dd if=cipher_ecb of=cipherbody_ecb bs=1 skip=54 </strong>

<strong>$ cat header cipherbody_cbc &gt;cbc.bmp </strong>

<strong>$ cat header cipherbody_ecb &gt;ecb.bmp </strong>







Finally, open both cbc.bmp and ecb.bmp with an image viewer.<strong>  </strong>

<strong> </strong>

<strong>How are they different? Why would they produce such different results? </strong>

<strong> </strong>

<strong> </strong>

<strong> </strong>

<strong> </strong>

<strong> </strong>

<strong> </strong>

<strong> </strong>

<strong> </strong>

<strong>Part 4: </strong>

This selection will require a hex editor such as bless, which can be installed with:




<strong># apt install bless </strong>




Here we will be looking at how an error in a ciphertext effect decryption for different modes of encryption. First, let us encrypt <strong>plaintext.txt</strong>​           with AES 256 using all the different modes of​ encryption available in openssl.




<strong># openssl aes-256-cbc -e -in plaintext.txt -pbkdf2 -out ciphertext_cbc # openssl aes-256-ecb -e -in plaintext.txt -pbkdf2 -out ciphertext_ecb </strong>

<strong>… etc </strong>

Next, let us simulate a fault or corruption in each of these ciphertexts. Use a hex editor to change exactly 1 bit in the 30th byte of each ciphertext. Decrypt each corrupted file using the correct password.




<strong># openssl aes-256-cbc -d -in ciphertext_cbc -pbkdf2  -out decrypted_cbc.txt  # openssl aes-256-ecb -d -in ciphertext_ecb -pbkdf2  -out decrypted_ecb.txt  … etc</strong>




<strong>How much information can you recover by decrypting the corrupted file for each of the different encryption modes? Please explain these differences to the best of your ability. </strong>