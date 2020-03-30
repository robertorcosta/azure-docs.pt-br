---
title: Projetar os fluxos de trabalho da Política como código
description: Aprenda a projetar fluxos de trabalho para implantar suas definições de diretiva do Azure como código e validar automaticamente os recursos.
ms.date: 11/04/2019
ms.topic: conceptual
ms.openlocfilehash: 0914ba6510c9d2ef87d3f83417f97340d42c8bce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74267274"
---
# <a name="design-policy-as-code-workflows"></a>Projetar os fluxos de trabalho da Política como código

À medida que você avança em sua jornada com a Governança em Nuvem, você vai querer passar da gestão manual de cada definição de política no portal Azure ou através dos vários SDKs para algo mais gerenciável e repetível em escala corporativa. Duas das abordagens predominantes para o gerenciamento de sistemas em escala na nuvem são:

- Infra-estrutura como Código: A prática de tratar o conteúdo que define seus ambientes, desde modelos de gerenciador de recursos até definições de política do Azure até projetos do Azure, como código-fonte.
- DevOps: A união de pessoas, processos e produtos para permitir a entrega contínua de valor aos nossos usuários finais.

Política como Código é a combinação dessas idéias. Essencialmente, mantenha suas definições de política no controle de origem e sempre que uma alteração for feita, teste e valide essa alteração. No entanto, essa não deve ser a extensão do envolvimento das políticas com infra-estrutura como Code ou DevOps.

A etapa de validação também deve ser um componente de outros fluxos de trabalho de integração contínua ou de implantação contínua. Exemplos incluem a implantação de um ambiente de aplicativos ou infra-estrutura virtual. Ao tornar a validação da Diretiva Do Azure um componente inicial do processo de compilação e implantação, as equipes de aplicativos e operações descobrem se suas alterações não são reclamadas, muito antes de ser tarde demais e estão tentando implantar na produção.

## <a name="workflow-overview"></a>Visão geral do fluxo de trabalho

O fluxo de trabalho geral recomendado da Política como Código se parece com este diagrama:

![Política como visão geral do fluxo de trabalho do código](../media/policy-as-code/policy-as-code-workflow.png)

### <a name="create-and-update-policy-definitions"></a>Criar e atualizar definições de políticas

As definições de diretiva são criadas usando JSON e armazenadas no controle de origem. Cada política tem seu próprio conjunto de arquivos, como parâmetros, regras e parâmetros de ambiente, que devem ser armazenados na mesma pasta. A estrutura a seguir é uma maneira recomendada de manter suas definições de política no controle de origem.

```text
.
|
|- policies/  ________________________ # Root folder for policies
|  |- policy1/  ______________________ # Subfolder for a policy
|     |- policy.json _________________ # Policy definition
|     |- policy.parameters.json ______ # Policy definition of parameters
|     |- policy.rules.json ___________ # Policy rule
|     |- params.dev.json _____________ # Parameters for a Dev environment
|     |- params.prd.json _____________ # Parameters for a Prod environment
|     |- params.tst.json _____________ # Parameters for a Test environment
|
|  |- policy2/  ______________________ # Subfolder for a policy
|     |- policy.json _________________ # Policy definition
|     |- policy.parameters.json ______ # Policy definition of parameters
|     |- policy.rules.json ___________ # Policy rule
|     |- params.dev.json _____________ # Parameters for a Dev environment
|     |- params.prd.json _____________ # Parameters for a Prod environment
|     |- params.tst.json _____________ # Parameters for a Test environment
|
```

Quando uma nova diretiva é adicionada ou uma existente atualizada, o fluxo de trabalho deve atualizar automaticamente a definição de diretiva no Azure. O teste da definição de política nova ou atualizada vem em uma etapa posterior.

### <a name="create-and-update-initiative-definitions"></a>Criar e atualizar definições de iniciativa

Da mesma forma, as iniciativas têm seu próprio arquivo JSON e arquivos relacionados que devem ser armazenados na mesma pasta. A definição da iniciativa exige que a definição de política já exista, portanto não pode ser criada ou atualizada até que a fonte da política tenha sido atualizada no controle de origem e, em seguida, atualizada no Azure. A estrutura a seguir é uma maneira recomendada de manter as definições de sua iniciativa no controle de origem:

```text
.
|
|- initiatives/ ______________________ # Root folder for initiatives
|  |- init1/ _________________________ # Subfolder for an initiative
|     |- policyset.json ______________ # Initiative definition
|     |- policyset.definitions.json __ # Initiative list of policies
|     |- policyset.parameters.json ___ # Initiative definition of parameters
|     |- params.dev.json _____________ # Parameters for a Dev environment
|     |- params.prd.json _____________ # Parameters for a Prod environment
|     |- params.tst.json _____________ # Parameters for a Test environment
|
|  |- init2/ _________________________ # Subfolder for an initiative
|     |- policyset.json ______________ # Initiative definition
|     |- policyset.definitions.json __ # Initiative list of policies
|     |- policyset.parameters.json ___ # Initiative definition of parameters
|     |- params.dev.json _____________ # Parameters for a Dev environment
|     |- params.prd.json _____________ # Parameters for a Prod environment
|     |- params.tst.json _____________ # Parameters for a Test environment
|
```

Como definições de políticas, ao adicionar ou atualizar uma iniciativa existente, o fluxo de trabalho deve atualizar automaticamente a definição de iniciativa no Azure. O teste da definição de iniciativa nova ou atualizada vem em uma etapa posterior.

