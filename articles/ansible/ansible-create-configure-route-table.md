---
title: Tutorial – Configurar as tabelas de rotas do Azure usando o Ansible
description: Saiba como criar, alterar e excluir tabelas de rotas usando o Ansible
keywords: ansible, azure, devops, bash, playbook, networking, route, route table
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 14753af58a179ddf4011cb29c7ed08faab62875c
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72241774"
---
# <a name="tutorial-configure-azure-route-tables-using-ansible"></a>Tutorial: Configurar as tabelas de rotas do Azure usando o Ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-28-note.md)]

O Azure roteia o tráfego automaticamente entre redes virtuais, redes locais e sub-redes do Azure. Se você precisar de mais controle sobre o roteamento do ambiente, crie uma [tabela de rotas](/azure/virtual-network/virtual-networks-udr-overview). 

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> Criar uma tabela de rotas Criar uma rede virtual e uma sub-rede Associar uma tabela de rotas a uma sub-rede Desassociar uma tabela de rotas de uma sub-rede Criar e excluir rotas Consultar uma tabela de rotas Excluir uma tabela de rotas

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-route-table"></a>Criar uma tabela de rotas

O código do guia estratégico nesta seção cria uma tabela de rotas. Para obter informações sobre os limites da tabela de rotas, confira [limites do Azure](/azure/azure-subscription-service-limits#azure-resource-manager-virtual-networking-limits). 

Salve o guia estratégico a seguir como `route_table_create.yml`:

```yml
- hosts: localhost
  vars:
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks:
    - name: Create a route table
      azure_rm_routetable:
        name: "{{ route_table_name }}"
        resource_group: "{{ resource_group }}"
```

Execute o guia estratégico usando o comando `ansible-playbook`:

```bash
ansible-playbook route_table_create.yml
```

## <a name="associate-a-route-table-to-a-subnet"></a>Associar uma tabela de rotas a uma sub-rede

O código do guia estratégico nesta seção:

* Cria uma rede virtual
* Cria uma sub-rede dentro da rede virtual
* Associa uma tabela de rotas a uma sub-rede

As tabelas de rotas não estão associadas às redes virtuais. Em vez disso, as tabelas de rotas são associadas a sub-rede de uma rede virtual.

A rede virtual e a tabela de rotas devem coexistir na mesma assinatura e localização do Azure.

As sub-redes e as tabelas de rotas têm uma relação um-para-muitos. Uma sub-rede pode ser definida sem tabelas de rotas associadas ou com apenas uma tabela de rotas. As tabelas de rotas podem ser associadas a nenhuma, apenas uma ou várias sub-redes. 

O tráfego da sub-rede é roteado com base em:

- rotas definidas nas tabelas de rotas
- [rotas padrão](/azure/virtual-network/virtual-networks-udr-overview#default)
- rotas propagadas de uma rede local

A rede virtual deve estar conectada ao gateway de rede virtual do Azure. O gateway pode ser o ExpressRoute, ou VPN se usar o BGP com um gateway de VPN.

Salve o guia estratégico a seguir como `route_table_associate.yml`:

```yml
- hosts: localhost
  vars:
    subnet_name: mySubnet
    virtual_network_name: myVirtualNetwork 
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks:
    - name: Create virtual network
      azure_rm_virtualnetwork:
        name: "{{ virtual_network_name }}"
        resource_group: "{{ resource_group }}"
        address_prefixes_cidr:
        - 10.1.0.0/16
        - 172.100.0.0/16
        dns_servers:
        - 127.0.0.1
        - 127.0.0.3
    - name: Create a subnet with route table
      azure_rm_subnet:
        name: "{{ subnet_name }}"
        virtual_network_name: "{{ virtual_network_name }}"
        resource_group: "{{ resource_group }}"
        address_prefix_cidr: "10.1.0.0/24"
        route_table: "{ route_table_name }"
```

Execute o guia estratégico usando o comando `ansible-playbook`:

```bash
ansible-playbook route_table_associate.yml
```

## <a name="dissociate-a-route-table-from-a-subnet"></a>Desassociar uma tabela de rotas de uma sub-rede

O código do guia estratégico nesta seção dissocia uma tabela de rotas de uma sub-rede.

Ao dissociar uma tabela de rotas de uma sub-rede, defina a `route_table` da sub-rede como `None`. 

Salve o guia estratégico a seguir como `route_table_dissociate.yml`:

```yml
- hosts: localhost
  vars:
    subnet_name: mySubnet
    virtual_network_name: myVirtualNetwork 
    resource_group: myResourceGroup
  tasks:
    - name: Dissociate a route table
      azure_rm_subnet:
        name: "{{ subnet_name }}"
        virtual_network_name: "{{ virtual_network_name }}"
        resource_group: "{{ resource_group }}"
        address_prefix_cidr: "10.1.0.0/24"
```

Execute o guia estratégico usando o comando `ansible-playbook`:

```bash
ansible-playbook route_table_dissociate.yml
```

## <a name="create-a-route"></a>Criar uma rota

O código do guia estratégico nesta seção cria uma rota dentro de uma tabela de rotas. 

Salve o guia estratégico a seguir como `route_create.yml`:

```yml
- hosts: localhost
  vars:
    route_name: myRoute
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks:
    - name: Create route
      azure_rm_route:
        name: "{{ route_name }}"
        resource_group: "{{ resource_group }}"
        next_hop_type: virtual_network_gateway
        address_prefix: "10.1.0.0/16"
        route_table_name: "{{ route_table_name }}"
```

Antes de executar o guia estratégico, confira as observações a seguir:

* `virtual_network_gateway` é definido como `next_hop_type`. Para saber mais sobre como o Azure escolhe as rotas, confira [Visão geral de roteamento](/azure/virtual-network/virtual-networks-udr-overview).
* `address_prefix` é definido como `10.1.0.0/16`. O prefixo não pode ser duplicado dentro da tabela de rotas.

Execute o guia estratégico usando o comando `ansible-playbook`:

```bash
ansible-playbook route_create.yml
```

## <a name="delete-a-route"></a>Excluir uma rota

O código do guia estratégico nesta seção exclui uma rota de uma tabela de rotas.

Salve o guia estratégico a seguir como `route_delete.yml`:

```yml
- hosts: localhost
  vars:
    route_name: myRoute
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks:
    - name: Remove route
      azure_rm_route:
        name: "{{ route_name }}"
        resource_group: "{{ resource_group }}"
        route_table_name: "{{ route_table_name }}"
        state: absent
```

Execute o guia estratégico usando o comando `ansible-playbook`:

```bash
ansible-playbook route_delete.yml
```

## <a name="get-route-table-information"></a>Obter informações de uma tabela de rotas

O código do guia estratégico nesta seção usa o módulo do Ansible `azure_rm_routetable_facts` para recuperar informações da tabela de rotas.

Salve o guia estratégico a seguir como `route_table_facts.yml`:

```yml
- hosts: localhost
  vars:
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks: 
    - name: Get route table information
      azure_rm_routetable_facts:
         resource_group: "{{ resource_group }}"
         name: "{{ route_table_name }}"
      register: query
    
    - debug:
         var: query.route_tables[0]
```

Execute o guia estratégico usando o comando `ansible-playbook`:

```bash
ansible-playbook route_table_facts.yml
```

## <a name="delete-a-route-table"></a>Excluir uma tabela de rotas

O código do guia estratégico nesta seção exclui uma tabela de rotas.

Quando uma tabela de rotas é excluída, todas as suas rotas também são excluídas.

Uma tabela de rotas não poderá ser excluída se estiver associada a uma sub-rede. [Desassocie a tabela de rotas de todas as sub-redes](#dissociate-a-route-table-from-a-subnet) antes de tentar excluí-la. 

Salve o guia estratégico a seguir como `route_table_delete.yml`:

```yml
- hosts: localhost
  vars:
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks:
    - name: Create a route table
      azure_rm_routetable:
        name: "{{ route_table_name }}"
        resource_group: "{{ resource_group }}"
        state: absent
```

Execute o guia estratégico usando o comando `ansible-playbook`:

```bash
ansible-playbook route_table_delete.yml
```

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"] 
> [Ansible no Azure](/azure/ansible/)