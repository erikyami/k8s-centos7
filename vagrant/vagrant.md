# Vagrant

Vagrant é uma ferramenta para criação e gerenciamento de ambiente de máquinas virtuais, focada na automação, facilitando a subida do nosso laboratório.

## Arquivo Vagrantfile

É o arquivo responsável para descrever as máquinas que compoem nosso ambiente. Depois de configurar o Vagrantfile, basta rodar o comando `vagrant up` para subir o laboratório.

## Comandos úteis

### Subindo o ambiente:

```
vagrant up
``` 

### Baixando o ambiente:

```
vagrant halt
``` 

### Vericando o status

```
vagrant status
``` 

### Conectando em uma VM

```
vagrant ssh <nome_da_maquina>
``` 