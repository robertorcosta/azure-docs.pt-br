---
title: Monitoramento de perguntas frequentes sobre alertas e relatórios
description: Neste artigo, descubra respostas para perguntas comuns sobre os relatórios Azure Backup Monitoring Alert e Azure Backup.
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 07/08/2019
ms.openlocfilehash: f5be97458ba658f315c31ae34e540842b64e3ec4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76989562"
---
# <a name="azure-backup-monitoring-alert---faq"></a>Alerta de monitoramento de backup do Azure - PERGUNTAS FREQUENTES

Este artigo responde a perguntas comuns sobre o monitoramento e a emissão de relatórios do Azure Backup.

## <a name="configure-azure-backup-reports"></a>Configurar relatórios de Backup do Azure

### <a name="how-do-i-check-if-reporting-data-has-started-flowing-into-a-log-analytics-la-workspace"></a>Como verificar se os dados de relatórios começaram a fluir para um espaço de trabalho do Log Analytics (LA) ?

Navegue até o espaço de trabalho la configurado, navegue até o item do menu **Logs** e execute a consulta CoreAzureBackup | tomar 1. Se você ver um registro sendo devolvido, significa que os dados começaram a fluir para o espaço de trabalho. O empurrão de dados iniciais pode levar até 24 horas.

### <a name="what-is-the-frequency-of-data-push-to-an-la-workspace"></a>Qual é a frequência de pressão de dados para um espaço de trabalho la?

Os dados de diagnóstico do cofre são bombeados para o espaço de trabalho do Log Analytics com algum atraso. Todos os eventos chegam ao espaço de trabalho do Log Analytics de 20 a 30 minutos depois de serem empurrados do cofre dos Serviços de Recuperação. Aqui estão mais detalhes sobre o lag:

* Em todas as soluções, os alertas incorporados do serviço de backup são empurrados assim que são criados. Assim, eles geralmente aparecem no espaço de trabalho do Log Analytics após 20 a 30 minutos.
* Em todas as soluções, os trabalhos de backup demanda e os empregos de restauração são empurrados assim que terminam.
* Para todas as soluções, exceto backup SQL, os trabalhos de backup programados são empurrados assim que terminam.
* Para backup sql, como os backups de log podem ocorrer a cada 15 minutos, as informações para todos os trabalhos de backup programados completos, incluindo logs, são loteadas e empurradas a cada 6 horas.
* Em todas as soluções, outras informações, como o item de backup, política, pontos de recuperação, armazenamento e assim por diante, são empurradas pelo menos uma vez por dia.
* Uma alteração na configuração de backup (como alterar política ou editar política) desencadeia um impulso de todas as informações de backup relacionadas.

### <a name="how-long-can-i-retain-reporting-data"></a>Quanto tempo posso reter dados de relatórios?

Depois de criar um espaço de trabalho la, você pode optar por reter dados por um máximo de 2 anos. Por padrão, um Espaço de Trabalho LA retém dados por 31 dias.

### <a name="will-i-see-all-my-data-in-reports-after-i-configure-the-la-workspace"></a>Verei todos os meus dados nos relatórios depois de configurar o Espaço de Trabalho de LA?

 Todos os dados gerados após a configuração das configurações de diagnóstico são empurrados para o Espaço de Trabalho la e estão disponíveis nos relatórios. Os trabalhos em andamento não são enviados por push para a geração de relatórios. Depois que o trabalho termina ou falha, ele é enviado para relatórios.

### <a name="can-i-view-reports-across-vaults-and-subscriptions"></a>Posso exibir relatórios entre cofres e assinaturas?

Sim, você pode ver relatórios através de cofres e assinaturas, bem como regiões. Seus dados podem residir em um único espaço de trabalho la ou em um grupo de espaços de trabalho LA.

### <a name="can-i-view-reports-across-tenants"></a>Posso ver relatórios entre inquilinos?

Se você é um usuário [do Azure Lighthouse](https://azure.microsoft.com/services/azure-lighthouse/) com acesso delegado às assinaturas de seus clientes ou espaços de trabalho LA, você pode usar relatórios de backup para visualizar dados em todos os seus inquilinos.

### <a name="how-long-does-it-take-for-the-azure-backup-agent-job-status-to-reflect-in-the-portal"></a>Quanto tempo leva para refletir no portal o status de trabalho do agente de backup do Azure?

O portal do Azure pode levar até 15 minutos para refletir o status de trabalho do agente de backup do Azure.

### <a name="when-a-backup-job-fails-how-long-does-it-take-to-raise-an-alert"></a>Quando um trabalho de backup falha, quanto tempo demora para gerar um alerta?

Um alerta é gerado em 20 minutos após a falha de backup do Azure.

### <a name="is-there-a-case-where-an-email-wont-be-sent-if-notifications-are-configured"></a>Há um caso em que um email não será enviado se as notificações forem configuradas?

Sim. Nas situações a seguir, notificações por email não serão enviadas.

* Se as notificações forem configuradas por hora e um alerta for gerado e resolvido em uma hora
* Quando um trabalho é cancelado
* Se um segundo trabalho de backup falhou porque o trabalho de backup original estava em andamento

## <a name="recovery-services-vault"></a>Cofre dos Serviços de Recuperação

### <a name="how-long-does-it-take-for-the-azure-backup-agent-job-status-to-reflect-in-the-portal"></a>Quanto tempo leva para refletir no portal o status de trabalho do agente de backup do Azure?

O portal do Azure pode levar até 15 minutos para refletir o status de trabalho do agente de backup do Azure.

### <a name="when-a-backup-job-fails-how-long-does-it-take-to-raise-an-alert"></a>Quando um trabalho de backup falha, quanto tempo demora para gerar um alerta?

Um alerta é gerado em 20 minutos após a falha de backup do Azure.

### <a name="is-there-a-case-where-an-email-wont-be-sent-if-notifications-are-configured"></a>Há um caso em que um email não será enviado se as notificações forem configuradas?

Sim. Nas seguintes situações, as notificações não são enviadas:

* Se as notificações forem configuradas por hora e um alerta for gerado e resolvido em uma hora
* Quando um trabalho é cancelado
* Se um segundo trabalho de backup falhou porque o trabalho de backup original estava em andamento

## <a name="next-steps"></a>Próximas etapas

Leia as outras perguntas frequentes:

* [Perguntas comuns](backup-azure-vm-backup-faq.md) sobre backups de VM do Azure.
* [Perguntas comuns](backup-azure-file-folder-backup-faq.md) sobre o agente do Backup do Azure
