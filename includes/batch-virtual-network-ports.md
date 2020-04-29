---
title: incluir arquivo
description: incluir arquivo
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: ''
ms.service: batch
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 04/03/2020
ms.author: labrenne
ms.custom: include file
ms.openlocfilehash: dc08dcded6418208751edbffcb5d263db059ec01
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80657499"
---
### <a name="general-requirements"></a>Requisitos gerais

* A VNet deve estar na mesma assinatura e região da conta do Lote que você usa para criar o pool.

* O pool usando a VNet pode ter um máximo de 4.096 nós.

* A sub-rede especificada para o pool deve ter endereços IP não atribuídos suficientes para acomodar o número de VMs direcionadas para o pool, ou seja, a soma das propriedades `targetDedicatedNodes` e `targetLowPriorityNodes` do pool. Se a sub-rede não tiver endereços IP não atribuídos suficientes, o pool alocará parcialmente os nós de computação e ocorrerá um erro de redimensionamento.

* O ponto de extremidade do Armazenamento do Azure precisa ser resolvido por servidores DNS personalizados que atendem sua rede VNet. Especificamente, as URLs no formato `<account>.table.core.windows.net`, `<account>.queue.core.windows.net`, e `<account>.blob.core.windows.net` devem poder ser resolvidas.

Os requisitos da VNet adicionais diferem, dependendo do pool do Lote estar na configuração da Máquina Virtual ou na configuração dos Serviços de Nuvem. Para as novas implantações de pool em uma VNet, recomenda-se a configuração da Máquina Virtual.

### <a name="pools-in-the-virtual-machine-configuration"></a>Pools na configuração da Máquina Virtual

**VNets com Suporte** - VNets baseadas no Azure Resource Manager apenas

**ID da sub-rede** - ao especificar a sub-rede usando as APIs de Lote, use o *identificador de recursos* da sub-rede. O identificador da sub-rede está no formato:

  ```
  /subscriptions/{subscription}/resourceGroups/{group}/providers/Microsoft.Network/virtualNetworks/{network}/subnets/{subnet}
  ```

**Permissões** - verifique se suas políticas de segurança ou bloqueios no grupo de recursos ou na assinatura da VNet restringem as permissões de um usuário para gerenciar a VNet.

**Recursos de rede adicionais** - o lote aloca automaticamente os recursos de rede adicionais no grupo de recursos que contém a VNet.

> [!IMPORTANT]
>Para cada um dos nós dedicados de 50 (ou cada um dos 20 nós de baixa prioridade), o lote aloca: um NSG (grupo de segurança de rede), um endereço IP público e um balanceador de carga. Esses recursos são limitados pelas [cotas de recursos](../articles/azure-resource-manager/management/azure-subscription-service-limits.md) da assinatura. Para grandes pools, talvez seja necessário solicitar um aumento de cota para um ou mais desses recursos.

#### <a name="network-security-groups-batch-default"></a>Grupos de segurança de rede: lote padrão

A sub-rede deve permitir que a comunicação de entrada do serviço de lote seja capaz de agendar tarefas nos nós de computação e a comunicação de saída para se comunicar com o armazenamento do Azure ou outros recursos conforme a necessidade da sua carga de trabalho. Para pools na configuração de máquina virtual, o lote adiciona NSGs no nível das NICs (interfaces de rede) anexadas aos nós de computação. Esses NSGs são configurados com as seguintes regras adicionais:

* O tráfego TCP de entrada nas portas 29876 e 29877 dos endereços IP do serviço de lote que `BatchNodeManagement` correspondem à marca de serviço.
* Tráfego TCP de entrada na porta 22 (nós do Linux) ou na porta 3389 (nós do Windows) para permitir o acesso remoto. Para determinados tipos de tarefas de várias instâncias no Linux (como MPI), você também precisará permitir o tráfego da porta do SSH 22 para IPs na sub-rede que contém os nós de computação do lote. Isso pode ser bloqueado por regras de NSG no nível de sub-rede (veja abaixo).
* Tráfego de saída em qualquer porta para a rede virtual. Isso pode ser alterado por regras de NSG no nível de sub-rede (veja abaixo).
* Tráfego de saída em qualquer porta para a Internet. Isso pode ser alterado por regras de NSG no nível de sub-rede (veja abaixo).

> [!IMPORTANT]
> Tenha cuidado se você modificar ou adicionar regras de entrada ou saída nos NSGs configurados em Lote. Se a comunicação com os nós de computação na sub-rede especificada for negada por um NSG, o serviço Lote definirá o estado dos nós de computação como **inutilizável**. Além disso, nenhum bloqueio de recurso deve ser aplicado a qualquer recurso criado pelo lote; caso contrário, isso pode resultar na impedimento da limpeza de recursos como resultado de ações iniciadas pelo usuário, como a exclusão de um pool.

