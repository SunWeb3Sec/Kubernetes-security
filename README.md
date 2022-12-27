# Kubernetes pentesting, hardening and hunting tools

Share my k8s research materials that I did two years ago.

[Kubernetes pentesting](https://github.com/SunWeb3Sec/Kubernetes-security/edit/main/README.md#kubernetes-pentesting)

[Kubernetes hardening](https://github.com/SunWeb3Sec/Kubernetes-security/edit/main/README.md#kubernetes-hardening)

[Kubernetes hunting tools](https://github.com/SunWeb3Sec/Kubernetes-security/edit/main/README.md#kubernetes-hunting-tools)

## Kubernetes pentesting

### Docker attacks
* Misconfiguration
  * Docker Remote API unauthorized access port 2375 (HTTP) and 2376 (HTTPS) /containers/json
  * Docker.sock is mounted inside the container
  * Docker compose secret expose
  * DIND(docker-in-docker) exploitation
* Docker high-risk startup parameters
  * Privileged privileged mode
  * Mount sensitive directories
  * Safety issues related to startup parameters
* Escape by Docker software flaw
  * Shocker attack, VMM-container breakout 
  * runC container escape vulnerability (CVE-2019-5736)
  * Docker cp command (CVE-2019-14271)
  * Abuse cgroup (release_agent)
  * rkt enter  (CVE-2019-10144, CVE-2019-10145 and CVE-2019-10146)
* Escape by Kernel vulnerability
  * Dirty COW - (CVE-2016-5195) - Docker Container Escape
* Backdoor container
### Kubernetes attacks
* Recon / Misconfiguration
  * Service unauthorized access / Expose 
    * ETCD port 2379
    * Maintains cluster state and secrets
    * No authentication by default
    * No encryption at rest by default
    * REST & gRPC APIs
  * Kubelet API port 10250/10255 (Misconfigured kubelets non-auth)
  * API server port 443/6443/8443/8080
  * Kube porxy port 10256
  * Calico port 9099
  * Weave 6782-4
  * NodePort expose
  * Cluster network recon
  * Shodan/ZoomEye/Censys
    * kubernetesDashboard
    * kubernetes
    * k8s
    * kubernetes master
    * openshift
    * swarm
    * product:etcd
    * k8s.io
    * apiserver
    * k8s_node/k8s-cluster-etcd/kubeadm-master/kubemaster-etcd
  * Bypass namespace restriction
    * The namespaces within the cluster doesn't have any network security restrictions by default.  By default, all pods in a Kubernetes cluster can communicate freely with each other without any issues
    * Docker sock
  * Container escape / replace host binary / reverse shell
    * docker.sock expose
    * Hostpath mount / chroot /host/ bash
  * Privilege escalation
    * Insecure deployment file 
    * Insecure pod security policy (AllowPrivilegeEscalation, MustRunAsNonRoot and privileged) Bypass the PSP to deploy a Pod
    Bad Pod #1: Everything allowed
      * Bad Pod #2: Privileged and hostPid
      * Bad Pod #3: Privileged only
      * Bad Pod #4: hostPath only
      * Bad Pod #5: hostPid only
      * Bad Pod #6: hostNetwork only
      * Bad Pod #7: hostIPC only
      * Bad Pod #8: Nothing allowed
    * Create pod into kube-system with automountServiceAccountToken: true
    * Create malicious Admission controllers
    * Shell Escape Sequences
  * Git expose 
  * Secret leakage
    * Config
    * Secret
    * SSH key
    * Environment information
  * DoS the memory/cpu resources
    * No applied limit ranges for the containers
* Initial Access
  * Using cloud credentials, instance metadata
    * SSRF over web vulnerability
    * In Container
  * Gain access private registry
  * Vulnerable application
    * Backdooring CI/CD
    * Discovering Routes and Hidden Consoles
    * SSRF Impacts on Cloud Environments
    * Command Injections
    * SQL Injections
    * Peirates for Container Escape
    * Injecting Functionless Environments Using LambdaShell
    * Vulnerable application - insecure deserialization
    * Insecure secret management - no protection of encryption key
    * Redis - no authentication
* Attack API over server account - Authorization
  * /run/secrets/kubernetes.io/serviceaccount/token
  * /var/lib/kubelet/kubeconfig
  * $HOME/.kube/config
  * get secret
* Bypass RBAC
  * Seeking Extensive Privileges (kubernetes-rbac-audit)
* Exploit
  * CVE-2018-1002105 (Unauthenticated user to perform privilege escalation)
  * CVE-2020-8558 (kube-proxy route_localnet unauthenticated access node)
  * CVE-2020-8555 (SSRF)
  * kubelet-exploit
  * Exec a command / shell in a container via the API server
  * Launch a container onto the cluster via the API server
  * Abuse or set up a "volume mount" to steal/modify data or the host itself
  * Ask a Kubelet to exec a command / shell in an existing container
  * Interact with the Docker daemon on the host
  * Interact with the internal or external networks
  * Image 3rd vulnerability
  * Kubernetes CronJob
  * helm2 exposes a Tiller gRPC interface (Default:No authentication)
* Pod compromise
  * RCE into the Pod
  * Steal Service Tokens
  * Find the public IP of the cluster
  * Setup kubectl with the compromised token
  * Determine what you can do in the cluster
* Namespace compromise
  * Bypass the PSP to deploy a Pod
  * Port forwarding into the Pod
  * Finding other services in the cluster
* Namespace tenant bypass
  * Compromise the other Pod
  * Steal account token in new namespace
  * Deploy a privileged pod
* Node compromise
  * Compromise the Node
  * Deeper compromise
  * Stealing the kubelet config
* Cluster compromise
  * Creating mirror pods
  * Accessing the shell in the kube-system namespace
  * Helm v2 tiller to PwN the cluster (Retrieve tiller service account token)
* Combo
  * No permission to get secret but you can create pod. Create pod that include secret
  * From pod lateral movement
  * Bypass the PSP to deploy a Pod
  * Later movement - instance metadata
* Cluster Layer
  * No network policy
  * No authentication or access control
  * No logical segmentation - namespaces
  * No pod security controls
  * Lack of monitoring
* Application Layer
  * Vulnerable application - insecure deserialization
  * Insecure secret management - no protection of encryption key
  * Redis - no authentication
  * More
* Container Layer
  * Running as root
  * No hardening of container runtime
  * Insecure secret in container environment variables
*High Privileged
  * Privilege to Use Pods/Exec
  * Privilege to Get/Patch Rolebindings
  * Impersonating a Privileged Account
  * Privileged Service Account Token
* Defense evasion
  * Clear container logs
  * Delete Kubernetes events
  * Pod / container name similarity


## Kubernetes hardening
## Kubernetes hunting tools
