---
title: Tutorial – Configurar tópicos no Barramento de Serviço do Azure usando o Ansible
description: Saiba como usar o Ansible para criar um tópico do Barramento de Serviço do Azure
keywords: ansible, azure, devops, bash, guia estratégico, barramento de serviço, tópicos, assinaturas
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 952779db582f9437f10608bf86b0b80560ded2c0
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72241226"
---
# <a name="tutorial-configure-topics-in-azure-service-bus-using-ansible"></a>Tutorial: Configurar tópicos no Barramento de Serviço do Azure usando o Ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-servicebus.md](../../includes/open-source-devops-intro-servicebus.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Criar um tópico
> * Criar uma assinatura
> * Criar uma política de SAS
> * Recuperar informações de namespace
> * Recuperar informações de assinatura e tópico
> * Revogar uma política de SAS

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-the-service-bus-topic"></a>Criar o tópico do Barramento de Serviço

O código do guia estratégico de exemplo cria os seguintes recursos:
- Grupo de recursos do Azure
- O namespace do Barramento de Serviço no grupo de recursos
- Tópico do Barramento de Serviço com o namespace

Salve o guia estratégico a seguir como `servicebus_topic.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      location: eastus
      namespace: servicebustestns
      topic: servicebustesttopic
  tasks:
    - name: Ensure resource group exist
      azure_rm_resourcegroup:
          name: "{{ resource_group }}"
          location: "{{ location }}"
    - name: Create a namespace
      azure_rm_servicebus:
          name: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
    - name: Create a topic
      azure_rm_servicebustopic:
          name: "{{ topic }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
      register: topic
    - debug:
          var: topic
```

Execute o guia estratégico usando o comando `ansible-playbook`:

```bash
ansible-playbook servicebus_topic.yml
```

## <a name="create-the-subscription"></a>Criar a assinatura

O código do guia estratégico de exemplo cria a assinatura em um tópico do Barramento de Serviço. Os tópicos do Barramento de Serviço do Azure podem ter várias assinaturas. Um assinante de um tópico pode receber uma cópia de cada mensagem enviada para o tópico. As assinaturas são entidades nomeadas criadas de forma durável, mas podem, opcionalmente, expirar.

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      location: eastus
      namespace: servicebustestns
      topic: servicebustesttopic
      subscription: servicebustestsubs
  tasks:
    - name: Create a subscription
      azure_rm_servicebustopicsubscription:
          name: "{{ subscription }}"
          topic: "{{ topic }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
      register: subs
    - debug:
          var: subs
```

Salve o guia estratégico a seguir como `servicebus_subscription.yml`:

Execute o guia estratégico usando o comando `ansible-playbook`:

```bash
ansible-playbook servicebus_subscription.yml
```

## <a name="create-the-sas-policy"></a>Criar a política de SAS

Uma [SAS (Assinatura de Acesso Compartilhado)](/azure/storage/common/storage-dotnet-shared-access-signature-part-1) é um mecanismo de autorização baseado em declarações usando tokens. 

O código do guia estratégico de exemplo cria duas políticas de SAS para uma fila do Barramento de Serviço com privilégios diferentes.

Salve o guia estratégico a seguir como `servicebus_topic_policy.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
      topic: servicebustesttopic
  tasks:
    - name: Create a policy with send and listen privilege
      azure_rm_servicebussaspolicy:
          name: "{{ topic }}-{{ item }}"
          topic: "{{ topic }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          rights: "{{ item }}"
      with_items:
        - send
        - listen
      register: policy
    - debug:
          var: policy
```

Execute o guia estratégico usando o comando `ansible-playbook`:

```bash
ansible-playbook servicebus_topic_policy.yml
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

## <a name="retrieve-topic-and-subscription-information"></a>Recuperar informações de assinatura e tópico

O código do guia estratégico de exemplo consulta as seguintes informações:
- Informações do tópico do Barramento de Serviço
- Lista de detalhes da assinatura para o tópico
 
Salve o guia estratégico a seguir como `servicebus_list.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
      topic: servicebustesttopic
  tasks:
    - name: Get a topic's information
      azure_rm_servicebus_facts:
          type: topic
          name: "{{ topic }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          show_sas_policies: yes
      register: topic_fact
    - name: "List subscriptions under topic {{ topic }}"
      azure_rm_servicebus_facts:
          type: subscription
          topic: "{{ topic }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
      register: subs_fact
    - debug:
          var: "{{ item }}"
      with_items:
        - topic_fact.servicebuses[0]
        - subs_fact.servicebuses
```

Antes de executar o guia estratégico, confira as observações a seguir:
- O valor `show_sas_policies` indica se é necessário mostrar as políticas de SAS na fila especificada. Por padrão, o valor é definido como `False` para evitar sobrecarga de rede adicional.

Execute o guia estratégico usando o comando `ansible-playbook`:

```bash
ansible-playbook servicebus_list.yml
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
      topic: servicebustesttopic
  tasks:
    - name: Delete a policy
      azure_rm_servicebussaspolicy:
          name: "{{ topic }}-policy"
          topic: "{{ topic }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          state: absent
```

Execute o guia estratégico usando o comando `ansible-playbook`:

```bash
ansible-playbook servicebus_topic_policy_delete.yml
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
      topic: servicebustesttopic
      subscription: servicebustestsubs
  tasks:
    - name: Delete subscription
      azure_rm_servicebustopicsubscription:
          name: "{{ subscription }}"
          topic: "{{ topic }}"
          resource_group: "{{ resource_group }}"
          namespace: "{{ namespace }}"
          state: absent
    - name: Delete topic
      azure_rm_servicebustopic:
          name: "{{ topic }}"
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
> [Ansible no Azure](/azure/ansible/)