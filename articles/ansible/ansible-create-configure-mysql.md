---
title: Tutorial – Configurar bancos de dados no Banco de Dados do Azure para MySQL usando o Ansible
description: Saiba como usar o Ansible para criar e configurar um servidor do Banco de Dados do Azure para MySQL
keywords: ansible, azure, devops, bash, guia estratégico, mysql, banco de dados
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 1b6c9a9aa3abbda7ffd72db0ecb137b3c9da1a6c
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72241839"
---
# <a name="tutorial-configure-databases-in-azure-database-for-mysql-using-ansible"></a>Tutorial: Configurar bancos de dados no Banco de Dados do Azure para MySQL usando o Ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

O [Banco de Dados do Azure para MySQL](/azure/mysql/overview) é um serviço de banco de dados relacional baseado no MySQL Community Edition. O Banco de Dados do Azure para MySQL permite gerenciar bancos de dados do MySQL em seus aplicativos Web.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Criar um servidor MySql
> * Criar um banco de dados MySql
> * Configurar uma regra de firewall para que um aplicativo externo possa se conectar ao servidor
> * Conectar-se ao servidor MySql a partir do Azure Cloud Shell
> * Consultar os servidores disponíveis do MySQL
> * Listar todos os bancos de dados nos servidores conectados

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

O código do guia estratégico nesta seção cria um grupo de recursos do Azure. Um grupo de recursos é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados.  

Salve o guia estratégico a seguir como `rg.yml`:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    location: eastus 
  tasks:
    - name: Create a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"
```

Antes de executar o guia estratégico, confira as observações a seguir:

* Um grupo de recursos chamado `myResourceGroup` é criado.
* O grupo de recursos é criado na localização `eastus`:

Execute o guia estratégico usando o comando `ansible-playbook`:

```bash
ansible-playbook rg.yml
```

## <a name="create-a-mysql-server-and-database"></a>Criar um banco de dados e um servidor MySQL

O código do guia estratégico nesta seção cria um servidor MySQL e um Banco de Dados do Azure para MySQL. O novo servidor MySQL é um servidor Gen 5 de Propósito Básico com um vCore e é chamado `mysqlserveransible`. A instância de banco de dados é chamada `mysqldbansible`.

Para saber mais sobre tipos de preço, confira [Tipos de preço do Banco de Dados do Azure para MySQL](/azure/mysql/concepts-pricing-tiers). 

Salve o guia estratégico a seguir como `mysql_create.yml`:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    location: eastus
    mysqlserver_name: mysqlserveransible
    mysqldb_name: mysqldbansible
    admin_username: mysqladmin
    admin_password: <server_admin_password> 
  tasks:
    - name: Create MySQL Server
      azure_rm_mysqlserver:
        resource_group: "{{ resource_group }}"
        name: "{{ mysqlserver_name }}"
        sku:
          name: B_Gen5_1
          tier: Basic
        location: "{{ location }}"
        version: 5.6
        enforce_ssl: True
        admin_username: "{{ admin_username }}"
        admin_password: "{{ admin_password }}"
        storage_mb: 51200
    - name: Create instance of MySQL Database
      azure_rm_mysqldatabase:
        resource_group: "{{ resource_group }}"
        server_name: "{{ mysqlserver_name }}"
        name: "{{ mysqldb_name }}"
```

Antes de executar o guia estratégico, confira as observações a seguir:

* Na seção `vars`, o valor de `mysqlserver_name` deve ser exclusivo.
* Na seção `vars`, substitua `<server_admin_password>` por uma senha.

Execute o guia estratégico usando o comando `ansible-playbook`:

```bash
ansible-playbook mysql_create.yml
```

## <a name="configure-a-firewall-rule"></a>Configurar uma regra de firewall

Uma regra de firewall de nível de servidor permite que um aplicativo externo se conecte ao servidor por meio do firewall do serviço do Azure MySQL. Exemplos de aplicativos externos são a ferramenta de linha de comando `mysql` e o MySQL Workbench.

O código do guia estratégico nesta seção cria uma regra de firewall chamada `extenalaccess` que permite conexões de qualquer endereço IP externo. 

Salve o guia estratégico a seguir como `mysql_firewall.yml`:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    mysqlserver_name: mysqlserveransible
  tasks:
  - name: Open firewall to access MySQL Server from outside
    azure_rm_resource:
      api_version: '2017-12-01'
      resource_group: "{{ resource_group }}"
      provider: dbformysql
      resource_type: servers
      resource_name: "{{ mysqlserver_name }}"
      subresource:
        - type: firewallrules
          name: externalaccess
      body:
        properties:
          startIpAddress: "0.0.0.0"
          endIpAddress: "255.255.255.255"
