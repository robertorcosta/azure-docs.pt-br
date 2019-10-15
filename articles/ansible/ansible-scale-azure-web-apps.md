---
title: Tutorial – Dimensionar aplicativos no Serviço de Aplicativo do Azure usando o Ansible
description: Saiba como escalar verticalmente um aplicativo no Serviço de Aplicativo do Azure
keywords: ansible, azure, devops, bash, guia estratégico, Serviço de Aplicativo do Azure, aplicativo Web, escala, Java
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 0adcddb8afa4f0e3d0b9288f351dae7d77922612
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72241482"
---
# <a name="tutorial-scale-apps-in-azure-app-service-using-ansible"></a>Tutorial: Dimensionar aplicativos no Serviço de Aplicativo do Azure usando o Ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[!INCLUDE [open-source-devops-intro-app-service.md](../../includes/open-source-devops-intro-app-service.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Obter fatos de um plano do Serviço de Aplicativo existente
> * Escalar verticalmente o plano do Serviço de Aplicativo para o S2 com três funções de trabalho

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]
- **Aplicativo do Serviço de Aplicativo do Azure** – se você não tiver um aplicativo do Serviço de Aplicativo do Azure, [configure um aplicativo no Serviço de Aplicativo do Azure usando o Ansible](ansible-create-configure-azure-web-apps.md).

## <a name="scale-up-an-app"></a>Escalar verticalmente um aplicativo

Há dois fluxos de trabalho para o dimensionamento: *escalar verticalmente* e *escalar horizontalmente*.

**Escalar verticalmente:** Escalar verticalmente significa adquirir mais recursos. Esses recursos incluem CPU, memória, espaço em disco, VMs e muito mais. Para escalar verticalmente, altere o tipo de preço do plano do Serviço de Aplicativo ao qual seu aplicativo pertence. 
**Escalar horizontalmente:** Escalar horizontalmente significa aumentar o número de instâncias de VM que executam seu aplicativo. Dependendo de seu tipo de preço do plano do Serviço de Aplicativo, você pode escalar horizontalmente até 20 instâncias. O [dimensionamento automático](/azure/azure-monitor/platform/autoscale-get-started) permite dimensionar automaticamente a contagem de instâncias com base em regras e agendamentos predefinidos.

O código do guia estratégico nesta seção define a operação a seguir:

* Obter fatos de um plano do Serviço de Aplicativo existente
* Atualizar o plano do Serviço de Aplicativo para o S2 com três funções de trabalho

Salve o guia estratégico a seguir como `webapp_scaleup.yml`:

```yml
- hosts: localhost
  connection: local
  vars:
    resource_group: myResourceGroup
    plan_name: myAppServicePlan
    location: eastus

  tasks:
  - name: Get facts of existing App service plan
    azure_rm_appserviceplan_facts:
      resource_group: "{{ resource_group }}"
      name: "{{ plan_name }}"
    register: facts

  - debug: 
      var: facts.appserviceplans[0].sku

  - name: Scale up the App service plan
    azure_rm_appserviceplan:
      resource_group: "{{ resource_group }}"
      name: "{{ plan_name }}"
      is_linux: true
      sku: S2
      number_of_workers: 3
      
  - name: Get facts
    azure_rm_appserviceplan_facts:
      resource_group: "{{ resource_group }}"
      name: "{{ plan_name }}"
    register: facts

  - debug: 
      var: facts.appserviceplans[0].sku
```

Execute o guia estratégico usando o comando `ansible-playbook`:

```bash
ansible-playbook webapp_scaleup.yml
```

Depois de executar o guia estratégico, você verá resultados semelhantes aos seguintes:

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Get facts of existing App service plan] 
 [WARNING]: Azure API profile latest does not define an entry for WebSiteManagementClient

ok: [localhost]

TASK [debug] 
ok: [localhost] => {
    "facts.appserviceplans[0].sku": {
        "capacity": 1,
        "family": "S",
        "name": "S1",
        "size": "S1",
        "tier": "Standard"
    }
}

TASK [Scale up the App service plan] 
changed: [localhost]

TASK [Get facts] 
ok: [localhost]

TASK [debug] 
ok: [localhost] => {
    "facts.appserviceplans[0].sku": {
        "capacity": 3,
        "family": "S",
        "name": "S2",
        "size": "S2",
        "tier": "Standard"
    }
}

PLAY RECAP 
localhost                  : ok=6    changed=1    unreachable=0    failed=0 
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"] 
> [Ansible no Azure](/azure/ansible/)