### <a name="test-and-validate-the-updated-definition"></a>Teste e valide a definição atualizada

Uma vez que a automação tenha tomado suas definições de política ou iniciativa recém-criadas ou atualizadas e feito a atualização para o objeto no Azure, é hora de testar as alterações que foram feitas. Ou a política ou a iniciativa da da frente deve ser atribuída aos recursos do ambiente mais distantes da produção. Este ambiente é tipicamente _Dev_.

A atribuição deve usar [enforcementMode](./assignment-structure.md#enforcement-mode) of _disabled_ para que a criação de recursos e atualizações não sejam bloqueadas, mas que os recursos existentes ainda sejam auditados para conformidade com a definição de política atualizada. Mesmo com o enforcementMode, recomenda-se que o escopo de atribuição seja um grupo de recursos ou uma assinatura que seja usada especificamente para validar políticas.

> [!NOTE]
> Embora o modo de aplicação seja útil, não é um substituto para testar completamente uma definição de política várias condições. A definição de diretiva `PUT` `PATCH` deve ser testada com chamadas de API rest, recursos compatíveis e não compatíveis, e casos de borda como uma propriedade ausente do recurso.

Depois que a atribuição for implantada, use o SDK de diretiva para [obter dados de conformidade](../how-to/get-compliance-data.md) para a nova atribuição. O ambiente utilizado para testar as políticas e atribuições deve ter recursos compatíveis e não compatíveis. Como um bom teste de unidade para código, você quer testar que os recursos são como esperado e que você também não tem falsos positivos ou falsos negativos. Se você testar e validar apenas para o que você espera, pode haver impacto inesperado e não identificado da política. Para obter mais informações, consulte [Avaliar o impacto de uma nova política do Azure](./evaluate-impact.md).

### <a name="enable-remediation-tasks"></a>Habilite tarefas de remediação

Se a validação da atribuição atender às expectativas, o próximo passo é validar a remediação.
As políticas que usam [o deployIfNotou](./effects.md#deployifnotexists) ou [modificam](./effects.md#modify) podem ser transformadas em uma tarefa de remediação e corrigir recursos de um estado não conforme.

O primeiro passo para fazer isso é conceder à atribuição de políticas a atribuição de função definida na definição da política. Essa atribuição de função dá à atribuição de políticas a identidade gerenciada com direitos suficientes para tornar as alterações necessárias para tornar o recurso compatível.

Uma vez que a atribuição de diretiva tenha direitos apropriados, use o SDK de diretiva para desencadear uma tarefa de remediação contra um conjunto de recursos que são conhecidos por não estar em conformidade. Três testes devem ser concluídos contra essas tarefas remediadas antes de prosseguir:

- Valide que a tarefa de remediação concluída com sucesso
- Faça avaliação de políticas para ver se os resultados de conformidade de políticas são atualizados conforme esperado
- Executar um teste de unidade de ambiente contra os recursos diretamente para validar suas propriedades mudaram

Testar tanto os resultados atualizados da avaliação da política quanto o ambiente fornecem diretamente a confirmação de que as tarefas de remediação mudaram o esperado e que a definição da política viu a mudança de conformidade como esperado.

### <a name="update-to-enforced-assignments"></a>Atualização para atribuições impostas

Depois de todos os portões de validação concluídos, atualize a atribuição para usar **enforcementMode** of _enabled_. Essa mudança deve ser feita inicialmente no mesmo ambiente longe da produção. Uma vez que esse ambiente seja validado como o esperado, a mudança deve então ser escopo para incluir o próximo ambiente e assim por diante até que a política seja implantada nos recursos de produção.

## <a name="process-integrated-evaluations"></a>Avaliações integradas de processos

O fluxo de trabalho geral para a Política como Código é para o desenvolvimento e implantação de políticas e iniciativas para um ambiente em escala. No entanto, a avaliação de políticas deve fazer parte do processo de implantação de qualquer fluxo de trabalho que implante ou crie recursos no Azure, como a implantação de aplicativos ou a execução de modelos do Gerenciador de Recursos para criar infra-estrutura.

Nesses casos, após a implantação do aplicativo ou da infra-estrutura ser feita a uma assinatura de teste ou grupo de recursos, a avaliação da política deve ser feita para que a validação do escopo de todas as políticas e iniciativas existentes seja feita. Embora eles possam ser configurados como **enforcementMode** _desativado_ em tal ambiente, é útil saber cedo se uma implantação de aplicativo ou infra-estrutura está violando as definições de políticas precocemente. Esta avaliação de políticas deve, portanto, ser um passo nesses fluxos de trabalho e falhar implantações que criam recursos não compatíveis.

## <a name="review"></a>Revisão

Este artigo abrange o fluxo de trabalho geral para a Política como Código e também onde a avaliação de políticas deve fazer parte de outros fluxos de trabalho de implantação. Esse fluxo de trabalho pode ser usado em qualquer ambiente que suporte etapas roteirizadas e automação com base em gatilhos.

## <a name="next-steps"></a>Próximas etapas

- Conheça a estrutura de [definição de políticas](./definition-structure.md).
- Conheça a estrutura de [atribuição de políticas.](./assignment-structure.md)
- Entenda como [criar políticas programáticas.](../how-to/programmatically-create.md)
- Saiba como [obter dados de conformidade](../how-to/get-compliance-data.md).
- Aprenda a [remediar recursos não compatíveis.](../how-to/remediate-resources.md)
- Reveja o que é um grupo de gestão com [organize seus recursos com grupos de gerenciamento do Azure.](../../management-groups/overview.md)