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
ms.openlocfilehash: 408b0f020be72b8e6b10dd6c97298afda1b91360
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245298"
---
# <a name="security-recommendations-in-azure-security-center"></a>Recomendações de segurança na Central de Segurança do Azure 
Este tópico explica como visualizar e entender as recomendações no Azure Security Center para ajudá-lo a proteger seus recursos do Azure.

> [!NOTE]
> Este documento apresenta o serviço usando uma implantação de exemplo.  Este documento não é um guia passo a passo.
>

## <a name="what-are-security-recommendations"></a>O que são recomendações de segurança?

Recomendações são ações para você tomar para garantir seus recursos.

O Security Center analisa periodicamente o estado de segurança dos recursos do Azure para identificar possíveis vulnerabilidades de segurança. Em seguida, fornece recomendações sobre como removê-los.

Cada recomendação fornece:

- Uma breve descrição do que está sendo recomendado.
- As etapas de correção a serem realizadas a fim de implementar a recomendação. <!-- In some cases, Quick Fix remediation is available. -->
- Quais recursos estão precisando de você realizando a ação recomendada sobre eles.
- O **impacto do Secure Score,** que é a quantidade que o Seu Secure Score aumentará se você implementar essa recomendação.

## <a name="monitor-recommendations"></a>Monitorar recomendações<a name="monitor-recommendations"></a>

O Security Center analisa o estado de segurança de seus recursos para identificar possíveis vulnerabilidades. O **"Panorama** **das Recomendações"** mostra o número total de recomendações identificadas pelo Security Center.

![Visão geral do centro de segurança](./media/security-center-recommendations/asc-overview.png)

1. Selecione o **bloco de recomendações** em visão **geral**. A lista **de recomendações** é aberta.

      ![Exibir recomendações](./media/security-center-recommendations/view-recommendations.png)

    Você pode filtrar as recomendações. Para filtrar as recomendações, selecione **Filtro** na folha **Recomendações**. A folha **Filtro** é aberta e você seleciona os valores de gravidade e de estado que deseja ver.

   * **RECOMENDAÇÕES**: A recomendação.
   * **IMPACTO DE PONTUAÇÃO SEGURA**: Uma pontuação gerada pelo Security Center usando suas recomendações de segurança e aplicando algoritmos avançados para determinar o quão crucial cada recomendação é. Para obter mais informações, consulte [o cálculo Secure Score](security-center-secure-score.md#secure-score-calculation).
   * **RECURSO**: lista os recursos aos quais essa recomendação se aplica.
   * **BARRAS DE STATUS**: Descreve a gravidade dessa recomendação específica:
       * **Alto (Vermelho)**: Existe uma vulnerabilidade com um recurso significativo (como um aplicativo, uma VM ou um grupo de segurança de rede) e requer atenção.
       * **Médio (Laranja)**: Existe uma vulnerabilidade e são necessárias etapas não críticas ou adicionais para eliminá-la ou concluir um processo.
       * **Baixa (Azul)**: Existe uma vulnerabilidade que deve ser tratada, mas não requer atenção imediata. (Por padrão, não são apresentadas recomendações baixas, mas você pode filtrar as recomendações baixas caso deseje vê-las.) 
       * **Saudável (Verde):**
       * **Não disponível (Cinza):**

1. Para ver os detalhes de cada recomendação, clique na recomendação.

    ![Detalhes da recomendação](./media/security-center-recommendations/recommendation-details.png)

>[!NOTE] 
> Consulte [modelos clássicos e de implantação do Gerenciador de recursos](../azure-classic-rm.md) para os recursos do Azure.
 
## <a name="next-steps"></a>Próximas etapas

Neste documento, você foi apresentado às recomendações de segurança da Central de Segurança. Para aprender a remediar as recomendações:

* [Remediar recomendações](security-center-remediate-recommendations.md) — Saiba como configurar políticas de segurança para suas assinaturas e grupos de recursos do Azure.
