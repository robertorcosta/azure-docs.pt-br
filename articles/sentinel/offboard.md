---
title: Remover O Azure Sentinel| Microsoft Docs
description: Como excluir sua instância do Azure Sentinel.
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
ms.openlocfilehash: 4042f7b9d0f6fdf293f338c89d5783fe266c4edf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77581677"
---
# <a name="remove-azure-sentinel-from-your-workspace"></a>Remova o Azure Sentinel do seu espaço de trabalho

Se você não quiser mais usar o Azure Sentinel, este artigo explica como removê-lo do seu espaço de trabalho.

## <a name="how-to-remove-azure-sentinel"></a>Como remover o Azure Sentinel

Siga este processo para remover o Azure Sentinel do seu espaço de trabalho:

1. Vá para **O Azure Sentinel**, seguido por **Configurações,** e selecione a guia **Remover O Zure Sentinel**.

1. Antes de remover o Azure Sentinel, use as caixas de seleção para nos informar por que você está removendo.a.

1. Selecione **Remover o Azure Sentinel do seu espaço de trabalho**.
    
    ![Exclua a solução SecurityInsights](media/offboard/delete-solution.png)

## <a name="what-happens-behind-the-scenes"></a>O que acontece nos bastidores?

Quando você remove a solução, o Azure Sentinel leva até 48 horas para concluir a primeira fase do processo de exclusão.

Depois que a desconexão é identificada, o processo de offboarding começa.

**A configuração desses conectores é removida:**
-   Office 365

-   AWS

-   Alertas de segurança de serviços da Microsoft (Azure ATP, Microsoft Cloud App Security, incluindo relatórios de TI do Cloud Discovery Shadow, Azure AD Identity Protection, Microsoft Defender ATP, Azure Security Center)

-   Inteligência contra ameaças

-   Logs de segurança comuns (incluindo logs baseados em CEF, Barracuda e Syslog) (Se você tiver o Azure Security Center, esses registros continuarão a ser coletados.)

-   Eventos de segurança do Windows (Se você tiver o Azure Security Center, esses logs continuarão a ser coletados.)

Nas primeiras 48 horas, os dados e as regras analíticas (incluindo a configuração de automação em tempo real) não estarão mais acessíveis ou consultados no Azure Sentinel.

**Após 30 dias, esses recursos são removidos:**

-   Incidentes (incluindo metadados de investigação)

-   Regras analíticas

-   Indicadores

Seus livros de jogo, livros de trabalho salvos, consultas de caça salvas e cadernos não são removidos. **Alguns podem quebrar devido aos dados removidos. Você pode removê-los manualmente.**

Depois de remover o serviço, há um período de carência de 30 dias durante o qual você pode reativar a solução e seus dados e regras analíticas serão restaurados, mas os conectores configurados que foram desconectados devem ser reconectados.

> [!NOTE]
> Se você remover a solução, sua assinatura continuará sendo registrada no provedor de recursos do Azure Sentinel. **Você pode removê-lo manualmente.**




## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu como remover o serviço Do Azure Sentinel. Se você mudar de idéia e quiser instalá-la novamente:
- Comece a [embarcar no Azure Sentinel](quickstart-onboard.md).
