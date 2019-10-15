---
title: Tutorial – Dimensionar automaticamente Conjuntos de Dimensionamento de Máquinas Virtuais no Azure usando o Ansible
description: Saiba como usar o Ansible para dimensionar conjuntos de dimensionamento de máquinas virtuais com o dimensionamento automático no Azure
keywords: ansible, azure, devops, bash, guia estratégico, dimensionar, dimensionamento automático, máquina virtual, conjunto de dimensionamento de máquinas virtuais, vmss
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 784cb532c11b16c820336ceeaf8d38f0225c832f
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72242103"
---
# <a name="tutorial-autoscale-virtual-machine-scale-sets-in-azure-using-ansible"></a>Tutorial: Dimensionar automaticamente conjuntos de dimensionamento de máquinas virtuais no Azure usando o Ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[!INCLUDE [open-source-devops-intro-vmss.md](../../includes/open-source-devops-intro-vmss.md)]

O recurso de ajustar automaticamente o número de instâncias de VM é chamado [dimensionamento automático](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview). A vantagem do dimensionamento automático é que ele reduz a sobrecarga de gerenciamento para monitorar e otimizar o desempenho do aplicativo. O dimensionamento automático pode ser configurado para atender a demanda ou em um agendamento definido. Com o Ansible, é possível especificar as regras de dimensionamento automático que definem o desempenho aceitável para uma experiência de cliente positiva.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Definir um perfil de autoescala
> * Dimensionamento automático baseado em agendamento recorrente
> * Dimensionamento automático baseado no desempenho do aplicativo
> * Recuperar informações de configurações de dimensionamento automático 
> * Desabilitar uma configuração de dimensionamento automático

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)] 
[!INCLUDE [ansible-prereqs-vm-scale-set.md](../../includes/ansible-prereqs-vm-scale-set.md)]

## <a name="autoscale-based-on-a-schedule"></a>Dimensionamento automático baseado em agendamento

Para habilitar o dimensionamento automático em um conjunto de dimensionamento, primeiro você define um perfil de dimensionamento automático. Esse perfil define a capacidade padrão, mínima e máxima do conjunto de dimensionamento. Esses limites permitem controlar o custo ao não criar instâncias de VM de forma contínua e equilibra o desempenho aceitável com um número mínimo de instâncias que permanecem em um evento de redução. 

O Ansible permite dimensionar seus conjuntos de dimensionamento em uma data específica ou agendamento recorrente.

O guia estratégico de exemplo nesta seção aumenta o número de instâncias de VM para três, às 10:00, todas as segundas-feiras.

Salve o guia estratégico a seguir como `vmss-auto-scale.yml`:

```yml
---
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    vmss_name: myScaleSet
    name: autoscalesetting
  tasks: 
    - name: Create autoscaling
      azure_rm_autoscale:
         resource_group: "{{ resource_group }}"
         name: "{{ name }}"
         target:
           namespace: "Microsoft.Compute"
           types: "virtualMachineScaleSets"
           name: "{{ vmss_name }}"
         enabled: true
         profiles:
         - count: '3'
           min_count: '3'
           max_count: '3'
           name: Auto created scale condition
           recurrence_timezone: Pacific Standard Time
           recurrence_frequency: Week
           recurrence_days:
              - Monday
           recurrence_mins:
              - '0'
           recurrence_hours:
              - '10'
```

Execute o guia estratégico usando o comando `ansible-playbook`:

```bash
ansible-playbook vmss-auto-scale.yml
```

## <a name="autoscale-based-on-performance-data"></a>Dimensionamento automático com base em dados de desempenho

Se a demanda do seu aplicativo aumentar, a carga nas instâncias de VM em seus conjuntos de dimensionamento aumentará. Se esse aumento de carga for consistente, em vez de apenas uma demanda breve, configure as regras de dimensionamento automático para aumentar o número de instâncias de VM no conjunto de dimensionamento. Quando essas instâncias de VM forem criadas e os aplicativos implantados, o conjunto de dimensionamento começará a distribuir o tráfego para eles por meio do balanceador de carga. O Ansible permite controlar quais métricas monitorar, como uso da CPU, uso do disco e tempo de carregamento do aplicativo. É possível expandir e reduzir horizontalmente conjuntos de dimensionamento com base nos limites de métrica de desempenho em um agendamento recorrente ou até determinada data. 

