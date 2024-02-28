# install the k3d
brew install k3d

# create a cluster named mycluster
k3d cluster create mycluster

# Verify the cluster status
kubectl get nodes

# create config files: 
- deployment.yaml
- service.yaml
- ingress.yaml
- hpa.yaml

# apply the files
kubectl apply -f deployment.yaml 
kubectl apply -f service.yaml 
kubectl apply -f ingress.yaml 
kubectl apply -f hpa.yaml 

# get the services
kubectl get services

# forward  traffic to port  80
kubectl port-forward <podsname> 8080:80

# create the selfsigned-certificate

openssl genpkey -algorithm RSA -out private.key 
openssl req -new -key private.key -out request.csr
openssl x509 -req -in request.csr -signkey private.key -out certificate.crt
kubectl create secret tls hello-world-tls-secret --cert=certificate.crt --key=private.key


# Scaling
kubectl scale deployment hello-world --replicas=5
# Verify Scaling
kubectl get pods -o wide


# Network hardening

kubectl get networkpolicies  # check networkpolicies
kubectl get ingress          # check ingress


## keynotes

1. Services are assumed to have virtual IPs only routable within the cluster network.
2. Ingress exposes HTTP and HTTPS routes from outside the cluster to services within the cluster. Traffic routing is controlled by rules defined on the Ingress resource.
3. An Ingress may be configured to give Services externally-reachable URLs, load balance traffic, terminate SSL / TLS, and offer name-based virtual hosting. An Ingress controller is responsible for fulfilling the Ingress, usually with a load balancer
4. Ingress frequently uses annotations to configure some options depending on the Ingress controller. The Ingress spec has all the information needed to configure a load balancer or proxy server.
5. An Ingress with no rules sends all traffic to a single default backend. If defaultBackend is not set, the handling of requests that do not match any of the rules will be up to the ingress controller 
6. You can secure an Ingress by specifying a Secret that contains a TLS private key and certificate. The Ingress resource only supports a single TLS port, 443, and assumes TLS termination at the ingress point (traffic to the Service and its Pods is in plaintext). If the TLS configuration section in an Ingress specifies different hosts, they are multiplexed on the same port according to the hostname specified through the SNI TLS extension (provided the Ingress controller supports SNI). The TLS secret must contain keys named tls.crt and tls.key that contain the certificate and private key to use for TLS 
7. Keep in mind that TLS will not work on the default rule because the certificates would have to be issued for all the possible sub-domains. Therefore, hosts in the tls section need to explicitly match the host in the rules section.


# Hardening

1. TLS for ingress: Enable TLS to encrypt traffic between clients and the Ingress controller. This is crucial for securing sensitive data during transmission.
2. Network Policies: Define network policies to control the traffic between pods. This helps restrict communication and reduces the attack surface.
3. RBAC: Restrict access to the Ingress controller by implementing RBAC rules.Adjust the role and role-binding definitions based on the specific requirements and service account used by your Ingress controller.
4. Update Ingress controller Image Regularly: Keep your Ingress controller image up-to-date to ensure you have the latest security patches and improvements.
5. WAF: Use annotations or configurations to enable a Web Application Firewall for your Ingress controller to protect against common web application attacks.