DC/OS on AWS
============
Creates a DC/OS Cluster on AWS

[Quick Start Guide](https://github.com/dcos-terraform/terraform-aws-dcos/blob/master/docs/published/README.md)

You can find more detailed documentation about this module here: [README.md](./docs/README.md)

EXAMPLE
-------

```hcl
module "dcos" {
  source  = "dcos-terraform/dcos/aws"
  version = "~> 0.2.0"

  providers = {
    aws = "aws"
  }

  cluster_name = "mydcoscluster"
  ssh_public_key_file = "~/.ssh/id_rsa.pub"
  admin_ips = ["198.51.100.0/24"]

  num_masters = "3"
  num_private_agents = "2"
  num_public_agents = "1"

  # availability_zones = ["<your_selected_region>a"]

  dcos_cluster_docker_credentials_enabled =  "true"
  dcos_cluster_docker_credentials_write_to_etc = "true"
  dcos_cluster_docker_credentials_dcos_owned = "false"
  dcos_cluster_docker_registry_url = "https://index.docker.io"
  dcos_use_proxy = "yes"
  dcos_http_proxy = "example.com"
  dcos_https_proxy = "example.com"
  dcos_no_proxy = <<EOF
# YAML
- "internal.net"
- "169.254.169.254"
  EOF
  dcos_overlay_network = <<EOF
# YAML
vtep_subnet: 44.128.0.0/20
vtep_mac_oui: 70:B3:D5:00:00:00
overlays:
  - name: dcos
    subnet: 12.0.0.0/8
    prefix: 26
EOF
  dcos_rexray_config = <<EOF
# YAML
rexray:
  loglevel: warn
  modules:
    default-admin:
      host: tcp://127.0.0.1:61003
  storageDrivers:
  - ec2
  volume:
    unmount:
      ignoreusedcount: true
EOF
  dcos_cluster_docker_credentials = <<EOF
# YAML
auths:
  'https://index.docker.io/v1/':
    auth: Ze9ja2VyY3licmljSmVFOEJrcTY2eTV1WHhnSkVuVndjVEE=
EOF

  # dcos_variant              = "ee"
  # dcos_license_key_contents = "${file("./license.txt")}"
  dcos_variant = "open"
}
```

## Inputs

| Name | Description | Type | Default | Required |
|------|-------------|:----:|:-----:|:-----:|
| admin\_ips | List of CIDR admin IPs | list | n/a | yes |
| ssh\_public\_key\_file | Path to SSH public key. This is mandatory but can be set to an empty string if you want to use ssh_public_key with the key as string. | string | n/a | yes |
| accepted\_internal\_networks | Subnet ranges for all internal networks | list | `<list>` | no |
| additional\_private\_agent\_ips | Additional private agent IPs. | list | `<list>` | no |
| additional\_public\_agent\_ips | Additional public agent IPs. | list | `<list>` | no |
| ansible\_additional\_config | Add additional config options to ansible. This is getting merged with generated defaults. Do not specify `dcos:` | string | `""` | no |
| ansible\_bundled\_container | Docker container with bundled dcos-ansible and ansible executables | string | `"mesosphere/dcos-ansible-bundle:latest"` | no |
| availability\_zones | List of availability_zones to be used as the same format that are required by the platform/cloud providers. i.e `['RegionZone']` | list | `<list>` | no |
| aws\_ami | AMI that will be used for the instances instead of the Mesosphere chosen default images. Custom AMIs must fulfill the Mesosphere DC/OS system-requirements: See https://docs.mesosphere.com/1.12/installing/production/system-requirements/ | string | `""` | no |
| aws\_key\_name | Specify the aws ssh key to use. We assume its already loaded in your SSH agent. Set ssh_public_key_file to empty string | string | `""` | no |
| bootstrap\_associate\_public\_ip\_address | [BOOTSTRAP] Associate a public ip address with there instances | string | `"true"` | no |
| bootstrap\_aws\_ami | [BOOTSTRAP] AMI to be used | string | `""` | no |
| bootstrap\_hostname\_format | [BOOTSTRAP] Format the hostname inputs are index+1, region, cluster_name | string | `"%[3]s-bootstrap%[1]d-%[2]s"` | no |
| bootstrap\_iam\_instance\_profile | [BOOTSTRAP] Instance profile to be used for these instances | string | `""` | no |
| bootstrap\_instance\_type | [BOOTSTRAP] Instance type | string | `"t2.medium"` | no |
| bootstrap\_os | [BOOTSTRAP] Operating system to use. Instead of using your own AMI you could use a provided OS. | string | `""` | no |
| bootstrap\_private\_ip | Private IP bootstrap nginx is listening on. Used to build the bootstrap URL. | string | `""` | no |
| bootstrap\_root\_volume\_size | [BOOTSTRAP] Root volume size in GB | string | `"80"` | no |
| bootstrap\_root\_volume\_type | [BOOTSTRAP] Root volume type | string | `"standard"` | no |
| cluster\_name | Name of the DC/OS cluster | string | `"dcos-example"` | no |
| cluster\_name\_random\_string | Add a random string to the cluster name | string | `"false"` | no |
| custom\_dcos\_download\_path | insert location of dcos installer script (optional) | string | `""` | no |
| dcos\_adminrouter\_tls\_1\_0\_enabled | Indicates whether to enable TLSv1 support in Admin Router. (optional) | string | `""` | no |
| dcos\_adminrouter\_tls\_1\_1\_enabled | Indicates whether to enable TLSv1.1 support in Admin Router. (optional) | string | `""` | no |
| dcos\_adminrouter\_tls\_1\_2\_enabled | Indicates whether to enable TLSv1.2 support in Admin Router. (optional) | string | `""` | no |
| dcos\_adminrouter\_tls\_cipher\_suite | [Enterprise DC/OS] Indicates whether to allow web browsers to send the DC/OS authentication cookie through a non-HTTPS connection. (optional) | string | `""` | no |
| dcos\_agent\_list | used to list the agents in the config.yaml (optional) | list | `<list>` | no |
| dcos\_audit\_logging | [Enterprise DC/OS] enable security decisions are logged for Mesos, Marathon, and Jobs. (optional) | string | `""` | no |
| dcos\_auth\_cookie\_secure\_flag | [Enterprise DC/OS] allow web browsers to send the DC/OS authentication cookie through a non-HTTPS connection. (optional) | string | `""` | no |
| dcos\_aws\_access\_key\_id | AWS key ID for exhibitor storage (optional but required with dcos_exhibitor_address) | string | `""` | no |
| dcos\_aws\_region | AWS region for exhibitor storage (optional but required with dcos_exhibitor_address) | string | `""` | no |
| dcos\_aws\_secret\_access\_key | AWS secret key for exhibitor storage (optional but required with dcos_exhibitor_address) | string | `""` | no |
| dcos\_aws\_template\_storage\_access\_key\_id | AWS key ID for CloudFormation template storage (optional) | string | `""` | no |
| dcos\_aws\_template\_storage\_bucket | AWS CloudFormation bucket name (optional) | string | `""` | no |
| dcos\_aws\_template\_storage\_bucket\_path | AWS CloudFormation bucket path (optional) | string | `""` | no |
| dcos\_aws\_template\_storage\_region\_name | AWS CloudFormation region name (optional) | string | `""` | no |
| dcos\_aws\_template\_storage\_secret\_access\_key | AWS secret key for the CloudFormation template (optional) | string | `""` | no |
| dcos\_aws\_template\_upload | To automatically upload the customized advanced templates to your S3 bucket. (optional) | string | `""` | no |
| dcos\_bootstrap\_port | Port of the bootstrap URL | string | `"8080"` | no |
| dcos\_bouncer\_expiration\_auth\_token\_days | [Enterprise DC/OS] Sets the auth token time-to-live (TTL) for Identity and Access Management. (optional) | string | `""` | no |
| dcos\_ca\_certificate\_chain\_path | [Enterprise DC/OS] Path (relative to the $DCOS_INSTALL_DIR) to a file containing the complete CA certification chain required for end-entity certificate verification, in the OpenSSL PEM format. (optional) | string | `""` | no |
| dcos\_ca\_certificate\_key\_path | [Enterprise DC/OS] Path (relative to the $DCOS_INSTALL_DIR) to a file containing a single X.509 certificate private key in the OpenSSL PEM format. (optional) | string | `""` | no |
| dcos\_ca\_certificate\_path | [Enterprise DC/OS] Path (relative to the $DCOS_INSTALL_DIR) to a file containing a single X.509 CA certificate in the OpenSSL PEM format. (optional) | string | `""` | no |
| dcos\_check\_time | Check if Network Time Protocol (NTP) is enabled during DC/OS startup. (optional) | string | `""` | no |
| dcos\_cluster\_docker\_credentials | Dictionary of Docker credentials to pass. (optional) | string | `""` | no |
| dcos\_cluster\_docker\_credentials\_dcos\_owned | Indicates whether to store the credentials file in /opt/mesosphere or /etc/mesosphere/docker_credentials. A sysadmin cannot edit /opt/mesosphere directly (optional) | string | `""` | no |
| dcos\_cluster\_docker\_credentials\_enabled | Indicates whether to pass the Mesos --docker_config option to Mesos. (optional) | string | `""` | no |
| dcos\_cluster\_docker\_credentials\_write\_to\_etc | Indicates whether to write a cluster credentials file. (optional) | string | `""` | no |
| dcos\_cluster\_docker\_registry\_enabled | DC/OS cluster docker registry enabled | string | `""` | no |
| dcos\_cluster\_docker\_registry\_url | The custom URL that Mesos uses to pull Docker images from. If set, it will configure the Mesos --docker_registry flag to the specified URL. (optional) | string | `""` | no |
| dcos\_cluster\_name | sets the DC/OS cluster name | string | `""` | no |
| dcos\_config | used to add any extra arguments in the config.yaml that are not specified here. (optional) | string | `""` | no |
| dcos\_custom\_checks | Custom installation checks that are added to the default check configuration process. (optional) | string | `""` | no |
| dcos\_customer\_key | [Enterprise DC/OS] sets the customer key (optional) | string | `""` | no |
| dcos\_dns\_bind\_ip\_blacklist | A list of IP addresses that DC/OS DNS resolvers cannot bind to. (optional) | string | `""` | no |
| dcos\_dns\_forward\_zones | Allow to forward DNS to certain domain requests to specific server. The following syntax must be used in combination with Terraform string heredoc. (optional) (:warning: DC/OS 1.10+) | string | `""` | no |
| dcos\_dns\_search | A space-separated list of domains that are tried when an unqualified domain is entered. (optional) | string | `""` | no |
| dcos\_docker\_remove\_delay | The amount of time to wait before removing stale Docker images stored on the agent nodes and the Docker image generated by the installer. (optional) | string | `""` | no |
| dcos\_enable\_docker\_gc | Indicates whether to run the docker-gc script, a simple Docker container and image garbage collection script, once every hour to clean up stray Docker containers. (optional) | string | `""` | no |
| dcos\_enable\_gpu\_isolation | Indicates whether to enable GPU support in DC/OS. (optional) | string | `""` | no |
| dcos\_enable\_mesos\_input\_plugin | Indicates whether to enable Telegraf's Mesos input plugin to collect Mesos metrics from Mesos masters and agents. Options: `true` or `false` (optional) | string | `""` | no |
| dcos\_exhibitor\_address | The address of the load balancer in front of the masters (recommended) | string | `""` | no |
| dcos\_exhibitor\_azure\_account\_key | the azure account key for exhibitor storage (optional but required with dcos_exhibitor_address) | string | `""` | no |
| dcos\_exhibitor\_azure\_account\_name | the azure account name for exhibitor storage (optional but required with dcos_exhibitor_address) | string | `""` | no |
| dcos\_exhibitor\_azure\_prefix | the azure account name for exhibitor storage (optional but required with dcos_exhibitor_address) | string | `""` | no |
| dcos\_exhibitor\_explicit\_keys | set whether you are using AWS API keys to grant Exhibitor access to S3. (optional) | string | `""` | no |
| dcos\_exhibitor\_storage\_backend | options are static, aws_s3, azure, or zookeeper (recommended) | string | `"static"` | no |
| dcos\_exhibitor\_zk\_hosts | a comma-separated list of one or more ZooKeeper node IP and port addresses to use for configuring the internal Exhibitor instances. (not recommended but required with exhibitor_storage_backend set to ZooKeeper. Use aws_s3 or azure instead. Assumes external ZooKeeper is already online.) | string | `""` | no |
| dcos\_exhibitor\_zk\_path | the filepath that Exhibitor uses to store data (not recommended but required with exhibitor_storage_backend set to zookeeper. Use aws_s3 or azureinstead. Assumes external ZooKeeper is already online.) | string | `""` | no |
| dcos\_fault\_domain\_detect\_contents | [Enterprise DC/OS] fault domain script contents. Optional but required if no fault-domain-detect script present. | string | `""` | no |
| dcos\_fault\_domain\_enabled | [Enterprise DC/OS] used to control if fault domain is enabled | string | `""` | no |
| dcos\_gc\_delay | The maximum amount of time to wait before cleaning up the executor directories (optional) | string | `""` | no |
| dcos\_gpus\_are\_scarce | Indicates whether to treat GPUs as a scarce resource in the cluster. (optional) | string | `""` | no |
| dcos\_http\_proxy | http proxy (optional) | string | `""` | no |
| dcos\_https\_proxy | https proxy (optional) | string | `""` | no |
| dcos\_image\_commit | The commit hash for the build of DC/OS | string | `""` | no |
| dcos\_instance\_os | Operating system to use. Instead of using your own AMI you could use a provided OS. | string | `"centos_7.4"` | no |
| dcos\_ip\_detect\_contents | Allows DC/OS to detect your private address. Use this to pass this as an input to the module rather than a file in side your bootstrap node. (recommended) | string | `""` | no |
| dcos\_ip\_detect\_public\_contents | Allows DC/OS to be aware of your publicly routeable address for ease of use (recommended) | string | `""` | no |
| dcos\_ip\_detect\_public\_filename | statically set your detect-ip-public path | string | `"genconf/ip-detect-public"` | no |
| dcos\_l4lb\_enable\_ipv6 | A boolean that indicates if layer 4 load balancing is available for IPv6 networks. (optional) | string | `""` | no |
| dcos\_license\_key\_contents | [Enterprise DC/OS] used to privide the license key of DC/OS for Enterprise Edition. Optional if license.txt is present on bootstrap node. | string | `""` | no |
| dcos\_log\_directory | The path to the installer host logs from the SSH processes. (optional) | string | `""` | no |
| dcos\_master\_discovery | The Mesos master discovery method. The available options are static or master_http_loadbalancer. (recommend the use of master_http_loadbalancer) | string | `"static"` | no |
| dcos\_master\_dns\_bindall | Indicates whether the master DNS port is open. (optional) | string | `""` | no |
| dcos\_master\_external\_loadbalancer | Allows DC/OS to configure certs around the External Load Balancer name. If not used SSL verfication issues will arrise. EE only. (recommended) | string | `""` | no |
| dcos\_master\_list | statically set your master nodes (not recommended but required with exhibitor_storage_backend set to static. Use aws_s3 or azure instead, that way you can replace masters in the cloud.) | string | `""` | no |
| dcos\_mesos\_container\_log\_sink | The log manager for containers (tasks). The options are to send logs to: 'journald', 'logrotate', 'journald+logrotate'. (optional) | string | `""` | no |
| dcos\_mesos\_dns\_set\_truncate\_bit | Indicates whether to set the truncate bit if the response is too large to fit in a single packet. (optional) | string | `""` | no |
| dcos\_mesos\_max\_completed\_tasks\_per\_framework | The number of completed tasks for each framework that the Mesos master will retain in memory. (optional) | string | `""` | no |
| dcos\_no\_proxy | A YAML nested list (-) of addresses to exclude from the proxy. (optional) | string | `""` | no |
| dcos\_num\_masters | set the num of master nodes (required with exhibitor_storage_backend set to aws_s3, azure, ZooKeeper) | string | `""` | no |
| dcos\_oauth\_enabled | [Open DC/OS Only] Indicates whether to enable authentication for your cluster. (optional) | string | `""` | no |
| dcos\_overlay\_config\_attempts | Specifies how many failed configuration attempts are allowed before the overlay configuration modules stop trying to configure an virtual network. (optional) | string | `""` | no |
| dcos\_overlay\_enable | Enable to disable overlay (optional) | string | `""` | no |
| dcos\_overlay\_mtu | The maximum transmission unit (MTU) of the Virtual Ethernet (vEth) on the containers that are launched on the overlay. (optional) | string | `""` | no |
| dcos\_overlay\_network | This group of parameters define an virtual network for DC/OS. (optional) | string | `""` | no |
| dcos\_package\_storage\_uri | Where to permanently store DC/OS packages. The value must be a file URL. (optional) | string | `""` | no |
| dcos\_previous\_version | DC/OS 1.9+ requires users to set this value to ensure users know the version. Terraform helps populate this value, but users can override it here. (recommended) | string | `""` | no |
| dcos\_previous\_version\_master\_index | Used to track the index of master for quering the previous DC/OS version during upgrading. (optional) applicable: 1.9+ | string | `"0"` | no |
| dcos\_process\_timeout | The allowable amount of time, in seconds, for an action to begin after the process forks. (optional) | string | `""` | no |
| dcos\_public\_agent\_list | statically set your public agents (not recommended) | list | `<list>` | no |
| dcos\_resolvers | A YAML nested list (-) of DNS resolvers for your DC/OS cluster nodes. (recommended) | list | `<list>` | no |
| dcos\_rexray\_config | The REX-Ray configuration method for enabling external persistent volumes in Marathon. (optional) | string | `""` | no |
| dcos\_rexray\_config\_filename | The REX-Ray configuration filename for enabling external persistent volumes in Marathon. (optional) | string | `""` | no |
| dcos\_rexray\_config\_method | The REX-Ray configuration method for enabling external persistent volumes in Marathon. (optional) | string | `""` | no |
| dcos\_s3\_bucket | name of the s3 bucket for the exhibitor backend (recommended but required with dcos_exhibitor_address) | string | `""` | no |
| dcos\_s3\_prefix | name of the s3 prefix for the exhibitor backend (recommended but required with dcos_exhibitor_address) | string | `""` | no |
| dcos\_security | [Enterprise DC/OS] set the security level of DC/OS, either 'strict' or 'permissive'. Default is 'permissive'. (recommended) | string | `""` | no |
| dcos\_skip\_checks | Upgrade option: Used to skip all dcos checks that may block an upgrade if any DC/OS component is unhealthly. (optional) applicable: 1.10+ | string | `"false"` | no |
| dcos\_staged\_package\_storage\_uri | Where to temporarily store DC/OS packages while they are being added. (optional) | string | `""` | no |
| dcos\_superuser\_password\_hash | [Enterprise DC/OS] set the superuser password hash (recommended) | string | `""` | no |
| dcos\_superuser\_username | [Enterprise DC/OS] set the superuser username (recommended) | string | `""` | no |
| dcos\_telemetry\_enabled | Change the telemetry option (optional) | string | `""` | no |
| dcos\_ucr\_default\_bridge\_subnet | IPv4 subnet allocated to the mesos-bridge CNI network for UCR bridge-mode networking. (optional) | string | `""` | no |
| dcos\_use\_proxy | To enable use of proxy for internal routing (optional) | string | `""` | no |
| dcos\_variant | Specifies which DC/OS variant it should be: `open` (Open Source) or `ee` (Enterprise Edition) | string | `"open"` | no |
| dcos\_version | Specifies which DC/OS version instruction to use. Options: 1.13.1, 1.12.3, 1.11.10, etc. See dcos_download_path or dcos_version tree for a full list. | string | `"1.13.1"` | no |
| dcos\_zk\_agent\_credentials | [Enterprise DC/OS] set the ZooKeeper agent credentials (recommended) | string | `""` | no |
| dcos\_zk\_master\_credentials | [Enterprise DC/OS] set the ZooKeeper master credentials (recommended) | string | `""` | no |
| dcos\_zk\_super\_credentials | [Enterprise DC/OS] set the zk super credentials (recommended) | string | `""` | no |
| masters\_acm\_cert\_arn | ACM certifacte to be used for the masters load balancer | string | `""` | no |
| masters\_associate\_public\_ip\_address | [MASTERS] Associate a public ip address with there instances | string | `"true"` | no |
| masters\_aws\_ami | [MASTERS] AMI to be used | string | `""` | no |
| masters\_hostname\_format | [MASTERS] Format the hostname inputs are index+1, region, cluster_name | string | `"%[3]s-master%[1]d-%[2]s"` | no |
| masters\_iam\_instance\_profile | [MASTERS] Instance profile to be used for these instances | string | `""` | no |
| masters\_instance\_type | [MASTERS] Instance type | string | `"m4.xlarge"` | no |
| masters\_internal\_acm\_cert\_arn | ACM certifacte to be used for the internal masters load balancer | string | `""` | no |
| masters\_os | [MASTERS] Operating system to use. Instead of using your own AMI you could use a provided OS. | string | `""` | no |
| masters\_root\_volume\_size | [MASTERS] Root volume size in GB | string | `"120"` | no |
| num\_masters | Specify the amount of masters. For redundancy you should have at least 3 | string | `"3"` | no |
| num\_of\_private\_agents | Specify the amount of private agents. These agents will provide your main resources | string | `""` | no |
| num\_of\_public\_agents | Specify the amount of public agents. These agents will host marathon-lb and edgelb | string | `""` | no |
| num\_private\_agents | Specify the amount of private agents. These agents will provide your main resources | string | `"2"` | no |
| num\_public\_agents | Specify the amount of public agents. These agents will host marathon-lb and edgelb | string | `"1"` | no |
| private\_agents\_associate\_public\_ip\_address | [PRIVATE AGENTS] Associate a public ip address with there instances | string | `"true"` | no |
| private\_agents\_aws\_ami | [PRIVATE AGENTS] AMI to be used | string | `""` | no |
| private\_agents\_extra\_volumes | [PRIVATE AGENTS] Extra volumes for each private agent | list | `<list>` | no |
| private\_agents\_hostname\_format | [PRIVATE AGENTS] Format the hostname inputs are index+1, region, cluster_name | string | `"%[3]s-privateagent%[1]d-%[2]s"` | no |
| private\_agents\_iam\_instance\_profile | [PRIVATE AGENTS] Instance profile to be used for these instances | string | `""` | no |
| private\_agents\_instance\_type | [PRIVATE AGENTS] Instance type | string | `"m4.xlarge"` | no |
| private\_agents\_os | [PRIVATE AGENTS] Operating system to use. Instead of using your own AMI you could use a provided OS. | string | `""` | no |
| private\_agents\_root\_volume\_size | [PRIVATE AGENTS] Root volume size in GB | string | `"120"` | no |
| private\_agents\_root\_volume\_type | [PRIVATE AGENTS] Root volume type | string | `"gp2"` | no |
| public\_agents\_access\_ips | List of ips allowed access to public agents. admin_ips are joined to this list | list | `<list>` | no |
| public\_agents\_acm\_cert\_arn | ACM certifacte to be used for the public agents load balancer | string | `""` | no |
| public\_agents\_additional\_ports | List of additional ports allowed for public access on public agents (80 and 443 open by default) | list | `<list>` | no |
| public\_agents\_associate\_public\_ip\_address | [PUBLIC AGENTS] Associate a public ip address with there instances | string | `"true"` | no |
| public\_agents\_aws\_ami | [PUBLIC AGENTS] AMI to be used | string | `""` | no |
| public\_agents\_hostname\_format | [PUBLIC AGENTS] Format the hostname inputs are index+1, region, cluster_name | string | `"%[3]s-publicagent%[1]d-%[2]s"` | no |
| public\_agents\_iam\_instance\_profile | [PUBLIC AGENTS] Instance profile to be used for these instances | string | `""` | no |
| public\_agents\_instance\_type | [PUBLIC AGENTS] Instance type | string | `"m4.xlarge"` | no |
| public\_agents\_os | [PUBLIC AGENTS] Operating system to use. Instead of using your own AMI you could use a provided OS. | string | `""` | no |
| public\_agents\_root\_volume\_size | [PUBLIC AGENTS] Root volume size | string | `"120"` | no |
| public\_agents\_root\_volume\_type | [PUBLIC AGENTS] Specify the root volume type. | string | `"gp2"` | no |
| ssh\_public\_key | SSH public key in authorized keys format (e.g. 'ssh-rsa ..') to be used with the instances. Make sure you added this key to your ssh-agent. | string | `""` | no |
| subnet\_range | Private IP space to be used in CIDR format | string | `"172.16.0.0/16"` | no |
| tags | Add custom tags to all resources | map | `<map>` | no |
| with\_replaceable\_masters | Create S3 bucket for exhibitor and configure DC/OS to use it. | string | `"false"` | no |

## Outputs

| Name | Description |
|------|-------------|
| config | The battle-tested provisioner contents of the output by DC/OS role to perform requried admin actions in behalf of the user as documented in http://mesosphere.com and http://dcos.io |
| infrastructure.aws\_key\_name | This is the AWS key name used for the cluster |
| infrastructure.bootstrap.instance | Bootstrap instance ID |
| infrastructure.bootstrap.os\_user | Bootstrap instance OS default user |
| infrastructure.bootstrap.prereq-id | Returns the ID of the prereq script for bootstrap (if user_data or ami are not used) |
| infrastructure.bootstrap.private\_ip | Private IP of the bootstrap instance |
| infrastructure.bootstrap.public\_ip | Public IP of the bootstrap instance |
| infrastructure.iam.agent\_profile | Name of the agent profile |
| infrastructure.lb.masters\_dns\_name | This is the load balancer to access the DC/OS UI |
| infrastructure.lb.masters\_internal\_dns\_name | This is the load balancer to access the masters internally in the cluster |
| infrastructure.lb.public\_agents\_dns\_name | This is the load balancer to reach the public agents |
| infrastructure.masters.instances | Master instances IDs |
| infrastructure.masters.os\_user | Master instances OS default user |
| infrastructure.masters.prereq-id | Returns the ID of the prereq script for masters (if user_data or ami are not used) |
| infrastructure.masters.private\_ips | Master instances private IPs |
| infrastructure.masters.public\_ips | Master instances public IPs |
| infrastructure.private\_agents.instances | Private Agent instances IDs |
| infrastructure.private\_agents.os\_user | Private Agent instances OS default user |
| infrastructure.private\_agents.prereq-id | Returns the ID of the prereq script for private agents (if user_data or ami are not used) |
| infrastructure.private\_agents.private\_ips | Private Agent instances private IPs |
| infrastructure.private\_agents.public\_ips | Private Agent public IPs |
| infrastructure.public\_agents.instances | Public Agent instances IDs |
| infrastructure.public\_agents.os\_user | Public Agent instances OS default user |
| infrastructure.public\_agents.prereq-id | Returns the ID of the prereq script for public agents (if user_data or ami are not used) |
| infrastructure.public\_agents.private\_ips | Public Agent instances private IPs |
| infrastructure.public\_agents.public\_ips | Public Agent public IPs |
| infrastructure.security\_groups.admin | This is the id of the admin security_group that the cluster is in |
| infrastructure.security\_groups.internal | This is the id of the internal security_group that the cluster is in |
| infrastructure.vpc.cidr\_block | This is the id of the VPC the cluster is in |
| infrastructure.vpc.id | This is the id of the VPC the cluster is in |
| infrastructure.vpc.main\_route\_table\_id | This is the id of the VPC's main routing table the cluster is in |
| infrastructure.vpc.subnet\_ids | This is the list of subnet_ids the cluster is in |
| masters-internal-loadbalancer | This is the internal load balancer address to access the DC/OS Services |
| masters-ips | Master IP addresses |
| masters-loadbalancer | This is the load balancer address to access the DC/OS UI |
| public-agents-loadbalancer | This is the load balancer address to access the DC/OS public agents |