```

Antes de executar o guia estratégico, confira as observações a seguir:

* Na seção vars, substitua `startIpAddress` e `endIpAddress`. Use o intervalo de endereços IP que corresponde ao intervalo do qual você vai se conectar.
* As conexões ao Banco de Dados do Azure para MySQL se comunicam pela porta 3306. Se estiver tentando se conectar em uma rede corporativa, talvez o tráfego de saída pela porta 3306 não seja permitido. Nesse caso, só será possível se conectar a seu servidor se seu departamento de TI abrir a porta 3306.
* O guia estratégico usa o módulo `azure_rm_resource` que permite o uso direto da API REST.

Execute o guia estratégico usando o comando `ansible-playbook`:

```bash
ansible-playbook mysql_firewall.yml
```

## <a name="connect-to-the-server"></a>Conectar ao servidor

Nesta seção, você usará o Azure Cloud Shell para se conectar ao servidor criado anteriormente.

1. Marque o botão **Experimente** no código a seguir:

    ```azurecli-interactive
    mysql -h mysqlserveransible.mysql.database.azure.com -u mysqladmin@mysqlserveransible -p
    ```

1. No prompt, insira o seguinte comando para consultar o status do servidor:

    ```sql
    mysql> status
    ```
    
    Se tudo correr bem, você verá um resultado semelhante para os seguintes resultados:
    
    ```
    demo@Azure:~$ mysql -h mysqlserveransible.mysql.database.azure.com -u mysqladmin@mysqlserveransible -p
    Enter password:
    Welcome to the MySQL monitor.  Commands end with ; or \g.
    Your MySQL connection id is 65233
    Server version: 5.6.39.0 MySQL Community Server (GPL)
    
    Copyright (c) 2000, 2018, Oracle and/or its affiliates. All rights reserved.
    
    Oracle is a registered trademark of Oracle Corporation and/or its
    affiliates. Other names may be trademarks of their respective
    owners.
    
    Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
    
    mysql> status
    --------------
    mysql  Ver 14.14 Distrib 5.7.23, for Linux (x86_64) using  EditLine wrapper
    
    Connection id:          65233
    Current database:
    Current user:           mysqladmin@13.76.42.93
    SSL:                    Cipher in use is AES256-SHA
    Current pager:          stdout
    Using outfile:          ''
    Using delimiter:        ;
    Server version:         5.6.39.0 MySQL Community Server (GPL)
    Protocol version:       10
    Connection:             mysqlserveransible.mysql.database.azure.com via TCP/IP
    Server characterset:    latin1
    Db     characterset:    latin1
    Client characterset:    utf8
    Conn.  characterset:    utf8
    TCP port:               3306
    Uptime:                 36 min 21 sec
    
    Threads: 5  Questions: 559  Slow queries: 0  Opens: 96  Flush tables: 3  Open tables: 10  Queries per second avg: 0.256
    --------------
    ```
    
## <a name="query-mysql-servers"></a>Consultar servidores MySQL

O código do guia estratégico nesta seção consulta servidores MySQL em `myResourceGroup` e lista os bancos de dados nos servidores encontrados.

Salve o guia estratégico a seguir como `mysql_query.yml`:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    mysqlserver_name: mysqlserveransible
  tasks:
    - name: Query MySQL Servers in current resource group
      azure_rm_mysqlserver_facts:
        resource_group: "{{ resource_group }}"
      register: mysqlserverfacts

    - name: Dump MySQL Server facts
      debug:
        var: mysqlserverfacts

    - name: Query MySQL Databases
      azure_rm_mysqldatabase_facts:
        resource_group: "{{ resource_group }}"
        server_name: "{{ mysqlserver_name }}"
      register: mysqldatabasefacts

    - name: Dump MySQL Database Facts
      debug:
        var: mysqldatabasefacts
```

Execute o guia estratégico usando o comando `ansible-playbook`:

```bash
ansible-playbook mysql_query.yml
```

Depois de executar o guia estratégico, você verá resultados semelhantes aos seguintes:

```json
"servers": [
    {
        "admin_username": "mysqladmin",
        "enforce_ssl": false,
        "fully_qualified_domain_name": "mysqlserveransible.mysql.database.azure.com",
        "id": "/subscriptions/685ba005-af8d-4b04-8f16-a7bf38b2eb5a/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/mysqlserveransible",
        "location": "eastus",
        "name": "mysqlserveransible",
        "resource_group": "myResourceGroup",
        "sku": {
            "capacity": 1,
            "family": "Gen5",
            "name": "B_Gen5_1",
            "tier": "Basic"
        },
        "storage_mb": 5120,
        "user_visible_state": "Ready",
        "version": "5.6"
    }
]
```

Você também verá o seguinte resultado para o banco de dados do MySQL:

```json
"databases": [
    {
        "charset": "utf8",
        "collation": "utf8_general_ci",
        "name": "information_schema",
        "resource_group": "myResourceGroup",
        "server_name": "mysqlserveransible"
    },
    {
        "charset": "latin1",
        "collation": "latin1_swedish_ci",
        "name": "mysql",
        "resource_group": "myResourceGroup",
        "server_name": "mysqlserveransibler"
    },
    {
        "charset": "latin1",
        "collation": "latin1_swedish_ci",
        "name": "mysqldbansible",
        "resource_group": "myResourceGroup",
        "server_name": "mysqlserveransible"
    },
    {
        "charset": "utf8",
        "collation": "utf8_general_ci",
        "name": "performance_schema",
        "resource_group": "myResourceGroup",
        "server_name": "mysqlserveransible"
    }
]
```

## <a name="clean-up-resources"></a>Limpar recursos

Quando não forem mais necessários, exclua os recursos criados neste artigo. 

Salve o guia estratégico a seguir como `cleanup.yml`:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
  tasks:
    - name: Delete a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        state: absent
```

Execute o guia estratégico usando o comando `ansible-playbook`:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"] 
> [Ansible no Azure](/azure/ansible/)