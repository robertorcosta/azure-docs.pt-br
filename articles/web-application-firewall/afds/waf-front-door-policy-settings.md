---
title: Configurações de diretiva para firewall de aplicativos web com porta frontal do Azure
description: Aprenda waf (Web Application Firewall).
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 08/21/2019
ms.author: victorh
ms.openlocfilehash: 08b21ccd7f7958f00546583f680ecb8cde4a20c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75932609"
---
# <a name="policy-settings-for-web-application-firewall-on-azure-front-door"></a>Configurações de diretiva para firewall de aplicativos web na porta frontal do Azure

Uma política waf (Web Application Firewall, firewall de aplicativos da Web) permite controlar o acesso aos seus aplicativos web por um conjunto de regras personalizadas e gerenciadas. O nome da política waf deve ser único. Você receberá um erro de validação se tentar usar um nome existente. Existem várias configurações de nível de política que se aplicam a todas as regras especificadas para essa política, conforme descrito neste artigo.

## <a name="waf-state"></a>WAF

Uma política waf para porta da frente pode estar em um dos dois estados seguintes:
- **Ativado:** Quando uma política é habilitada, o WAF está inspecionando ativamente as solicitações recebidas e toma ações correspondentes de acordo com as definições das regras
- **Desativado:** - Quando uma política é desativada, a inspeção waf é pausada. As solicitações recebidas contornarão o WAF e serão enviadas para back-ends com base no roteamento front door.

## <a name="waf-mode"></a>Modo WAF

A política de WAF pode ser configurada para ser executada nos dois modos a seguir:

- **Modo de detecção** Quando executado no modo de detecção, o WAF não toma nenhuma ação que não seja monitorar e registrar a solicitação e sua regra WAF combinada para logs WAF. Ative os diagnósticos de registro para porta frontal (ao usar o portal, isso pode ser conseguido indo para a seção **Diagnósticos** no portal Azure).

- **Modo de prevenção** Quando configurado para ser executado no modo de prevenção, o WAF toma a ação especificada se uma solicitação corresponder a uma regra. Qualquer solicitação correspondente também é registrada nos logs do WAF.

## <a name="waf-response-for-blocked-requests"></a>Resposta waf para solicitações bloqueadas

Por padrão, quando o WAF bloqueia uma solicitação por causa de uma regra combinada, ela retorna um código de status 403 com - **A solicitação é bloqueada.** Uma seqüência de referência também é devolvida para registro.

Você pode definir um código de status de resposta personalizado e uma mensagem de resposta quando uma solicitação é bloqueada pelo WAF. Os seguintes códigos de status personalizados são suportados:

- 200 OK
- 403 Proibido
- Método 405 não permitido
- 406 Não é aceitável
- 429 Muitos pedidos

O código de status de resposta personalizado e a mensagem de resposta são uma configuração de nível de diretiva. Uma vez configurado, todas as solicitações bloqueadas recebem o mesmo status de resposta personalizado e mensagem de resposta.

## <a name="uri-for-redirect-action"></a>URI para ação de redirecionamento

Você é obrigado a definir um URI para redirecionar solicitações para se a ação **REDIRECT** for selecionada para qualquer uma das regras contidas em uma política WAF. Este URI redirecionado precisa ser um site HTTP(S) válido e, uma vez configurado, todas as regras de correspondência de solicitações com uma ação "REDIRECT" serão redirecionadas para o site especificado.


## <a name="next-steps"></a>Próximas etapas
- Saiba como definir [respostas personalizadas do](waf-front-door-configure-custom-response-code.md) WAF
