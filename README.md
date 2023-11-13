# Ansible


#### Configurando/gerenciando via Ansible dois servidores em diferentes SO’s que rode apache com PHP, persistência MySQL e hospedagem de site em WordPress

Meu SO é Linux então instalei Virtual Machines nativas (KMV) apenas para aplicar os conceitos desse projeto

Criei 3 VM’s ( 1ª Ubuntu com o Ansible, 2ª Ubuntu e 3ª CentOS)
	virt-manager

1ª VM
- configurei chave ssh e copiei conteúdo da chave pública pra autenticar nas outras maquinas sem pedir senha, pois essa terá o gerenciador Ansible pra executar os comandos nas demais
	ssh-keygen
	cat .ssh/id_rsa.pub
	ssh-copy-id username@remote_host
- instalei o Ansible
	sudo apt install ansible
- criei o arquivo de configuração ‘ansible.cfg’ (o Ansible tem um arquivo de config por padrão, mas é recomendado personalizar um pra cada playbook)
- criei o arquivo de inventário ‘inventory.yaml’ (endereços hosts, ip’s das máquinas)
- criei o arquivo de instruções ‘playbook.yaml’ (pra automatizar as tarefas, referenciando cada role)
- criei roles no diretório task (pra agrupar e organizar melhor as tarefas), com chamadas para handlers (subtarefas)
	ansible-galaxy init roleName	
	- role serverTools: seta o nome do host definido no inventory, instala alguns pacotes, abre o protocolo http (apenas para laboratório), desabilita o SELinux mas no ambiente de dev o certo é configurá-lo
	- role mysql: instalei o banco de dados MariaDB nas 2 VM’s, configurei pra que fique startado não só no momento da instalação, via socket do módulo do mysql (mariadb é um fork do mysql) é criado um banco de dados utilizando variáveis (diretório vars, mas no ambiente de dev é mais seguro imputar no servidor variáveis de ambiente) em seguida criação de usuário e seus privilégios, e por ultima a task que remove usuários anônimos por uma boa prática
- role apache: instalação do servidor web Apache nos dois SO’s: ubuntu e redhat cada qual com um arquivo, referenciando-os no main, handlers para habilitar módulos do apache, habilitar o site pro Apache, arquivo de config que o site utiliza, virtual host com as configs do site...

*adicionei o virtual host (jacque.local) no arquivo de host local ao acessar o site pelo navegador

2ª e 3ª VM
Configurei 2 servidores pra hospedar um site em wordpress no Apache 
- alterei permissão no arquivo sudoers e visudo pra que o usuário root não precise se autenticar e inseri o mesmo usuario no grupo sudo
	username ALL=(ALL:ALL) NOPASSWD:ALL
	usermod -aG sudo username / usermod -aG wheel username #centos
	