#### <a name="network-security-groups-specifying-subnet-level-rules"></a>Grupos de segurança de rede: especificando regras de nível de sub-rede

Não é necessário especificar NSGs no nível de sub-rede da rede virtual porque o lote configura seu próprio NSGs (veja acima). Se você tiver um NSG associado à sub-rede em que os nós de computação do lote são implantados ou quiser aplicar regras de NSG personalizadas para substituir os padrões aplicados, você deve configurar esse NSG com pelo menos as regras de segurança de entrada e saída, conforme mostrado nas tabelas a seguir.

Configure o tráfego de entrada na porta 3389 (Windows) ou 22 (Linux) somente se você precisar permitir o acesso remoto aos nós de computação de fontes externas. Talvez seja necessário habilitar as regras da porta 22 no Linux se você precisar de suporte para tarefas de várias instâncias com determinados tempos de execução do MPI. Permitir o tráfego nessas portas não é estritamente necessário para que os nós de computação do pool sejam utilizáveis.

**Regras de segurança de entrada**

| Endereços IP da fonte | Marca de serviço de origem | Portas de origem | Destino | Portas de destino | Protocolo | Ação |
| --- | --- | --- | --- | --- | --- | --- |
| N/D | `BatchNodeManagement`[Marca de serviço](../articles/virtual-network/security-overview.md#service-tags) (se estiver usando a variante regional, na mesma região que a sua conta do lote) | * | Qualquer | 29876-29877 | TCP | Allow |
| IPs de origem do usuário para acessar remotamente nós de computação e/ou sub-rede do nó de computação para tarefas de várias instâncias do Linux, se necessário. | N/D | * | Qualquer | 3389 (Windows), 22 (Linux) | TCP | Allow |

> [!WARNING]
> Os endereços IP do serviço de lote podem mudar ao longo do tempo. Portanto, é altamente recomendável utilizar a marca `BatchNodeManagement` de serviço (ou variante regional) para regras NSG. Não é recomendável popular as regras NSG com endereços IP do serviço de lote diretamente.

**Regras de segurança da saída**

| Fonte | Portas de origem | Destino | Marca de serviço de destino | Portas de destino | Protocolo | Ação |
| --- | --- | --- | --- | --- | --- | --- |
| Qualquer | * | [Marca de serviço](../articles/virtual-network/security-overview.md#service-tags) | `Storage`(se estiver usando a variante regional, na mesma região que a sua conta do lote) | 443 | TCP | Allow |

### <a name="pools-in-the-cloud-services-configuration"></a>Pools na configuração dos Serviços de Nuvem

**VNets com Suporte** - VNets clássicas apenas

**ID da sub-rede** - ao especificar a sub-rede usando as APIs de Lote, use o *identificador de recursos* da sub-rede. O identificador da sub-rede está no formato:

  ```
  /subscriptions/{subscription}/resourceGroups/{group}/providers/Microsoft.ClassicNetwork /virtualNetworks/{network}/subnets/{subnet}
  ```

**Permissões** - a `Microsoft Azure Batch` entidade de serviço deve ter a `Classic Virtual Machine Contributor` função RBAC (Controle de Acesso Baseado em Função) para a VNet especificada.

#### <a name="network-security-groups"></a>Grupos de segurança de rede

A sub-rede deve permitir a comunicação de entrada a partir do serviço de Lote para conseguir agendar as tarefas nos nós de computação e a comunicação de saída para se comunicar com o Armazenamento do Azure ou outros recursos.

Você não precisa especificar um NSG, porque o Lote configura comunicação de entrada apenas dos endereços IP de Lote para os nós do pool. No entanto, se a sub-rede especificada associou NSGs e/ou um firewall, configure as regras de segurança de entrada e saída conforme mostrado nas tabelas a seguir. Se a comunicação com os nós de computação na sub-rede especificada for negada por um NSG, o serviço Lote definirá o estado dos nós de computação como **inutilizável**.

Configure o tráfego de entrada na porta 3389 para Windows se você precisar permitir o acesso RDP aos nós do pool. Não é necessário para os nós do pool serem usados.

**Regras de segurança de entrada**

| Endereços IP da fonte | Portas de origem | Destino | Portas de destino | Protocolo | Ação |
| --- | --- | --- | --- | --- | --- |
Qualquer <br /><br />Embora isso exija efetivamente "permitir todos", o serviço do Lote aplica uma regra ACL no nível de cada nó que filtra todos os endereços IP que não são do serviço do Lote. | * | Qualquer | 10100, 20100, 30100 | TCP | Allow |
| Opcional, para permitir o acesso RDP a nós de computação. | * | Qualquer | 3389 | TCP | Allow |

**Regras de segurança da saída**

| Fonte | Portas de origem | Destino | Portas de destino | Protocolo | Ação |
| --- | --- | --- | --- | --- | --- |
| Qualquer | * | Qualquer | 443  | Qualquer | Allow |
