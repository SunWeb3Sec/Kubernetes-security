# Kubernetes pentesting, hardening and hunting tools

Share my k8s research materials that I did two years ago.

This repo aims to how to secure your kubernetes (K8s) environment.

[Kubernetes pentesting](https://github.com/SunWeb3Sec/Kubernetes-security#kubernetes-pentesting)

[Kubernetes hardening](https://github.com/SunWeb3Sec/Kubernetes-security#kubernetes-hardening)

[Kubernetes hunting tools](https://github.com/SunWeb3Sec/Kubernetes-security#kubernetes-hunting-tools)

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
      * Bad Pod #1: Everything allowed
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
* Secure EKS Cluster
  * Cloud Infrastructure Security
  * VPC Layout
  * Dedicated IAM Role for EKS Cluster Creation
  * Cluster Resource Tagging
  * Control SSH Access to Nodes
  * EC2 Security Groups for Nodes
  * Don’t Install the Kubernetes Dashboard
  * AWS Fargate for Nodeless EKS (AWS does not recommend running sensitive workloads on Fargate.)
  * IAM Policies and the Principle of Least Privilege( cluster autoscaler by using the IAM policy Condition)
  * Isolating Critical Cluster Workloads
  * Manage IAM Credentials for Pods
* Authentication
  * --anonymous-auth=false (Default:true)
  * To enable X509 client certificate authentication to the kubelet's HTTPS endpoint
* Authorization (RBAC, Node, ABAC or Webhook)
  * –authorization-mode is not set to AlwaysAllow, as the more secure Webhook mode will delegate authorization decisions to the Kubernetes API server.
  * Do not grant write access to ConfigMaps in ClusterRoles, which apply globally across all namespaces. Use RoleBindings to limit these permissions to specific namespaces.
* Admission Control
  * Gatekeeper
  * Enable NodeRestriction admission plugin to limit a kubelet to modify its own node) pods and pod status
  * AlwaysPullImages
  * DenyEscalatingExec
  * ResourceQuota
  * LimitRanger
* Pod security. Admins can control specific actions.
  * Pod security policy
  * Restrict the containers that can run as privileged (Delete the default pod security policy)
  * Do not run processes in containers as root
  * Do not allow privileged escalation
  * Restrict the use of hostPath or if hostPath is necessary restrict which prefixes can be used and configure the volume as read-only (By default pods that run as root will have write access to the file system exposed by hostPath) prevent symbolic links threat
  * Set requests and limits for each container to avoid resource contention and DoS attacks
Quality Of Service (QoS)
  * Privileged: false
  * runAsUser:    rule: 'MustRunAsNonRoot'
  * AllowPrivilegeEscalation=false
* Multi-tenancy
  * Namespace / RBAC
  * Node selector
  * Anti-Affinity Rules
  * Taints / Tolerations
* Network security
  * Network policies. The default is that all pods talk to all pods. Consider changing it.
  * Traffic control
  * Network Policies (Calico)
    * Create a default deny policy
    * Create a rule to allow DNS queries
    * Incrementally add rules to selectively allow the flow of traffic between namespaces/pods
    * Log network traffic metadata
    * Use encryption with AWS load balancers
  * Security Groups
  * Encryption in transit
  * Service Mesh
  * Container Network Interfaces (CNIs)
  * Nitro Instances
* Kubernetes secrets. Use secrets to store sensitive data instead of config maps.
* ETCD
  * PKI-based authentication for etcd
  * Encryption at rest
  * etcd peer-to-peer TLS
  * Kubernetes API to etcd cluster TLS
* Image Security
  * Private registries
  * Image signing (use only signed images from trusted registry)
  * Image vulnerability check
* Container security
  * Dockerfile
  * Do not expose the Docker daemon socket
  * Set a non-root user
  * Add –no-new-privileges flag
  * Prevent Docker in docker (DIND)
  * Docker Bench for Security
  * Preventing containers from loading unwanted kernel modules
    * /etc/modprobe.d/kubernetes-blacklist.conf
* Runtime security
  * SELinux
  * AppArmor (EX: enforce AppArmor profiles on pods via Pod Security Policies. Prevent the attack pods from writing to files in the host’s filesystem)
  * Seccomp 
  * Falco
* Detective Controls (Audit logging. Watch them!)
  * Auditing and logging
  * Enable audit logs
  * Create alarms for suspicious events
  * Analyzing Log Data with CloudWatch Logs Insights
  * Audit your CloudTrail logs (IRSA)
  * Falco log
  * Audit all the container's activity
* Infrastructure Security
  * Use an OS optimized for running containers
  * Treat your infrastructure as immutable and automate the replacement of your worker nodes
  * Periodically run kube-bench to verify compliance with CIS benchmarks for Kubernetes
  * Minimize access to worker nodes
  * Deploy workers onto private subnets
  * Run Amazon Inspector to assess hosts for exposure, vulnerabilities, and deviations from best practices
  * Harden the host (always patch)
  * Separate partitions for containers
* Defense
  * Don’t allow privileged Pods
  * Don't allow a container to become root
  * Don’t allow host mounts at all
  * Consider a network plugin or Network Policy for segmentation
  * Only use images and registries that you trust and don’t rely on Docker Hub as a trusted source
  * Keep roles and role bindings as strict as possible
  * Don’t automount Service Tokens into Pods if your services don’t need to communicate to the API
  * Consider abstracting direct console access to the cluster away (ie Terraform, Spinnaker) so that none of your developers have cluster-admin permission.

## Kubernetes hunting tools
* [kubiscan](https://github.com/cyberark/KubiScan)
* [Kube hunter](https://github.com/aquasecurity/kube-hunter)
* [Kube bench](https://github.com/aquasecurity/kube-bench)
* [kubeaudit](https://github.com/Shopify/kubeaudit) 
* [kubescore](https://github.com/zegl/kube-score)
* [checkov](https://github.com/bridgecrewio/checkov)
* [kubesec](https://github.com/controlplaneio/kubesec)
* [kubesploit](https://github.com/cyberark/kubesploit)
* [Kubernetes Goat](https://github.com/madhuakula/kubernetes-goat)
