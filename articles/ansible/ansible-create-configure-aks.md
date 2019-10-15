---
title: Tutorial – Configurar clusters do AKS (Serviço de Kubernetes do Azure) no Azure usando o Ansible
description: Saiba como usar o Ansible para criar e gerenciar um cluster do Serviço de Kubernetes do Azure no Azure
keywords: ansible, azure, devops, bash, cloudshell, guia estratégico, aks, contêiner, aks, kubernetes
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 9b70a9c364768322a3eae6ef5b92c87b6839c540
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72242082"
---
# <a name="tutorial-configure-azure-kubernetes-service-aks-clusters-in-azure-using-ansible"></a>Tutorial: Configurar clusters do Serviço de Kubernetes do Azure (AKS) no Azure usando o Ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-aks.md](../../includes/open-source-devops-intro-aks.md)]

O AKS pode ser configurado para usar o [Azure AD (Active Directory)](/azure/active-directory/) na autenticação do usuário. Uma vez configurado, você usa seu token de autenticação do Azure AD para entrar no cluster do AKS. O RBAC pode ser baseado na identidade de um usuário ou na associação do grupo de diretórios.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Criar um cluster AKS
> * Configurar um cluster do AKS

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-managed-aks-cluster"></a>Criar um cluster do AKS gerenciado

O guia estratégico de exemplo cria um grupo de recursos e um cluster do AKS no grupo de recursos.

Salve o guia estratégico a seguir como `azure_create_aks.yml`:

```yml
- name: Create Azure Kubernetes Service
  hosts: localhost
  connection: local
  vars:
    resource_group: myResourceGroup
    location: eastus
    aks_name: myAKSCluster
    username: azureuser
    ssh_key: "your_ssh_key"
    client_id: "your_client_id"
    client_secret: "your_client_secret"
  tasks:
  - name: Create resource group
    azure_rm_resourcegroup:
      name: "{{ resource_group }}"
      location: "{{ location }}"
  - name: Create a managed Azure Container Services (AKS) cluster
    azure_rm_aks:
      name: "{{ aks_name }}"
      location: "{{ location }}"
      resource_group: "{{ resource_group }}"
      dns_prefix: "{{ aks_name }}"
      linux_profile:
        admin_username: "{{ username }}"
        ssh_key: "{{ ssh_key }}"
      service_principal:
        client_id: "{{ client_id }}"
        client_secret: "{{ client_secret }}"
      agent_pool_profiles:
        - name: default
          count: 2
          vm_size: Standard_D2_v2
      tags:
        Environment: Production
```

Antes de executar o guia estratégico, confira as observações a seguir:

- A primeira seção dentro de `tasks` define um grupo de recursos chamado `myResourceGroup` dentro no local `eastus`.
- A segunda seção dentro de `tasks` define um cluster do AKS chamado `myAKSCluster` dentro do grupo de recursos `myResourceGroup`.
- Para o espaço reservado `your_ssh_key`, insira a chave pública RSA no formato de linha única – começando com "ssh-rsa" (sem as aspas).

Execute o guia estratégico usando o comando `ansible-playbook`:

```bash
ansible-playbook azure_create_aks.yml
```

A execução do guia estratégico mostra resultados semelhantes à seguinte saída:

```Output
PLAY [Create AKS] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Create resource group] 
changed: [localhost]

TASK [Create an Azure Container Services (AKS) cluster] 
changed: [localhost]

PLAY RECAP 
localhost                  : ok=3    changed=2    unreachable=0    failed=0
```

## <a name="scale-aks-nodes"></a>Dimensionar nós do AKS

O guia estratégico de exemplo na seção anterior define dois nós. Você pode ajustar o número de nós modificando o valor `count` no bloco `agent_pool_profiles`.

Salve o guia estratégico a seguir como `azure_configure_aks.yml`:

```yml
- name: Scale AKS cluster
  hosts: localhost
  connection: local
  vars:
    resource_group: myResourceGroup
    location: eastus
    aks_name: myAKSCluster
    username: azureuser
    ssh_key: "your_ssh_key"
    client_id: "your_client_id"
    client_secret: "your_client_secret"
  tasks:
  - name: Scaling an existed AKS cluster
    azure_rm_aks:
        name: "{{ aks_name }}"
        location: "{{ location }}"
        resource_group: "{{ resource_group }}"
        dns_prefix: "{{ aks_name }}"
        linux_profile:
          admin_username: "{{ username }}"
          ssh_key: "{{ ssh_key }}"
        service_principal:
          client_id: "{{ client_id }}"
          client_secret: "{{ client_secret }}"
        agent_pool_profiles:
          - name: default
            count: 3
            vm_size: Standard_D2_v2
```

Antes de executar o guia estratégico, confira as observações a seguir:

- Para o espaço reservado `your_ssh_key`, insira a chave pública RSA no formato de linha única – começando com "ssh-rsa" (sem as aspas).

Execute o guia estratégico usando o comando `ansible-playbook`:

```bash
ansible-playbook azure_configure_aks.yml
```

A execução do guia estratégico mostra resultados semelhantes à seguinte saída:

```Output
PLAY [Scale AKS cluster] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Scaling an existed AKS cluster] 
changed: [localhost]

PLAY RECAP 
localhost                  : ok=2    changed=1    unreachable=0    failed=0
```

## <a name="delete-a-managed-aks-cluster"></a>Excluir um cluster do AKS gerenciado

O guia estratégico de exemplo exclui um cluster do AKS.

Salve o guia estratégico a seguir como `azure_delete_aks.yml`:


```yml
- name: Delete a managed Azure Container Services (AKS) cluster
  hosts: localhost
  connection: local
  vars:
    resource_group: myResourceGroup
    aks_name: myAKSCluster
  tasks:
  - name:
    azure_rm_aks:
      name: "{{ aks_name }}"
      resource_group: "{{ resource_group }}"
      state: absent
  ```

Execute o guia estratégico usando o comando `ansible-playbook`:

```bash
ansible-playbook azure_delete_aks.yml
```

A execução do guia estratégico mostra resultados semelhantes à seguinte saída:

```Output
PLAY [Delete a managed Azure Container Services (AKS) cluster] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [azure_rm_aks] 

PLAY RECAP 
localhost                  : ok=2    changed=1    unreachable=0    failed=0
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Tutorial: dimensionar o aplicativo no AKS (Serviço de Kubernetes do Azure)](/azure/aks/tutorial-kubernetes-scale)