---
title: Isentar um recurso das recomendações de segurança da central de segurança do Azure e a pontuação segura
description: Saiba como isentar um recurso de recomendações de segurança e a pontuação segura
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 347d5ac9de164f2e96340df71fd3b1b908e607c1
ms.sourcegitcommit: 65d518d1ccdbb7b7e1b1de1c387c382edf037850
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/09/2020
ms.locfileid: "94372737"
---
# <a name="exempt-a-resource-from-recommendations-and-secure-score"></a>Isentar um recurso das recomendações e da classificação de segurança

Uma prioridade básica de cada equipe de segurança está tentando garantir que os analistas possam se concentrar nas tarefas e nos incidentes que são importantes para a organização. A central de segurança tem muitos recursos para personalizar as informações que priorizam mais e garantir que sua pontuação segura seja uma reflexão válida das decisões de segurança da sua organização. A isenção de recursos é um desses recursos.

Quando você investiga uma recomendação de segurança na central de segurança do Azure, uma das primeiras informações que você examina é a lista de recursos afetados.

Ocasionalmente, um recurso será listado de que você sente que não deve ser incluído. Ele pode ter sido corrigido por um processo não acompanhado pela Central de Segurança. Ou, talvez, sua organização tenha decidido aceitar o risco para esse recurso específico. 

Nesses casos, você pode criar uma regra de isenção e garantir que o recurso não esteja listado com os recursos não íntegros no futuro e não afete sua pontuação segura. 

O recurso será listado como não aplicável e o motivo será mostrado como "isento" com a justificativa selecionada.

## <a name="availability"></a>Disponibilidade

|Aspecto|Detalhes|
|----|:----|
|Estado da versão:|Versão Prévia<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)] |
|Preço:|Essa é uma funcionalidade de política do Azure Premium que é oferecida para clientes do Azure defender sem custo adicional. Para outros usuários, os encargos podem ser aplicados no futuro.|
|Funções e permissões necessárias:|**Proprietário da assinatura** ou **colaborador da política** para criar uma isenção<br>Para criar uma regra, você precisa de permissões para editar políticas no Azure Policy.<br>Saiba mais em [permissões de RBAC do Azure no Azure Policy](../governance/policy/overview.md#azure-rbac-permissions-in-azure-policy).|
|Nuvens:|![Sim](./media/icons/yes-icon.png) Nuvens comerciais<br>![Não](./media/icons/no-icon.png) Nacionais/soberanas (US Gov, China Gov, outros Gov)|
|||


## <a name="create-an-exemption-rule"></a>Criar uma regra de isenção

1. Na lista de recursos não íntegros, selecione o menu de reticências ("...") para o recurso que você deseja isentar.

    :::image type="content" source="./media/exempt-resource/create-exemption.png" alt-text="Criar opção de isenção no menu de contexto":::

    O painel criar isenção é aberto.

    :::image type="content" source="./media/exempt-resource/exemption-rule-options.png" alt-text="Criar painel de isenção":::

1. Insira seus critérios e selecione um critério para o motivo pelo qual esse recurso deve ser isento:
    - **Atenuado** -esse problema não é relevante para o recurso porque ele foi tratado por uma ferramenta ou processo diferente daquele que está sendo sugerido
    - **Renúncia** -aceitando o risco para este recurso
1. Clique em **Salvar**.
1. Após um tempo (pode levar até 24 horas):
    - O recurso não afeta sua pontuação segura.
    - O recurso está listado na guia **não aplicável** da página de detalhes de recomendação
    - A faixa de informações na parte superior da página de detalhes de recomendação lista o número de recursos isentos:
        
        :::image type="content" source="./media/exempt-resource/info-banner.png" alt-text="Número de recursos isentos":::

1. Para examinar os recursos isentos, abra a guia **não aplicável** .

    :::image type="content" source="./media/exempt-resource/modifying-exemption.png" alt-text="Modificando uma isenção":::

    O motivo para cada isenção é incluído na tabela (1).

    Para modificar ou excluir uma isenção, selecione o menu de reticências ("...") conforme mostrado (2).


## <a name="review-all-of-the-exemption-rules-on-your-subscription"></a>Examinar todas as regras de isenção em sua assinatura

As regras de isenção usam a política do Azure para criar uma isenção para o recurso na atribuição de política.

Você pode usar Azure Policy para acompanhar toda a isenção na página de **isenção** :

:::image type="content" source="./media/exempt-resource/policy-page-exemption.png" alt-text="Página de isenção do Azure Policy":::



## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a isentar um recurso de uma recomendação para que ele não afete sua pontuação segura. Para obter mais informações sobre a pontuação segura, consulte:

- [Pontuação segura na Central de Segurança do Azure](secure-score-security-controls.md)