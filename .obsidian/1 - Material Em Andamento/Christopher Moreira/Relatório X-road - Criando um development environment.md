2026-06-24 18:46

Status: #baby 

Tags: [[x-road]] [[desenvolvimento]] [[ambiente de desenvolvimento]] [[programação]] [[ansible]]

Autor: Christopher
- - -
# Decorrer
## Setup Inicial
Primeiro vamos fazer o setup inicial que a aplicação x-road precisa para desenvolvimento.

**Seguem a baixo as instalações e regras que precisam ser aplicadas na criação do ambiente:**
### S.O
$_ `lsb_release -a`
	`Distributor ID:	Ubuntu`
	`Description:	Ubuntu 16.04.7 LTS`
	`Release:	16.04`
	`Codename:	xenial`

### Initial Stack
$_ `git --version`
	`git version 2.7.4`

$_ `ansible --version`
	`ansible 2.0.0.2`
	  `config file = /etc/ansible/ansible.cfg`
	  `configured module search path = Default w/o overrides`

 $_ `docker --version`
	`Docker version 18.09.7, build 2d0083d`

---
Tendo o Setup inicial da aplicação, iramos clonar o repositorio:
`git clone https://github.com/nordic-institute/X-Road.git`

Temos que ter a seguinte listagem de diretórios - 24/06/2026

`~/demo/X-Road$ ls -la`
`total 588`
`drwxrwxr-x 12 xroad xroad   4096 Jun 24 19:45 .`
`drwxrwxr-x  3 xroad xroad   4096 Jun 24 19:44 ..`
`-rw-rw-r--  1 xroad xroad 165211 Jun 24 19:45 CHANGELOG.md`
`-rw-rw-r--  1 xroad xroad   5151 Jun 24 19:45 CODE_OF_CONDUCT.md`
`-rw-rw-r--  1 xroad xroad  16331 Jun 24 19:45 CONTRIBUTING.md`
`drwxrwxr-x  6 xroad xroad   4096 Jun 24 19:45 deployment`
`drwxrwxr-x 10 xroad xroad   4096 Jun 24 19:45 development`
`drwxrwxr-x 11 xroad xroad   4096 Jun 24 19:45 doc`
`drwxrwxr-x  8 xroad xroad   4096 Jun 24 19:45 .git`
`-rw-rw-r--  1 xroad xroad     61 Jun 24 19:45 .gitattributes`
`drwxrwxr-x  2 xroad xroad   4096 Jun 24 19:45 .githooks`
`drwxrwxr-x  4 xroad xroad   4096 Jun 24 19:45 .github`
`-rw-rw-r--  1 xroad xroad    244 Jun 24 19:45 .gitignore`
`drwxrwxr-x  3 xroad xroad   4096 Jun 24 19:45 img`
`-rw-rw-r--  1 xroad xroad   1371 Jun 24 19:45 LICENSE`
`-rw-rw-r--  1 xroad xroad  12435 Jun 24 19:45 .ort.yml`
`-rw-rw-r--  1 xroad xroad   6383 Jun 24 19:45 publiccode.yml`
`-rw-rw-r--  1 xroad xroad   5279 Jun 24 19:45 README.md`
`drwxrwxr-x  2 xroad xroad   4096 Jun 24 19:45 .scripts`
`-rw-rw-r--  1 xroad xroad   6294 Jun 24 19:45 SECURITY.md`
`drwxrwxr-x  7 xroad xroad   4096 Jun 24 19:45 sidecar`
`drwxrwxr-x 15 xroad xroad   4096 Jun 24 19:45 src`
`-rw-rw-r--  1 xroad xroad  11733 Jun 24 19:45 TRADEMARK.md`
`-rw-rw-r--  1 xroad xroad   2242 Jun 24 19:45 usage_scenario.yml`
`-rw-rw-r--  1 xroad xroad  16745 Jun 24 19:45 xroad_logo_small.png`
`-rw-rw-r--  1 xroad xroad 269241 Jun 24 19:45 X-Road_overview.png`
`xroad@xroad-virtual-machine:~/demo/X-Road$` 

---
Acessamos o diretorio do ansible em development/ansible
`cd development/ansible`
vamos começar buildando o [[ansible inventory]]

Em
`cd hosts/`
vamos começar copiando o arquivo
`cp lxd_hosts.txt demo-hosts.txt`

E passamos a editar o arquivo que clonamos, ele deve ficar assim:
	
	`#central servers (ubuntu lxd containers)`
	`[cs_servers]`
	`demo-cs ansible_connection=lxd`
	
	`#configuration proxies (ubuntu lxd containers, optional)`
	`[cp_servers]`
	`#xroad-lxd-cp ansible_connection=lxd`
	
	`#certification authority, time stamping authority and ocsp service server for testing purposes (ubuntu)`
	`[ca_servers]`
	`demo-ca ansible_connection=lxd ubuntu_releasever=noble`
	
	`#security servers (ubuntu lxd containers)`
	`[ss_servers]`
	`demo-ss1 ansible_connection=lxd`
	`demo-ss2 ansible_connection=lxs`
	
	`[ss_servers:children]`
	`rhel_ss`
	
	`#security servers (centos lxd containers, not supported in variant ee)`
	`[rhel_ss]`
	`#xroad-lxd-rh-ss1 ansible_connection=lxd`
	
	`#container host`
	`[lxd_servers]`
	`localhost ansible_connection=local`
	
	`#compilation host`
	`[compile_servers]`
	`localhost ansible_connection=local`
	### vars ###
	
	`[ss_servers:vars]`
	`variant=vanilla`
	
	`[demo-servers:children]`
	`ss-servers`
	`cs-server`

Em comparação ao arquivo final oque fizemos foi:
- Renomear o security Server
- Cirar dois Security Servers
- Renomear o central Server
- E subir tanto ss quanto cs servers como children
- - -
# Referências

