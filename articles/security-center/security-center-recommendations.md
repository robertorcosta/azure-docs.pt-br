---
title: Recomendações de segurança na Central de Segurança do Azure
description: Este documento mostra como as recomendações na Central de Segurança do Azure ajudam a proteger os recursos do Azure e a cumprir as políticas de segurança.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 86c50c9f-eb6b-4d97-acb3-6d599c06133e
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/22/2020
ms.author: memildin
ms.openlocfilehash: c0952e0f267f9c2511a2e80fa7106958d8dd0ee5
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92791878"
---
# <a name="security-recommendations-in-azure-security-center"></a>Recomendações de segurança na Central de Segurança do Azure 
Este tópico explica como exibir e entender as recomendações na central de segurança do Azure para ajudá-lo a proteger seus recursos do Azure.


## <a name="what-are-security-recommendations"></a>O que são recomendações de segurança?

As recomendações são ações a serem executadas para proteger seus recursos.

A Central de Segurança analisa periodicamente o estado de segurança dos seus recursos do Azure para identificar possíveis vulnerabilidades na segurança. Em seguida, ela fornece recomendações sobre como corrigir essas vulnerabilidades.

Cada recomendação fornece:

- Uma descrição breve do problema
- As etapas de correção para executar a fim de implementar a recomendação
- Os recursos afetados

## <a name="monitor-recommendations"></a>Recomendações de monitor <a name="monitor-recommendations"></a>

A central de segurança analisa o estado de segurança de seus recursos para identificar possíveis vulnerabilidades. 

1. No menu da central de segurança, abra a página **recomendações** para ver as recomendações aplicáveis ao seu ambiente. As recomendações são agrupadas em controles de segurança.

      ![Recomendações agrupadas por controle de segurança](./media/security-center-recommendations/view-recommendations.png)

1. Expanda um controle e selecione uma recomendação específica para exibir a página de detalhes de recomendação.

    :::image type="content" source="./media/security-center-recommendations/recommendation-details-page.png" alt-text="Página de detalhes de recomendação." lightbox="./media/security-center-recommendations/recommendation-details-page.png":::

    A página inclui:

    1. **Aplicar** e **negar** botões em recomendações com suporte (consulte [evitar configurações incorretas com recomendações de impor/negar](prevent-misconfigurations.md))
    1. **Indicador de severidade**
    1. **Intervalo de atualização**  (quando relevante) 
    1. **Descrição** -uma breve descrição do problema
    1. **Etapas de correção** -uma descrição das etapas manuais necessárias para corrigir o problema de segurança nos recursos afetados. Para obter recomendações com ' correção rápida ', você pode selecionar **Exibir lógica de correção** antes de aplicar a correção sugerida aos seus recursos. 
    1. **Recursos afetados** -seus recursos são agrupados em guias:
        - **Recursos íntegros** – recursos relevantes que não são afetados ou nos quais você já corrigiu o problema.
        - **Recursos não íntegros** – recursos que ainda são afetados pelo problema identificado.
        - **Recursos não aplicáveis** – recursos para os quais a recomendação não pode dar uma resposta definitiva. A guia não aplicável também inclui os motivos para cada recurso. 

            :::image type="content" source="./media/security-center-recommendations/recommendations-not-applicable-reasons.png" alt-text="Página de detalhes de recomendação.":::
    1. Botões de ação para corrigir a recomendação ou disparar um aplicativo lógico.

## <a name="preview-recommendations"></a>Recomendações de visualização

As recomendações sinalizadas como **Visualização** não estão incluídas nos cálculos da sua pontuação segura.

Elas ainda deverão ser corrigidas sempre que possível, para que, quando o período de versão prévia terminar, elas contribuam para a sua classificação.

Um exemplo de recomendação de versão prévia:

:::image type="content" source="./media/secure-score-security-controls/example-of-preview-recommendation.png" alt-text="Página de detalhes de recomendação.":::
 
## <a name="next-steps"></a>Próximas etapas

Neste documento, você foi apresentado às recomendações de segurança da Central de Segurança. Para obter informações relacionadas:

- [Corrigir recomendações](security-center-remediate-recommendations.md)– saiba como configurar políticas de segurança para suas assinaturas e grupos de recursos do Azure.
- [Evitar configurações incorretas com recomendações de aplicação/negação](prevent-misconfigurations.md).
- [Automatizar respostas para gatilhos da central de segurança](workflow-automation.md)– automatizar respostas para recomendações
- [Isentar um recurso de uma recomendação](exempt-resource.md)
- [Recomendações de segurança – um guia de referência](recommendations-reference.md)