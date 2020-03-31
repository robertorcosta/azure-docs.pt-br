---
title: Configure a proteção do bot para o Web Application Firewall com a Porta Frontal do Azure (Visualização)
description: Aprenda waf (Web Application Firewall).
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 08/21/2019
ms.author: victorh
ms.openlocfilehash: f48b683044bc727cda461fb64a743c055188962a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76934676"
---
# <a name="configure-bot-protection-for-web-application-firewall-preview"></a>Configurar a proteção do bot para o Web Application Firewall (Preview)
Este artigo mostra como configurar a regra de proteção de bots no WaF (Azure Web Application Firewall) para front door usando o portal Azure. A regra de proteção do Bot também pode ser configurada usando o modelo CLI, Azure PowerShell ou Azure Resource Manager.

> [!IMPORTANT]
> O conjunto de regras de proteção contra bots está atualmente em versão prévia pública e é fornecido com um contrato de nível de serviço de versão prévia pública. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.  Veja os [Termos de Uso Adicionais para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter detalhes.

## <a name="prerequisites"></a>Pré-requisitos

Crie uma política waf básica para a Porta da Frente seguindo as instruções descritas em [Criar uma política WAF para a Porta Frontal do Azure usando o portal Azure](waf-front-door-create-portal.md).

## <a name="enable-bot-protection-rule-set"></a>Habilitar o conjunto de regras de proteção de bots

Na página **Regras gerenciadas** ao criar uma diretiva de firewall de aplicativos da Web, primeiro encontre a seção **'Conjunto de regras gerenciadas',** selecione a caixa de seleção na frente da regra **Microsoft_BotManager_1.0** no menu suspenso e selecione **'Revisar + Criar**''

   ![Regra de proteção de bots](.././media/waf-front-door-configure-bot-protection/botmanager112019.png)

## <a name="next-steps"></a>Próximas etapas

- Aprenda a [monitorar o WAF](waf-front-door-monitor.md).
