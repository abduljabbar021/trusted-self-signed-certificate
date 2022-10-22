# Trusted Self Signed Certificate with CA
Become A Certificate Authority (CA) like LetsEncrypt &amp; Comodo and create your Trusted Self Signed Certificate for "Local Testing"


## Create Root CA

    openssl genrsa -des3 -out rootCA.key 4096

-desc3 will ask you for a key which your certificates can use to verify themselves with your CA in Browsers. You can omit this key config.
rootCA.key is the key name for your CA. You can call it anything. Let's call it as rootCA



## Create and Self Sign the Root Certificate

    openssl req -x509 -new -nodes -key rootCA.key -sha256 -days 1024 -out rootCA.crt

Again, we call our it as rootCA. 
Here we used our rootCA.key to create the root certificate that needs to be distributed to all the Computers/Browsers that have to trust us.
Once created, you can import this Certificate in Computers/Browsers among Trusted Certificate Authorities to verify certificates signed by this CA.

For Example in Chrome:
Chrome -> Settings -> Search Certificates -> Security -> Manage Certificates -> Authorities -> Import


## Create Self-Signed Certificate for Your Domain

    NAME=mydomain.example       //<--mydomain.example is the domain name you want to create Certificate for. It can be subdomain.mydomain.com

    openssl genrsa -out $NAME.key 2048

    openssl req -new -key $NAME.key -out $NAME.csr   //<--This will ask for extra information. You can skip Challenge Password & Optional Company Name

    >$NAME.ext cat <<-EOF
    authorityKeyIdentifier=keyid,issuer
    basicConstraints=CA:FALSE
    keyUsage = digitalSignature, nonRepudiation, keyEncipherment, dataEncipherment
    subjectAltName = @alt_names
    [alt_names]
    DNS.1 = $NAME
    EOF

    openssl x509 -req -in $NAME.csr -CA PathToCA/rootCA.crt -CAkey PathToCA/rootCA.key -CAcreateserial \ -out $NAME.crt -days 825 -sha256 -extfile $NAME.ext
    //Change your PathToCA/rootCA.crt in above line. It should point to CA Certificate.

Above command will ask for the PassKey you provided while creating your CA Certificate. This Certificate along with its PassPhrase will be verified by the CA you just recently imported in your Computer/Browser CA Authorities
        
        
        
Respect also goes to followings, I added some more in-depth details :)

https://stackoverflow.com/questions/7580508/getting-chrome-to-accept-self-signed-localhost-certificate/60516812#60516812
https://gist.github.com/fntlnz/cf14feb5a46b2eda428e000157447309
