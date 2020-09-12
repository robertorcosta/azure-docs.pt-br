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
ms.date: 07/29/2019
ms.author: memildin
ms.openlocfilehash: 11043d6686bd762b1c0a9827c7edb2230487cc72
ms.sourcegitcommit: 1b320bc7863707a07e98644fbaed9faa0108da97
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89595437"
---
# <a name="security-recommendations-in-azure-security-center"></a>Recomendações de segurança na Central de Segurança do Azure 
Este tópico explica como exibir e entender as recomendações na central de segurança do Azure para ajudá-lo a proteger seus recursos do Azure.

> [!NOTE]
> Este documento apresenta o serviço usando uma implantação de exemplo.  Este documento não é um guia passo a passo.
>

## <a name="what-are-security-recommendations"></a>O que são recomendações de segurança?

As recomendações são ações a serem executadas para proteger seus recursos.

A central de segurança analisa periodicamente o estado de segurança de seus recursos do Azure para identificar possíveis vulnerabilidades de segurança. Em seguida, ele fornece recomendações sobre como corrigir essas vulnerabilidades.

Cada recomendação fornece:

- Uma breve descrição do problema.
- As etapas de correção para executar a fim de implementar a recomendação.
- Os recursos afetados.

## <a name="monitor-recommendations"></a>Recomendações de monitor <a name="monitor-recommendations"></a>

A central de segurança analisa o estado de segurança de seus recursos para identificar possíveis vulnerabilidades. O bloco **recomendações** em **visão geral** mostra o número total de recomendações identificadas pela central de segurança.

![Visão geral da central de segurança](./media/security-center-recommendations/asc-overview.png)

1. Selecione o **bloco recomendações** em **visão geral**. A lista de **recomendações** é aberta.

1. As recomendações são agrupadas em controles de segurança.

      ![Recomendações agrupadas por controle de segurança](./media/security-center-recommendations/view-recommendations.png)

1. Expanda um controle e selecione uma recomendação específica para exibir a página de recomendação.

    :::image type="content" source="./media/security-center-recommendations/recommendation-details-page.png" alt-text="Página de detalhes de recomendação." lightbox="./media/security-center-recommendations/recommendation-details-page.png":::

    A página inclui:

    - **Aplicar** e **negar** botões em recomendações com suporte (consulte [evitar configurações incorretas com recomendações de impor/negar](prevent-misconfigurations.md))
    - **Indicador de severidade**
    - **Intervalo de atualização**  (quando relevante) 
    - **Descrição** -uma breve descrição do problema
    - **Etapas de correção** -uma descrição das etapas manuais necessárias para corrigir o problema de segurança nos recursos afetados. Para obter recomendações com ' correção rápida ', você pode selecionar **Exibir lógica de correção** antes de aplicar a correção sugerida aos seus recursos. 
    - **Recursos afetados** -seus recursos são agrupados em guias:
        - **Recursos íntegros** – recursos relevantes que não são afetados ou nos quais você já corrigiu o problema.
        - **Recursos não íntegros** – recursos que ainda são afetados pelo problema identificado.
        - **Recursos não aplicáveis** – recursos para os quais a recomendação não pode dar uma resposta definitiva. A guia não aplicável também inclui os motivos para cada recurso. 

            :::image type="content" source="./media/security-center-recommendations/recommendations-not-applicable-reasons.png" alt-text="Recursos não aplicáveis com motivos.":::

## <a name="preview-recommendations"></a>Recomendações de visualização

As recomendações sinalizadas como **Visualização** não estão incluídas nos cálculos da sua pontuação segura.

Eles ainda devem ser corrigidos sempre que possível, para que, quando o período de visualização terminar, eles contribuam para a sua pontuação.

Um exemplo de uma recomendação de visualização:

:::image type="content" source="./media/secure-score-security-controls/example-of-preview-recommendation.png" alt-text="Recomendação com o sinalizador de visualização":::
 
## <a name="next-steps"></a>Próximas etapas

Neste documento, você foi apresentado às recomendações de segurança da Central de Segurança. Para saber como corrigir as recomendações:

- [Corrigir recomendações](security-center-remediate-recommendations.md) – saiba como configurar políticas de segurança para suas assinaturas e grupos de recursos do Azure.
- [Evitar configurações incorretas com recomendações de aplicação/negação](prevent-misconfigurations.md).
