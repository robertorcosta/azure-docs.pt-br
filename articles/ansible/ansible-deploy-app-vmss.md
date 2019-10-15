---
title: Tutorial – Implantar aplicativos nos Conjuntos de Dimensionamento de Máquinas Virtuais no Azure usando o Ansible
description: Saiba como usar o Ansible para configurar um conjunto de dimensionamento de máquinas virtuais no Azure e implantar aplicativos nesse conjunto de dimensionamento
keywords: ansible, azure, devops, bash, guia estratégico, máquina virtual, conjunto de dimensionamento de máquinas virtuais, vmss
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: f9035259d466a50b83fe0094d43bc0fe985e8c4e
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72241741"
---
# <a name="tutorial-deploy-apps-to-virtual-machine-scale-sets-in-azure-using-ansible"></a>Tutorial: Implantar aplicativos nos conjuntos de dimensionamento de máquinas virtuais no Azure usando o Ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[!INCLUDE [open-source-devops-intro-vmss.md](../../includes/open-source-devops-intro-vmss.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Recuperar informações de host para um grupo de VMs do Azure
> * Clonar e criar o aplicativo de exemplo
> * Instalar o JRE (Java Runtime Environment) em um conjunto de dimensionamento
> * Implantar o aplicativo Java em um conjunto de dimensionamento

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)] 
[!INCLUDE [ansible-prereqs-vm-scale-set.md](../../includes/ansible-prereqs-vm-scale-set.md)]
- **git** - O [git](https://git-scm.com) é usado para baixar um exemplo de Java usado neste tutorial.
- **JDK (Java SE Development Kit)** – o [JDK](https://aka.ms/azure-jdks) é usado para compilar o projeto Java de exemplo.
- **Apache Maven** - [Apache Maven](https://maven.apache.org/download.cgi) é usado para compilar o projeto Java de exemplo.

## <a name="get-host-information"></a>Obter informações do host

O código do guia estratégico nesta seção recupera as informações do host de um grupo de máquinas virtuais. O código obtém os endereços IP públicos e o balanceador de carga dentro de um grupo de recursos especificado e cria um grupo de hosts chamado `scalesethosts` no inventário.

Salve o guia estratégico de exemplo a seguir como `get-hosts-tasks.yml`:

  ```yml
  - name: Get facts for all Public IPs within a resource groups
    azure_rm_publicipaddress_facts:
      resource_group: "{{ resource_group }}"
    register: output_ip_address

  - name: Get loadbalancer info
    azure_rm_loadbalancer_facts:
      resource_group: "{{ resource_group }}"
      name: "{{ loadbalancer_name }}"
    register: output

  - name: Add all hosts
    add_host:
      groups: scalesethosts
      hostname: "{{ output_ip_address.ansible_facts.azure_publicipaddresses[0].properties.ipAddress }}_{{ item.properties.frontendPort }}"
      ansible_host: "{{ output_ip_address.ansible_facts.azure_publicipaddresses[0].properties.ipAddress }}"
      ansible_port: "{{ item.properties.frontendPort }}"
      ansible_ssh_user: "{{ admin_username }}"
      ansible_ssh_pass: "{{ admin_password }}"
    with_items:
      - "{{ output.ansible_facts.azure_loadbalancers[0].properties.inboundNatRules }}"
  ```

## <a name="prepare-an-application-for-deployment"></a>Preparar um aplicativo para implantação

O código do guia estratégico nesta seção usa o `git` para clonar um projeto de exemplo do Java do GitHub e compila o projeto. 

Salve o guia estratégico a seguir como `app.yml`:

  ```yml
  - hosts: localhost
    vars:
      repo_url: https://github.com/spring-guides/gs-spring-boot.git
      workspace: ~/src/helloworld

    tasks:
    - name: Git Clone sample app
      git:
        repo: "{{ repo_url }}"
        dest: "{{ workspace }}"

    - name: Build sample app
      shell: mvn package chdir="{{ workspace }}/complete"
  ```

Execute o guia estratégico de exemplo do Ansible com o seguinte comando:

  ```bash
  ansible-playbook app.yml
  ```

Depois de executar o guia estratégico, você verá resultados semelhantes aos seguintes:

  ```Output
  PLAY [localhost] 

  TASK [Gathering Facts] 
  ok: [localhost]

  TASK [Git Clone sample app] 
  changed: [localhost]

  TASK [Build sample app] 
  changed: [localhost]

  PLAY RECAP 
  localhost                  : ok=3    changed=2    unreachable=0    failed=0

  ```

## <a name="deploy-the-application-to-a-scale-set"></a>Implantar o aplicativo em um conjunto de dimensionamento

O código do guia estratégico nesta seção é usado para:

* Instalar o JRE em um grupo de hosts chamado `saclesethosts`
* Implantar o aplicativo Java em um grupo de hosts chamado `saclesethosts`

Há duas maneiras de obter o guia estratégico de exemplo:

* [Baixe o guia estratégico](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss/vmss-setup-deploy.yml) e salve-o em `vmss-setup-deploy.yml`.
* Crie um novo arquivo chamado `vmss-setup-deploy.yml` e copie-o para o conteúdo a seguir:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    scaleset_name: myScaleSet
    loadbalancer_name: myScaleSetLb
    admin_username: azureuser
    admin_password: "{{ admin_password }}"
  tasks:
  - include: get-hosts-tasks.yml

- name: Install JRE on a scale set
  hosts: scalesethosts
  become: yes
  vars:
    workspace: ~/src/helloworld
    admin_username: azureuser

  tasks:
  - name: Install JRE
    apt:
      name: default-jre
      update_cache: yes

  - name: Copy app to Azure VM
    copy:
      src: "{{ workspace }}/complete/target/gs-spring-boot-0.1.0.jar"
      dest: "/home/{{ admin_username }}/helloworld.jar"
      force: yes
      mode: 0755

  - name: Start the application
    shell: java -jar "/home/{{ admin_username }}/helloworld.jar" >/dev/null 2>&1 &
    async: 5000
    poll: 0
```

Antes de executar o guia estratégico, confira as observações a seguir:

* Na seção `vars`, substitua o espaço reservado `{{ admin_password }}` pela sua própria senha.
* Para usar o tipo de conexão ssh com senhas, instale o programa sshpass:

    Ubuntu:

    ```bash
    apt-get install sshpass
    ```

    CentOS:

    ```bash
    yum install sshpass
    ```

* Em alguns ambientes, você poderá ver um erro sobre o uso de uma senha SSH em vez de uma chave. Se você receber esse erro, poderá desabilitar a verificação de chave de host adicionando a seguinte linha a `/etc/ansible/ansible.cfg` ou `~/.ansible.cfg`:

    ```bash
    [defaults]
    host_key_checking = False
    ```

Execute o guia estratégico com o comando a seguir:

  ```bash
  ansible-playbook vmss-setup-deploy.yml
  ```

A saída resultante da execução do comando ansible-playbook indica que o aplicativo Java de exemplo foi instalado no grupo de hosts do conjunto de dimensionamento:

  ```Output
  PLAY [localhost]

  TASK [Gathering Facts]
  ok: [localhost]

  TASK [Get facts for all Public IPs within a resource groups]
  ok: [localhost]

  TASK [Get loadbalancer info]
  ok: [localhost]

  TASK [Add all hosts]
  changed: [localhost] ...

  PLAY [Install JRE on scale set]

  TASK [Gathering Facts]
  ok: [40.114.30.145_50000]
  ok: [40.114.30.145_50003]

  TASK [Copy app to Azure VM]
  changed: [40.114.30.145_50003]
  changed: [40.114.30.145_50000]

  TASK [Start the application]
  changed: [40.114.30.145_50000]
  changed: [40.114.30.145_50003]

  PLAY RECAP
  40.114.30.145_50000        : ok=4    changed=3    unreachable=0    failed=0
  40.114.30.145_50003        : ok=4    changed=3    unreachable=0    failed=0
  localhost                  : ok=4    changed=1    unreachable=0    failed=0
  ```

## <a name="verify-the-results"></a>Verifique os resultados

Para verificar os resultados de seu trabalho, navegue até a URL do balanceador de carga de seu conjunto de dimensionamento:

![Aplicativo Java em execução em um conjunto de dimensionamento no Azure.](media/ansible-vmss-deploy/ansible-deploy-app-vmss.png)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Tutorial: Dimensionar automaticamente conjuntos de dimensionamento de máquinas virtuais no Azure usando o Ansible](./ansible-auto-scale-vmss.md)