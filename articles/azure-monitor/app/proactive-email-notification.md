---
title: Alterações de notificação de detecção inteligente-Aplicativo Azure insights
description: Altere para os destinatários de notificação padrão da detecção inteligente. A detecção inteligente permite monitorar rastreamentos de aplicativos com Aplicativo Azure informações sobre padrões incomuns na telemetria de rastreamento.
ms.topic: conceptual
author: harelbr
ms.author: harelbr
ms.date: 03/13/2019
ms.reviewer: mbullwin
ms.openlocfilehash: f73e5bbdd8585b3367e529a8fa00630042e56cac
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "77671776"
---
# <a name="smart-detection-e-mail-notification-change"></a>Alteração de notificações por email de detecção inteligente

Com base nos comentários dos clientes, no dia 1º de abril de 2019, alteraremos as funções padrão que recebem notificações por email de detecção inteligente.

## <a name="what-is-changing"></a>O que está mudando?

Atualmente, as notificações por email de detecção inteligente são enviadas por padrão para as funções de _proprietário da assinatura_, _colaborador da assinatura_ e _leitor da assinatura_. Essas funções geralmente incluem usuários que não estão ativamente envolvidos no monitoramento, o que faz com que muitos desses usuários recebam notificações desnecessariamente. Para melhorar essa experiência, estamos fazendo uma alteração para que as notificações por email sejam enviadas por padrão apenas para as funções de [leitor de monitoramento](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) e [colaborador de monitoramento](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor).

## <a name="scope-of-this-change"></a>Escopo da alteração

Essa alteração afetará todas as regras de detecção inteligente, exceto as seguintes:

* Regras de detecção inteligente marcadas como versão prévia. Essas regras de detecção inteligente não dão suporte a notificações por email no momento.

* Regra de anomalias de falha. Essa regra passará a focar nas novas funções padrão depois da migração de um alerta clássico para a plataforma unificada de alertas (mais informações estão disponíveis [aqui](https://docs.microsoft.com/azure/azure-monitor/platform/monitoring-classic-retirement)).

## <a name="how-to-prepare-for-this-change"></a>Como se preparar para essa alteração?

Para garantir que as notificações por email da detecção inteligente sejam enviadas aos usuários relevantes, esses usuários devem ser atribuídos ao [leitor de monitoramento](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) ou às funções de colaborador de [monitoramento](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) da assinatura.

Para atribuir aos usuários as funções de leitor de monitoramento ou colaborador de monitoramento pelo portal do Azure, siga as etapas descritas no artigo [Adicionar uma atribuição de função](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#add-a-role-assignment). Certifique-se de selecionar _leitor de monitoramento_ ou _colaborador de monitoramento_ como a função atribuída aos usuários.

> [!NOTE]
> Destinatários específicos das notificações de detecção inteligente, configurados usando a opção _Destinatários de email adicionais_ nas configurações de regra, não serão afetados por essa alteração. Esses destinatários continuarão recebendo as notificações por email.

Se você tiver perguntas ou dúvidas sobre essa alteração, [entre em contato conosco](mailto:smart-alert-feedback@microsoft.com).

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre a detecção inteligente:

- [Anomalias de falha](../../azure-monitor/app/proactive-failure-diagnostics.md)
- [Perdas de memória](../../azure-monitor/app/proactive-potential-memory-leak.md)
- [Anomalias de desempenho](../../azure-monitor/app/proactive-performance-diagnostics.md)
