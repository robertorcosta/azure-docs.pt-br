---
author: memildin
ms.author: memildin
manager: rkarlin
ms.date: 02/24/2020
ms.topic: include
ms.openlocfilehash: c77849b2285283a34e6adf84dc3845a4076407af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77597926"
---
## <a name="attack-scenario"></a>Cenário de ataque

Ataques de força bruta geralmente se destinam às portas de gerenciamento como um meio para obter acesso a uma VM. Se for bem-sucedido, um invasor poderá assumir o controle sobre a VM e estabelecer uma base em seu ambiente.

Uma maneira de reduzir a exposição a um ataque de força bruta é limitar a quantidade de tempo que uma porta fica aberta. Os portos de gestão não precisam estar abertos o tempo todo. Eles só precisam estar abertos enquanto você estiver conectado à VM, por exemplo, para executar tarefas de gerenciamento ou manutenção. Quando o just-in-time é ativado, o Security Center usa as regras do Grupo de Segurança de [Rede](../articles/virtual-network/security-overview.md#security-rules) (NSG) e do Firewall Do Azure, que restringem o acesso às portas de gerenciamento para que não possam ser alvo dos invasores.

![Cenário JIT](../articles/security-center/media/security-center-just-in-time/just-in-time-scenario.png)

## <a name="how-does-jit-access-work"></a>Como funciona o acesso JIT?

Quando o JIT está habilitado, a Central de Segurança bloqueia o tráfego de entrada às suas VMs do Azure, criando uma regra de NSG. Você seleciona as portas na VM para as quais o tráfego de entrada será bloqueado. Essas portas são controladas pela solução Just-In-Time.

Quando um usuário solicita acesso a uma VM, o Security Center verifica se o usuário tem permissões [RBAC (Role-Based Access Control, controle de acesso baseado em função)](../articles/role-based-access-control/role-assignments-portal.md) para essa VM. Se a solicitação for aprovada, o Security Center configura automaticamente os NSGs (Network Security Groups) e o Azure Firewall para permitir o tráfego de entrada nas portas ou intervalos IP de origem solicitados, pelo tempo especificado. Depois que o tempo expirar, a Central de Segurança restaura os NSGs aos seus estados anteriores. No entanto, as conexões já estabelecidas não estão sendo interrompidas.

 > [!NOTE]
 > Se uma solicitação de acesso JIT for aprovada para uma VM por trás de um Firewall Do Azure, o Security Center altera automaticamente as regras de política de NSG e firewall. Durante o tempo especificado, as regras permitem o tráfego de entrada nas portas selecionadas e endereços OU intervalos IP de origem solicitados. Após o fim do tempo, o Security Center restaura as regras de firewall e NSG para seus estados anteriores.


## <a name="permissions-needed-to-configure-and-use-jit"></a>Permissões necessárias para configurar e usar o JIT

| Para habilitar um usuário para: | Permissões para definir|
| --- | --- |
| Configure ou edite uma diretiva JIT para uma VM | *Atribua essas ações à função:*  <ul><li>No escopo de uma assinatura ou grupo de recursos associado à VM:<br/> `Microsoft.Security/locations/jitNetworkAccessPolicies/write` </li><li> No escopo de uma assinatura ou grupo de recursos da VM: <br/>`Microsoft.Compute/virtualMachines/write`</li></ul> | 
|Solicitar acesso JIT a uma VM | *Atribua essas ações ao usuário:*  <ul><li>No escopo de uma assinatura ou grupo de recursos associado à VM:<br/>  `Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action` </li><li>No escopo de uma assinatura ou grupo de recursos associado à VM:<br/>  `Microsoft.Security/locations/jitNetworkAccessPolicies/*/read` </li><li>  No escopo de uma assinatura ou grupo de recursos ou VM:<br/> `Microsoft.Compute/virtualMachines/read` </li><li>  No escopo de uma assinatura ou grupo de recursos ou VM:<br/> `Microsoft.Network/networkInterfaces/*/read` </li></ul>|