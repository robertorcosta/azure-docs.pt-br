---
title: Corrigir recomendações na central de segurança do Azure | Microsoft Docs
description: Este artigo explica como corrigir recomendações na central de segurança do Azure para proteger seus recursos e obedecer às políticas de segurança.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 8be947cc-cc86-421d-87a6-b1e23077fd50
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/08/2020
ms.author: memildin
ms.openlocfilehash: 3d4b968d0d5e091d72cf718d58b11ade68ddf8c6
ms.sourcegitcommit: 295db318df10f20ae4aa71b5b03f7fb6cba15fc3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/15/2020
ms.locfileid: "94637074"
---
# <a name="remediate-recommendations-in-azure-security-center"></a>Recomendações de correção na Central de Segurança do Azure

As recomendações fornecem sugestões sobre como proteger melhor seus recursos. Implemente uma recomendação seguindo as etapas de correção fornecidas na recomendação.

## <a name="remediation-steps"></a>Etapas de correção <a name="remediation-steps"></a>

Depois de examinar todas as recomendações, decida qual delas corrigir primeiro. Recomendamos que você use o [impacto de Pontuação segura](security-center-recommendations.md#monitor-recommendations) para ajudar a priorizar o que fazer primeiro.

1. Na lista, clique na recomendação.

1. Siga as instruções na seção **etapas de correção** . Cada recomendação tem seu próprio conjunto de instruções. A captura de tela a seguir mostra as etapas de correção para configurar aplicativos para permitir somente o tráfego por HTTPS.

    ![Detalhes da recomendação](./media/security-center-remediate-recommendations/security-center-remediate-recommendation.png)

1. Depois de concluído, uma notificação será exibida informando se a correção foi bem-sucedida.

## <a name="quick-fix-remediation"></a>Correção de correção rápida<a name="one-click"></a>

A correção rápida simplifica a correção e permite que você aumente rapidamente sua pontuação segura, melhorando a segurança do seu ambiente.

A correção rápida permite que você corrija rapidamente uma recomendação em vários recursos. 

> [!TIP]
> A correção rápida só está disponível para recomendações específicas. Para localizar as recomendações que têm a opção correção rápida, use o filtro dedicado na parte superior da lista de recomendações:
> 
> :::image type="content" source="media/security-center-remediate-recommendations/quick-fix-filter.png" alt-text="Use os filtros acima da lista de recomendações para encontrar recomendações que tenham a opção correção rápida":::

Para implementar uma correção rápida de correção:

1. Na lista de recomendações que têm a **correção rápida!** , clique na recomendação.

    [![Selecione correção rápida!](media/security-center-remediate-recommendations/security-center-one-click-fix-select.png)](media/security-center-remediate-recommendations/security-center-one-click-fix-select.png#lightbox)

1. Na guia **recursos não íntegros** , selecione os recursos em que você deseja implementar a recomendação e clique em **corrigir**.

    > [!NOTE]
    > Alguns dos recursos listados podem estar desabilitados, pois você não tem as permissões apropriadas para modificá-los.

1. Na caixa de confirmação, leia os detalhes e as implicações de correção.

    ![Correção rápida](./media/security-center-remediate-recommendations/security-center-one-click-fix-view.png)

    > [!NOTE]
    > As implicações são listadas na caixa cinza na janela **corrigir recursos** que é aberta depois de clicar em **corrigir**. Eles listam as alterações que ocorrem ao prosseguir com a correção de correção rápida.

1. Insira os parâmetros relevantes, se necessário, e aprove a correção.

    > [!NOTE]
    > Pode levar vários minutos após a correção ser concluída para ver os recursos na guia **recursos íntegros** . Para exibir as ações de correção, verifique o [log de atividades](#activity-log).

1. Depois de concluído, uma notificação será exibida informando se a correção foi bem-sucedida.

## <a name="quick-fix-remediation-logging-in-the-activity-log"></a>Log de correções de correção rápida no log de atividades <a name="activity-log"></a>

A operação de correção usa uma implantação de modelo ou chamada à API de PATCH REST para aplicar a configuração no recurso. Essas operações são registradas no [log de atividades do Azure](../azure-resource-manager/management/view-activity-logs.md).


## <a name="next-steps"></a>Próximas etapas

Neste documento, você mostrou como corrigir recomendações na central de segurança. Para saber mais sobre a central de segurança, consulte os seguintes tópicos:

* [Configurando políticas de segurança na central de segurança do Azure](tutorial-security-policy.md) -saiba como configurar políticas de segurança para suas assinaturas e grupos de recursos do Azure.
* [Monitoramento da integridade de segurança na Central de Segurança do Azure](security-center-monitoring.md): saiba como monitorar a integridade dos recursos do Azure.
