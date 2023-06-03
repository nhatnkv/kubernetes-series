# Creating a CA (Certificate Authority)

1. Generate a private key for the CA:

  `openssl genrsa -out ca.key 4096`

2. Create a self-signed CA certificate using the private key:

  `openssl req -new -x509 -sha256 -key ca.key -out ca.crt -days 365`


## Generating Client Certificates

1. Generate a private key for the client:

  `openssl genrsa -out client.key 2048`

2. Create a Certificate Signing Request (CSR) for the client:

  `openssl req -new -key client.key -out client.csr`

  Enter the required details when prompted, ensuring the Common Name (CN) matches the client's identity.

3. Sign the client CSR using the CA certificate and private key:

  `openssl x509 -req -in client.csr -CA ca.crt -CAkey ca.key -CAcreateserial -out client.crt -days 365`

## Using Client Certificates for Authentication in Kubernetes

1. Create a Kubernetes user in the `kubeconfig` file:

  `kubectl config set-credentials <username> --client-certificate=client.crt --client-key=client.key --embed-certs=true`

  Replace `<username>` with the desired username for the client.

2. Set the current context to use the newly created user:

  `kubectl config set-context <context-name> --cluster=<cluster-name> --user=<username>`

Replace `<context-name>` with a meaningful name for the context and `<cluster-name>` with the name of the cluster defined in the `kubeconfig` file.

3. Use the configured user to interact with the Kubernetes cluster:
  
  `kubectl --context=<context-name> get pods`

  Replace `<context-name>` with the context name used in the previous step.

