---
title: Remover o Azure Sentinel | Microsoft Docs
description: Como excluir sua instância do Sentinela do Azure.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/29/2019
ms.author: yelevin
ms.openlocfilehash: 0d918de4669e1b4bac6c1775a813eb1d224f39cf
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/10/2020
ms.locfileid: "89662010"
---
# <a name="remove-azure-sentinel-from-your-workspace"></a>Remover o Azure Sentinel do seu espaço de trabalho

Se você não quiser mais usar o Azure Sentinel, este artigo explica como removê-lo do seu espaço de trabalho.

## <a name="how-to-remove-azure-sentinel"></a>Como remover o Azure Sentinel

Siga este processo para remover o Azure Sentinel do seu espaço de trabalho:

1. Vá para **Azure Sentinel**, seguido por **configurações**e selecione a guia **remover Azure Sentinel**.

1. Antes de remover o Azure Sentinel, use as caixas de seleção para nos informar por que você o está removendo.

1. Selecione **remover Azure Sentinel do seu espaço de trabalho**.
    
    ![Excluir a solução SecurityInsights](media/offboard/delete-solution.png)

## <a name="what-happens-behind-the-scenes"></a>O que acontece nos bastidores?

Quando você remove a solução, o Azure Sentinel leva até 48 horas para concluir a primeira fase do processo de exclusão.

Depois que a desconexão é identificada, o processo de remoção é iniciado.

**A configuração desses conectores foi removida:**
-   Office 365

-   AWS

-   Alertas de segurança de serviços da Microsoft: Microsoft defender para identidade (*anteriormente Azure ATP*), Microsoft Cloud App Security incluindo Cloud Discovery relatório de ti de sombra, Azure ad Identity Protection, Microsoft defender for Endpoint (*anteriormente Microsoft defender ATP*), Azure defender (anteriormente, a*central de segurança do Azure*)

-   Inteligência contra ameaças

-   Logs de segurança comuns (incluindo logs baseados em CEF, Barracuda e syslog) (se você tiver o Azure defender, esses logs continuarão a ser coletados.)

-   Eventos de segurança do Windows (se você tiver o Azure defender, esses logs continuarão a ser coletados).

Nas primeiras 48 horas, as regras de dados e analíticas (incluindo a configuração de automação em tempo real) não estarão mais acessíveis ou consultáveis no Azure Sentinel.

**Após 30 dias, esses recursos serão removidos:**

-   Incidentes (incluindo metadados de investigação)

-   Regras analíticas

-   Indicadores

Seus guias estratégicos, pastas de trabalho salvas, consultas de busca salvas e notebooks não são removidos. **Alguns podem ser interrompidos devido aos dados removidos. Você pode removê-los manualmente.**

Depois de remover o serviço, há um período de carência de 30 dias durante o qual você pode reabilitar a solução e seus dados e regras analíticas serão restaurados, mas os conectores configurados que foram desconectados devem ser reconectados.

> [!NOTE]
> Se você remover a solução, sua assinatura continuará a ser registrada com o provedor de recursos do Azure Sentinel. **Você pode removê-lo manualmente.**




## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu a remover o serviço do Azure Sentinel. Se você mudar de ideia e quiser instalá-la novamente:
- Comece [a usar a integração do Azure Sentinel](quickstart-onboard.md).