O código do guia estratégico nesta seção verifica a carga de trabalho da CPU dos 10 minutos anteriores, às 18:00, todas as segundas-feiras. 

Com base nas métricas de porcentagem da CPU, o guia estratégico realiza uma das ações a seguir:

- Escalar horizontalmente o número de instâncias de VM para quatro
- Reduzir horizontalmente o número de instâncias de VM para uma

Salve o guia estratégico a seguir como `vmss-auto-scale-metrics.yml`:

```yml
---
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    vmss_name: myScaleSet
    name: autoscalesetting
  tasks:
  - name: Get facts of the resource group
    azure_rm_resourcegroup_facts:
      name: "{{ resource_group }}"
    register: rg

  - name: Get scale set resource uri
    set_fact:
      vmss_id: "{{ rg.ansible_facts.azure_resourcegroups[0].id }}/providers/Microsoft.Compute/virtualMachineScaleSets/{{ vmss_name }}"
    
  - name: Create autoscaling
    azure_rm_autoscale:
      resource_group: "{{ resource_group }}"
      name: "{{ name }}"
      target: "{{ vmss_id }}"
      enabled: true
      profiles:
      - count: '1'
        max_count: '1'
        min_count: '1'
        name: 'This scale condition is executed when none of the other scale condition(s) match'
        recurrence_days:
          - Monday
        recurrence_frequency: Week
        recurrence_hours:
          - 18
        recurrence_mins:
          - 0
        recurrence_timezone: Pacific Standard Time
      - count: '1'
        min_count: '1'
        max_count: '4'
        name: Auto created scale condition
        recurrence_days:
          - Monday
        recurrence_frequency: Week
        recurrence_hours:
          - 18
        recurrence_mins:
          - 0
        recurrence_timezone: Pacific Standard Time
        rules:
          - cooldown: 5
            direction: Increase
            metric_name: Percentage CPU
            metric_resource_uri: "{{ vmss_id }}"
            operator: GreaterThan
            statistic: Average
            threshold: 70
            time_aggregation: Average
            time_grain: 1
            time_window: 10
            type: ChangeCount
            value: '1'
          - cooldown: 5
            direction: Decrease
            metric_name: Percentage CPU
            metric_resource_uri: "{{ vmss_id }}"
            operator: LessThan
            statistic: Average
            threshold: 30
            time_aggregation: Average
            time_grain: 1
            time_window: 10
            type: ChangeCount
            value: '1'
```

Execute o guia estratégico usando o comando `ansible-playbook`:

```bash
ansible-playbook vmss-auto-scale-metrics.yml
```

## <a name="get-autoscale-settings-information"></a>Recuperar informações de configurações de dimensionamento automático 

O código do guia estratégico nesta seção usa o módulo `azure_rm_autoscale_facts` para recuperar os detalhes da configuração do dimensionamento automático.

Salve o guia estratégico a seguir como `vmss-auto-scale-get-settings.yml`:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    name: autoscalesetting
  tasks: 
    - name: Retrieve autoscale settings information
      azure_rm_autoscale_facts:
        resource_group: "{{ resource_group }}"
        name: "{{ name }}"
      register: autoscale_query
    
    - debug:
        var: autoscale_query.autoscales[0]
```

Execute o guia estratégico usando o comando `ansible-playbook`:

```bash
ansible-playbook vmss-auto-scale-get-settings.yml
```

## <a name="disable-autoscale-settings"></a>Desabilitar configurações de dimensionamento automático

Há duas maneiras de desabilitar as configurações de dimensionamento automático. Uma delas é alterando a chave `enabled` de `true` para `false`. A outra é excluindo a configuração.

O código do guia estratégico nesta seção exclui a configuração do dimensionamento automático. 

Salve o guia estratégico a seguir como `vmss-auto-scale-delete-setting.yml`:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    name: autoscalesetting
  tasks: 
    - name: Delete autoscaling
      azure_rm_autoscale:
         resource_group: "{{ resource_group }}"
         name: "{{ name }}"
         state: absent
```

Execute o guia estratégico usando o comando `ansible-playbook`:

```bash
vmss-auto-scale-delete-setting.yml
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"] 
> [Tutorial: Atualizar a imagem personalizada dos conjuntos de dimensionamento de máquinas virtuais do Azure usando o Ansible](./ansible-vmss-update-image.md)