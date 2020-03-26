---
title: Início Rápido – Gerenciar Máquinas Virtuais do Linux no Azure usando o Ansible
description: Neste Início Rápido, saiba como gerenciar uma Máquina Virtual do Linux no Azure usando o Ansible
keywords: ansible, azure, devops, bash, cloudshell, playbook, bash
ms.topic: quickstart
ms.service: ansible
author: tomarchermsft
manager: gwallace
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: d94858391951aaf9387394afeb5ad2ae373fa7b5
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2020
ms.locfileid: "80239525"
---
# <a name="quickstart-manage-linux-virtual-machines-in-azure-using-ansible"></a>Início Rápido: Gerenciar Máquinas Virtuais do Linux no Azure usando o Ansible

O Ansible permite que você automatize a implantação e a configuração de recursos em seu ambiente. Neste artigo, você usará um guia estratégico do Ansible para iniciar e parar uma Máquina Virtual do Linux. 

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [open-source-devops-prereqs-azure-sub.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="stop-a-virtual-machine"></a>Parar uma máquina virtual

Nesta seção, você usará o Ansible para desalocar (parar) uma máquina virtual do Azure.

1. Entre no [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Abra o [Cloud Shell](/azure/cloud-shell/overview).

1. Crie um arquivo chamado `azure-vm-stop.yml` e abra-o no editor:

    ```bash
    code azure-vm-stop.yml
    ```

1. Cole o código de exemplo a seguir no editor:

    ```yaml
    - name: Stop Azure VM
      hosts: localhost
      connection: local
      tasks:
        - name: Stop virtual machine
          azure_rm_virtualmachine:
            resource_group: {{ resource_group_name }}
            name: {{ vm_name }}
            allocated: no
    ```

1. Substitua os espaços reservados `{{ resource_group_name }}` e `{{ vm_name }}` por seus valores.

1. Salve o arquivo e saia do editor.

1. Execute o guia estratégico usando o comando `ansible-playbook`:

    ```bash
    ansible-playbook azure-vm-stop.yml
    ```

1. Depois de executar o guia estratégico, você verá resultados semelhantes aos seguintes:

    ```bash
    PLAY [Stop Azure VM] ********************************************************

    TASK [Gathering Facts] ******************************************************
    ok: [localhost]

    TASK [Deallocate the Virtual Machine] ***************************************
    changed: [localhost]

    PLAY RECAP ******************************************************************
    localhost                  : ok=2    changed=1    unreachable=0    failed=0
    ```

## <a name="start-a-virtual-machine"></a>Iniciar uma máquina virtual

Nesta seção, você usará o Ansible para iniciar uma máquina virtual do Azure desalocada (parada).

1. Entre no [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Abra o [Cloud Shell](/azure/cloud-shell/overview).

1. Crie um arquivo chamado `azure-vm-start.yml` e abra-o no editor:

    ```bash
    code azure-vm-start.yml
    ```

1. Cole o código de exemplo a seguir no editor:

    ```yaml
    - name: Start Azure VM
      hosts: localhost
      connection: local
      tasks:
        - name: Start virtual machine
          azure_rm_virtualmachine:
            resource_group: {{ resource_group_name }}
            name: {{ vm_name }}
    ```

1. Substitua os espaços reservados `{{ resource_group_name }}` e `{{ vm_name }}` por seus valores.

1. Salve o arquivo e saia do editor.

1. Execute o guia estratégico usando o comando `ansible-playbook`:

    ```bash
    ansible-playbook azure-vm-start.yml
    ```

1. Depois de executar o guia estratégico, você verá resultados semelhantes aos seguintes:

    ```bash
    PLAY [Start Azure VM] ********************************************************

    TASK [Gathering Facts] ******************************************************
    ok: [localhost]

    TASK [Start the Virtual Machine] ********************************************
    changed: [localhost]

    PLAY RECAP ******************************************************************
    localhost                  : ok=2    changed=1    unreachable=0    failed=0
    ```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"] 
> [Tutorial: Gerenciar estoques dinâmicos do Azure usando o Ansible](./ansible-manage-azure-dynamic-inventories.md)