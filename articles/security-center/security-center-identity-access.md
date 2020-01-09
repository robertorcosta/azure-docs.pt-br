---
title: Monitorar identidade e acesso na Central de Segurança do Azure | Microsoft Docs
description: Aprenda a usar os recursos de identidade e acesso na Central de Segurança do Azure para monitorar a atividade de acesso e os problemas com identidade do usuário.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 9f04e730-4cfa-4078-8eec-905a443133da
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/19/2019
ms.author: memildin
ms.openlocfilehash: 6b262baddd10c9d0dff4b196b733972b97d99872
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/31/2019
ms.locfileid: "75552977"
---
# <a name="monitor-identity-and-access-preview"></a>Monitorar identidade e acesso (visualização)
Quando o Security Center identifica possíveis vulnerabilidades de segurança, ele cria recomendações que guiam você pelo processo de configuração dos controles necessários para proteger e proteger seus recursos.

Este artigo explica a página de **identidade e acesso** da seção segurança de recursos da central de segurança do Azure.

Para obter uma lista completa das recomendações que podem ser exibidas nesta página, consulte [recomendações de identidade e acesso](recommendations-reference.md#recs-identity).

> [!NOTE]
> O monitoramento de identidade e acesso está em pré-visualização e disponível apenas na camada Padrão da Central de Segurança. Confira os [Preços](security-center-pricing.md) para saber mais sobre os tipos de preço da Central de Segurança.
>

A identidade deve ser o plano de controle da sua empresa, e a proteção de identidades deve ser sua prioridade máxima. O perímetro de segurança evoluiu de um perímetro de rede para um perímetro de identidade. A segurança se torna menos sobre defender a rede e mais sobre como defender seus dados, bem como gerenciar a segurança de seus aplicativos e usuários. Hoje em dia, com mais dados e aplicativos se mudando para a nuvem, a identidade se tornou o novo perímetro.

Ao monitorar as atividades de identidade, você poderá agir de forma proativa antes que um incidente local ocorra ou agir de forma reativa para interromper uma tentativa de ataque. O painel Identity & Access fornece recomendações como:

- Habilite a MFA para contas privilegiadas em sua assinatura
- Remova contas externas com permissões de gravação da sua assinatura
- Remova contas externas privilegiadas da sua assinatura

> [!NOTE]
> Se a sua assinatura tiver mais de 600 contas, o Security Center não poderá executar as recomendações de identidade em relação à sua assinatura. As recomendações que não são executadas são listadas em "avaliações não disponíveis" abaixo.
A Central de Segurança não pode executar as recomendações de identidade em relação aos agentes de administração de um parceiro do provedor de soluções de nuvem (CSP).
>

## <a name="monitor-identity-and-access"></a>Monitorar a identidade e acesso

Abra a lista de problemas identificados de identidade e acesso selecionando **identidade & acesso** na barra lateral da central de segurança (em **recursos**) ou na página Visão geral. 

Em **Identidade e acesso**, há duas guias:

- **Visão geral**: recomendações identificadas pela Central de segurança.
- **Assinaturas**: lista de suas assinaturas e o estado de segurança atual de cada um.

[Acesso ao ![& identidade](./media/security-center-identity-access/identity-dashboard.png)](./media/security-center-identity-access/identity-dashboard.png#lightbox)

### <a name="overview-section"></a>Seção de visão geral
Sob **visão geral**, há uma lista de recomendações. A primeira coluna lista a recomendação. A segunda coluna mostra o número total de assinaturas afetadas por essa recomendação. A terceira coluna mostra a gravidade do problema.

1. Selecione uma recomendação. A janela recomendações é aberta e exibe:

   - Descrição da recomendação
   - Lista de assinaturas insalubres e saudáveis
   - Lista de recursos que não são verificados devido a uma avaliação com falha ou o recurso está sob uma assinatura em execução no nível Gratuito e não é avaliado

    [![janela de recomendações](./media/security-center-identity-access/select-subscription.png)](./media/security-center-identity-access/select-subscription.png#lightbox)

1. Selecione uma assinatura na lista para obter mais detalhes.

### <a name="subscriptions-section"></a>Seção de assinaturas
Em **Assinaturas**, há uma lista de assinaturas. A primeira coluna lista as assinaturas. A segunda coluna mostra o número total de recomendações para cada assinatura. A terceira coluna mostra a severidade dos problemas.

[guia assinaturas do ![](./media/security-center-identity-access/subscriptions.png)](./media/security-center-identity-access/subscriptions.png#lightbox)

1. Selecione uma assinatura. Abre uma exibição de resumo com três guias:

   - **Recomendações**: com base nas avaliações realizadas pela Central de segurança que falhou.
   - **Avaliações aprovadas**: lista de avaliações realizadas pela Central de Segurança que foram aprovadas.
   - **Avaliações indisponíveis**: lista de avaliações que falharam devido a um erro ou porque a assinatura tiver mais de 600 contas.

   Sob **recomendações** é uma lista das recomendações para a assinatura selecionada e a gravidade de cada recomendação.

   [![recomendações para a assinatura Select](./media/security-center-identity-access/recommendations.png)](./media/security-center-identity-access/recommendations.png#lightbox)

1. Selecione uma recomendação para obter uma descrição da recomendação, uma lista de assinaturas não íntegras e saudáveis e uma lista de recursos não verificados.

   [![descrição da recomendação](./media/security-center-identity-access/designate.png)](./media/security-center-identity-access/designate.png#lightbox)

   Em **Avaliações aprovadas**, há uma lista de avaliações aprovadas.  Gravidade dessas avaliações sempre é verde.

   [![avaliações aprovadas](./media/security-center-identity-access/passed-assessments.png)](./media/security-center-identity-access/passed-assessments.png#lightbox)

1. Selecione uma avaliação aprovada da lista para uma descrição da avaliação e uma lista de assinaturas saudáveis. Existe um separador para subscrições não íntegras que lista todas as subscrições que falharam.

   [![avaliações aprovadas](./media/security-center-identity-access/remove.png)](./media/security-center-identity-access/remove.png#lightbox)

> [!NOTE]
> Se você criou uma política de acesso condicional que exige MFA, mas tem exclusões definidas, a avaliação de recomendação do MFA da central de segurança considera a política como não compatível, pois permite que alguns usuários entrem no Azure sem MFA.

## <a name="next-steps"></a>Próximos passos
Para saber mais sobre as recomendações que se aplicam a outros tipos de recursos do Azure, consulte os seguintes artigos:

- [Protegendo suas máquinas e aplicativos na Central de segurança do Azure](security-center-virtual-machine-protection.md)
- [Protegendo sua rede na Central de Segurança do Azure](security-center-network-recommendations.md)
- [Proteger seus dados na Central de segurança do Azure e o serviço do SQL Azure](security-center-sql-service-recommendations.md)