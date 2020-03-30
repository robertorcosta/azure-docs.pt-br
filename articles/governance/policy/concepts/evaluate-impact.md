---
title: Avalie o impacto de uma nova política do Azure
description: Entenda o processo a seguir ao introduzir uma nova definição de política no seu ambiente Azure.
ms.date: 09/23/2019
ms.topic: conceptual
ms.openlocfilehash: 562fa2378356ddc1eac48b6ea5c160ebf655d525
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74463515"
---
# <a name="evaluate-the-impact-of-a-new-azure-policy"></a>Avalie o impacto de uma nova política do Azure

O Azure Policy é uma ferramenta poderosa para gerenciar seus recursos do Azure para padrões de negócios e para atender às necessidades de conformidade. Quando pessoas, processos ou pipelines criam ou atualizam recursos, a Azure Policy analisa a solicitação. Quando o efeito de definição de diretiva é [Anexar](./effects.md#deny) ou [ImplantarIfNotExist,](./effects.md#deployifnotexists)a diretiva altera a solicitação ou adiciona a ele. Quando o efeito de definição de diretiva é [Auditoria](./effects.md#audit) ou [AuditoriaIfNotExist](./effects.md#auditifnotexists), A diretiva faz com que uma entrada de registro de atividade seja criada. E quando o efeito de definição de política é [Negar](./effects.md#deny), a política impede a criação ou alteração da solicitação.

Esses resultados são exatamente como desejado quando você sabe que a política é definida corretamente. No entanto, é importante validar uma nova política funciona como pretendido antes de permitir que ela mude ou bloqueie o trabalho. A validação deve garantir que apenas os recursos pretendidos sejam determinados como incompatíveis e que nenhum recurso compatível esteja incorretamente incluído (conhecido como _falso positivo)_ nos resultados.

A abordagem recomendada para validar uma nova definição de política é seguindo estas etapas:

- Definir firmemente sua política
- Audite seus recursos existentes
- Auditoria de solicitações de recursos novas ou atualizadas
- Implante sua política para recursos
- Monitoramento contínuo

## <a name="tightly-define-your-policy"></a>Definir firmemente sua política

É importante entender como a política de negócios é implementada como uma definição de política e a relação dos recursos do Azure com outros serviços do Azure. Esta etapa é realizada [identificando os requisitos](../tutorials/create-custom-policy-definition.md#identify-requirements) e [determinando as propriedades dos recursos.](../tutorials/create-custom-policy-definition.md#determine-resource-properties)
Mas também é importante ver além da definição estreita da sua política de negócios. Sua política diz, por exemplo, "Todas as Máquinas Virtuais devem..."? E outros serviços do Azure que fazem uso de VMs, como HDInsight ou AKS? Ao definir uma política, devemos considerar como essa política impacta recursos que são usados por outros serviços.

Por essa razão, suas definições de políticas devem ser tão bem definidas e focadas nos recursos e nas propriedades que você precisa avaliar para a conformidade possível.

## <a name="audit-existing-resources"></a>Auditoria dos recursos existentes

Antes de procurar gerenciar recursos novos ou atualizados com sua nova definição de política, é melhor ver como ele avalia um subconjunto limitado de recursos existentes, como um grupo de recursos de teste. Use o [modo](./assignment-structure.md#enforcement-mode)
de execução_Desativado_ (DoNotEnforce) em sua atribuição de diretiva para evitar que o [efeito](./effects.md) seja acionado ou entradas de registro de atividade saem sendo criadas.

Esta etapa lhe dá a chance de avaliar os resultados de conformidade da nova política sobre os recursos existentes sem afetar o fluxo de trabalho. Verifique se nenhum recurso compatível está marcado como não compatível _(falso positivo)_ e se todos os recursos que você espera não estar em conformidade são marcados corretamente.
Após o subconjunto inicial de recursos validar como esperado, expanda lentamente a avaliação para todos os recursos existentes.

Avaliar os recursos existentes dessa forma também oferece uma oportunidade para remediar recursos não conformes antes da implementação completa da nova política. Essa limpeza pode ser feita manualmente ou através de uma [tarefa de correção](../how-to/remediate-resources.md) se o efeito de definição de política for _DeployIfNotExists_.

## <a name="audit-new-or-updated-resources"></a>Audite recursos novos ou atualizados

Uma vez validada sua nova definição de política está relatando corretamente os recursos existentes, é hora de olhar para o impacto da política quando os recursos são criados ou atualizados. Se a definição da política apoiar a parametrização de efeitos, use [Auditoria](./effects.md#audit). Essa configuração permite monitorar a criação e atualização de recursos para ver se a nova definição de política desencadeia uma entrada no registro de atividade do Azure para um recurso que não está em conformidade sem afetar o trabalho ou as solicitações existentes.

Recomenda-se atualizar e criar novos recursos que correspondam à sua definição de política para ver se o efeito _Auditoria_ está sendo acionado corretamente quando esperado. Fique atento a solicitações de recursos que não devem ser impactadas pela nova definição de política que desencadeiao efeito _Auditoria._
Esses recursos impactados são outro exemplo de _falsos positivos_ e devem ser fixados na definição da política antes da implementação completa.

Caso a definição da política seja alterada nesta fase de testes, recomenda-se iniciar o processo de validação com a auditoria dos recursos existentes. Uma mudança na definição de política para um _falso positivo_ em recursos novos ou atualizados provavelmente também terá um impacto sobre os recursos existentes.

## <a name="deploy-your-policy-to-resources"></a>Implante sua política para recursos

Depois de concluir a validação de sua nova definição de política com recursos existentes e solicitações de recursos novas ou atualizadas, você inicia o processo de implementação da política. Recomenda-se criar a atribuição de política para a nova definição de política para um subconjunto de todos os recursos primeiro, como um grupo de recursos. Após validar a implantação inicial, amplie o escopo da política para níveis mais amplos e amplos, como assinaturas e grupos de gestão. Essa expansão é alcançada removendo a atribuição e criando uma nova nos escopos de destino até que seja atribuída ao escopo completo dos recursos destinados a serem cobertos pela sua nova definição de política.

Durante a implantação, se forem localizados recursos que devem ser isentos da sua nova definição de política, dirija-os de uma das seguintes maneiras:

- Atualize a definição da política para ser mais explícita para reduzir o impacto não intencional
- Alterar o escopo da atribuição de políticas (removendo e criando uma nova atribuição)
- Adicione o grupo de recursos à lista de exclusão para a atribuição de políticas

Quaisquer alterações no escopo (nível ou exclusões) devem ser totalmente validadas e comunicadas com suas organizações de segurança e conformidade para garantir que não haja lacunas na cobertura.

## <a name="monitor-your-policy-and-compliance"></a>Monitore sua política e conformidade

Implementar e atribuir sua definição de política não é o passo final. Monitore continuamente o nível de [conformidade](../how-to/get-compliance-data.md) dos recursos para a sua nova definição de política e configure [alertas e notificações apropriados do Azure Monitor](../../../azure-monitor/platform/alerts-overview.md) para quando dispositivos não compatíveis forem identificados. Também é recomendado avaliar a definição de políticas e atribuições relacionadas em uma base programada para validar a definição de política está atendendo às necessidades de política de negócios e conformidade. As políticas devem ser removidas se não forem mais necessárias. As políticas também precisam ser atualizadas de tempos em tempos à medida que os recursos subjacentes do Azure evoluem e adicionam novas propriedades e recursos.

## <a name="next-steps"></a>Próximas etapas

- Conheça a estrutura de [definição de políticas](./definition-structure.md).
- Conheça a estrutura de [atribuição de políticas.](./assignment-structure.md)
- Entenda como [criar políticas programáticas.](../how-to/programmatically-create.md)
- Saiba como [obter dados de conformidade](../how-to/get-compliance-data.md).
- Aprenda a [remediar recursos não compatíveis.](../how-to/remediate-resources.md)
- Reveja o que é um grupo de gestão com [organize seus recursos com grupos de gerenciamento do Azure.](../../management-groups/overview.md)