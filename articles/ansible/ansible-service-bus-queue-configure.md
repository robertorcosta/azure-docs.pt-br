---
title: Tutorial – Configurar filas no Barramento de Serviço do Azure usando o Ansible
description: Saiba como usar o Ansible para criar uma fila do Barramento de Serviço do Azure
keywords: ansible, azure, devops, bash, playbook, service bus, queue
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: a48796c2177a8b5b818553bf8aa0ff36f712d4e0
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72241389"
---
# <a name="tutorial-configure-queues-in-azure-service-bus-using-ansible"></a>Tutorial: Configurar as filas no Barramento de Serviço do Azure usando o Ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-servicebus.md](../../includes/open-source-devops-intro-servicebus.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Criar uma fila
> * Criar uma política de SAS
> * Recuperar informações de namespace
> * Recuperar informações de fila
> * Revogar a política de SAS de fila

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-the-service-bus-queue"></a>Criar uma fila do Barramento de Serviço

O código do guia estratégico de exemplo cria os seguintes recursos:
- Grupo de recursos do Azure
- O namespace do Barramento de Serviço no grupo de recursos
- Fila do Barramento de Serviço com o namespace

Salve o guia estratégico a seguir como `servicebus_queue.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      location: eastus
      namespace: servicebustestns
      queue: servicebustestqueue
  tasks:
    - name: Ensure resource group exist
      azure_rm_resourcegroup:
          name: "{{ resource_group }}"
          location: "{{ location }}"
    - name: Create a namespace
      azure_rm_servicebus:
          name: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
    - name: Create a queue
      azure_rm_servicebusqueue:
          name: "{{ queue }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
      register: queue
    - debug:
          var: queue
```

Execute o guia estratégico usando o comando `ansible-playbook`:

```bash
ansible-playbook servicebus_queue.yml
```

## <a name="create-the-sas-policy"></a>Criar a política de SAS

Uma [SAS (Assinatura de Acesso Compartilhado)](/azure/storage/common/storage-dotnet-shared-access-signature-part-1) é um mecanismo de autorização baseado em declarações usando tokens. 

O código do guia estratégico de exemplo cria duas políticas de SAS para uma fila do Barramento de Serviço com privilégios diferentes.

Salve o guia estratégico a seguir como `servicebus_queue_policy.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
      queue: servicebustestqueue
  tasks:
    - name: Create a policy with send and listen priviledge
      azure_rm_servicebussaspolicy:
          name: "{{ queue }}-policy"
          queue: "{{ queue }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          rights: listen_send
      register: policy
    - debug:
          var: policy
```

Antes de executar o guia estratégico, confira as observações a seguir:
- O valor `rights` representa o privilégio que um usuário tem com a fila. Especifique um dos seguintes valores: `manage`, `listen`, `send` ou `listen_send`.

Execute o guia estratégico usando o comando `ansible-playbook`:

```bash
ansible-playbook servicebus_queue_policy.yml
```

## <a name="retrieve-namespace-information"></a>Recuperar informações de namespace

O código do guia estratégico de exemplo consulta as informações de namespace.

Salve o guia estratégico a seguir como `servicebus_namespace_info.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
  tasks:
    - name: Get a namespace's information
      azure_rm_servicebus_facts:
          type: namespace
          name: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          show_sas_policies: yes
      register: ns
    - debug:
          var: ns
```

Antes de executar o guia estratégico, confira as observações a seguir:
- O valor `show_sas_policies` indica se é necessário mostrar as políticas de SAS no namespace especificado. Por padrão, o valor é `False` para evitar sobrecarga de rede adicional.

Execute o guia estratégico usando o comando `ansible-playbook`:

```bash
ansible-playbook servicebus_namespace_info.yml
```

## <a name="retrieve-queue-information"></a>Recuperar informações de fila

O código do guia estratégico de exemplo consulta as informações de fila. 

Salve o guia estratégico a seguir como `servicebus_queue_info.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
      queue: servicebustestqueue
  tasks:
    - name: Get a queue's information
      azure_rm_servicebus_facts:
          type: queue
          name: "{{ queue }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          show_sas_policies: yes
      register: queue
    - debug:
          var: queue
```

Antes de executar o guia estratégico, confira as observações a seguir:
- O valor `show_sas_policies` indica se é necessário mostrar as políticas de SAS na fila especificada. Por padrão, o valor é definido como `False` para evitar sobrecarga de rede adicional.

Execute o guia estratégico usando o comando `ansible-playbook`:

```bash
ansible-playbook servicebus_queue_info.yml
```

## <a name="revoke-the-queue-sas-policy"></a>Revogar a política de SAS de fila

O código do guia estratégico de exemplo exclui uma política de SAS de fila.

Salve o guia estratégico a seguir como `servicebus_queue_policy_delete.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
      queue: servicebustestqueue
  tasks:
    - name: Create a policy with send and listen priviledge
      azure_rm_servicebussaspolicy:
          name: "{{ queue }}-policy"
          queue: "{{ queue }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          state: absent
```

Execute o guia estratégico usando o comando `ansible-playbook`:

```bash
ansible-playbook servicebus_queue_policy_delete.yml
```

## <a name="clean-up-resources"></a>Limpar recursos

Quando não forem mais necessários, exclua os recursos criados neste artigo. 

Salve o seguinte código como `cleanup.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
      queue: servicebustestqueue
  tasks:
    - name: Delete queue
      azure_rm_servicebusqueue:
          name: "{{ queue }}"
          resource_group: "{{ resource_group }}"
          namespace: "{{ namespace }}"
          state: absent
    - name: Delete namespace
      azure_rm_servicebus:
          name: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          state: absent
    - name: Delete resource group
      azure_rm_resourcegroup:
          name: "{{ resource_group }}"
          state: absent
          force_delete_nonempty: yes
```

Execute o guia estratégico usando o comando `ansible-playbook`:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"] 
> [Tutorial: Configurar um tópico no Barramento de Serviço do Azure usando o Ansible](ansible-service-bus-topic-configure.md)