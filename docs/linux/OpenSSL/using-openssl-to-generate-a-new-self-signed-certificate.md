---
title: 'Using OpenSSL to Generate a New Self-Signed Certificate'
tags: ['linux', 'openssl']
---
# Using OpenSSL to Generate a New Self-Signed Certificate

Unfortunately the OpenSSL utility leaves something to be desired.  This is because many of the options that are required for modern browsers to trust the certificate cannot easily be passed on the command line.  Because of this you need to define configuration files.

## 1. Generate RSA Key
The first step is to generate an RSA private key of a particular size.  In this case we're using 4096 rather than 2048 because browsers will begin deprecating 2048 signed keys.

```shell
openssl genrsa -out example.key 4096
```

## 2. Create a CSR (Certificate Signing Request)
At this point we need to create a CSR which we will complete when generating the actual certificate.  The easiest way to do this is to pass in a configuration file which defines the parameters of the CSR. 

*Note: all modern browsers require a certificate with the "Subject Alternate Names" extension.  These values define the domains and IPs that the certificate is good for.  In this case we need to create the CSR with this in mind.*

Below is a sample configuration file that can be used to create a CSR.  This can be adjusted based on the details of the certificate (Country, State, City, etc.):

```ini
[ req ]
default_bits = 4096
prompt = no
default_md = sha256
req_extensions = req_ext
distinguished_name = dn

[ dn ]
C = US
ST = Montana
L = Kalispell
O = Company
OU = Company Dev
CN = ${DOMAIN}

[ req_ext ]
subjectAltName = @alt_names

[ alt_names ]
DNS.1 = ${DOMAIN}
DNS.2 = www.${DOMAIN}
IP.1 = 127.0.0.1
IP.2 = ::1
```

Then to create the CSR pass in the key you created in Step #1, along with the path to the configuration file (e.g. `csr.conf`):

```shell
openssl req -new -key example.key -out example.csr -config csr.conf
```

## 3. Complete the CSR and Create a Self-Signed Certificate
At this point we need to complete the CSR and in that process complete the creation of our certificate.  We need to define several extensions in the certificate that allow it to be used in modern browsers.  Because of this we will also need a configuration file (e.g. `cert.conf`):


```shell
#!/bin/bash

if [ "$#" -ne 1 ]
then
  echo "Error: No domain name argument provided"
  echo "Usage: Provide a domain name as an argument"
  exit 1
fi

DOMAIN=$1

COUNTRY=US
STATE=Montana
LOCALITY=Kalispell
ORG=Company Inc.
ORG_UNIT=Company Inc. Dev Team

CA=${DOMAIN}-CA
CA_KEY=${CA}.key
CA_CERT=${CA}.crt

KEY=${DOMAIN}.key
CSR=${DOMAIN}.csr
CERT=${DOMAIN}.crt

# Create root CA & private key

openssl req -x509 -sha256 -days 3650 -nodes -newkey rsa:4096 -subj "/CN=${DOMAIN}/C=${COUNTRY}/ST=${STATE}/L=${LOCALITY}" -keyout ${CA_KEY} -out ${CA_CERT} 

# Generate Private key 

openssl genrsa -out ${KEY} 4096

# Create CSR config

cat > csr.conf <<EOF
[ req ]
default_bits = 4096
prompt = no
default_md = sha256
req_extensions = req_ext
distinguished_name = dn

[ dn ]
C = ${COUNTRY}
ST = ${STATE}
L = ${LOCALITY}
O = ${ORG}
OU = ${ORG_UNIT}
CN = ${DOMAIN}

[ req_ext ]
subjectAltName = @alt_names

[ alt_names ]
DNS.1 = ${DOMAIN}
DNS.2 = www.${DOMAIN}
IP.1 = 127.0.0.1
IP.2 = ::1

EOF

# Create CSR request using private key

echo "Creating CSR request..."

openssl req -new -key ${KEY} -out ${CSR} -config csr.conf

# Create a external config file for the certificate

# Additional keyUsages = digitalSignature, nonRepudiation, keyEncipherment, dataEncipherment

cat > cert.conf <<EOF

authorityKeyIdentifier = keyid,issuer
basicConstraints = CA:FALSE
keyUsage = digitalSignature, nonRepudiation, keyEncipherment, dataEncipherment
subjectAltName = @alt_names

[alt_names]
DNS.1 = ${DOMAIN}
DNS.2 = www.${DOMAIN}
IP.1 = 127.0.0.1
IP.2 = ::1

EOF

# Create self-signed certificate

echo "Completing the CSR and generating new certificate..."

openssl x509 -req -in ${DOMAIN}.csr -CA ${CA_CERT} -CAkey ${CA_KEY} -CAcreateserial -out ${CERT} -days 3650 -sha256 -extfile cert.conf

echo "Cleaning up generated configuration files..."

rm csr.conf
rm cert.conf

echo "Root CA Certificate Details:"
openssl x509 -in ${CA_CERT} -text -noout

echo "Certificate Details:"
openssl x509 -in ${CERT} -text -noout

echo "Finished certificate generation process:"
echo "Root CA Private Key=${CA_KEY}"
echo "Root CA Certificate=${CA_CERT}"
echo "CSR=${CSR}"
echo "Private Key=${KEY}"
echo "Certificate=${CERT}"

# Make sub-directory for the certificate
mkdir -p /usr/share/ca-certificates/${DOMAIN}

# Copy the CA and regular certificate
sudo cp -v ${CERT} /usr/share/ca-certificates/${DOMAIN}/
sudo cp -v ${CA_CERT} /usr/share/ca-certificates/${DOMAIN}/

# Copy the CA cerificate as a trust source anchor
sudo cp -v ${CA_CERT} /usr/share/ca-certificates/trust-source/anchors/

# Rebuild trust source anchors
sudo update-ca-trust extract

```

## Arch Linux -- Installing the Certificate OS-Wide (makes it available to wget, curl, dotnet, etc.)

Copy the certificate to the following locations:

```shell
# Make sub-directory for the certificate
mkdir -p /usr/share/ca-certificates/example

# Copy the certificate
sudo cp example.crt.pem /usr/share/ca-certificates/example/.

# Copy the cerificate as a trust source anchor
sudo cp example.crt.pem /usr/share/ca-certificates/trust-source/anchors/.

# Rebuild trust source anchors
sudo update-ca-trust extract

# Copy the certificate for certain command line utilities
sudo cp example.crt.pem /etc/ssl/certs/.
```