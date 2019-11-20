---
title: Configurar a proteção de bot para WAF com a porta frontal do Azure (visualização)
description: Saiba mais sobre o WAF (firewall do aplicativo Web).
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 08/21/2019
ms.author: victorh
ms.openlocfilehash: 95660f764d28172ecb55a4952b785fea5f2aa4bb
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74186708"
---
# <a name="configure-bot-protection-for-web-application-firewall-preview"></a>Configurar a proteção de bot para o Firewall do aplicativo Web (versão prévia)
Este artigo mostra como configurar a regra de proteção de bot no firewall do aplicativo Web do Azure (WAF) para a porta frontal usando CLI do Azure, Azure PowerShell ou Azure Resource Manager modelo.

Um conjunto de regras de proteção contra bots gerenciado pode ser habilitado para que seu WAF execute ações personalizadas em solicitações de endereços IP mal-intencionados conhecidos. Os endereços IP e os domínios são originados do feed de inteligência de ameaças da Microsoft. O [Grafo de Segurança Inteligente](https://www.microsoft.com/security/operations/intelligence) potencializa a inteligência contra ameaças da Microsoft e é usado por vários serviços, incluindo a Central de Segurança do Azure.

> [!IMPORTANT]
> O conjunto de regras de proteção contra bots está atualmente em versão prévia pública e é fornecido com um contrato de nível de serviço de versão prévia pública. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.  Veja os [Termos de Uso Adicionais para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter detalhes.

## <a name="prerequisites"></a>pré-requisitos

Crie uma política de WAF básica para a porta frontal seguindo as instruções descritas em [criar uma política de WAF para a porta frontal do Azure usando o portal do Azure](waf-front-door-create-portal.md).

## <a name="enable-bot-protection-rule-set"></a>Habilitar conjunto de regras de proteção de bot

1. Na página de política básica que você criou na seção anterior, em **configurações**, clique em **regras**.
2. Na página detalhes, na seção **gerenciar regras** , no menu suspenso, marque a caixa de seleção na frente da regra **BotProtection-preview-0,1**e selecione **salvar** acima.
    
   ![Regra de proteção de bot](.././media/waf-front-door-configure-bot-protection/botprotect2.png)

## <a name="next-steps"></a>Próximas etapas

- Saiba como [monitorar o WAF](waf-front-door-monitor.md).
