---
title: Monitore as cargas de trabalho protegidas do Azure Backup
description: Neste artigo, conheça os recursos de monitoramento e notificação das cargas de trabalho do Azure Backup usando o portal Azure.
ms.topic: conceptual
ms.date: 03/05/2019
ms.assetid: 86ebeb03-f5fa-4794-8a5f-aa5cbbf68a81
ms.openlocfilehash: de5a82f5ad1d8113b27c07484f2f08f4cf97c759
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294930"
---
# <a name="monitoring-azure-backup-workloads"></a>Monitorando as cargas de trabalho de backup do Azure

O Azure Backup fornece várias soluções de backup com base no requisito de backup e topologia de infra-estrutura (On-premises vs Azure). Qualquer usuário de backup ou admin deve ver o que está acontecendo em todas as soluções e espera-se que seja notificado em cenários importantes. Este artigo detalha os recursos de monitoramento e notificação fornecidos pelo serviço de backup do Azure.

## <a name="backup-jobs-in-recovery-services-vault"></a>Trabalhos de backup no cofre de serviços de recuperação

O Azure Backup fornece recursos de monitoramento e alerta incorporados para cargas de trabalho protegidas pelo Azure Backup. Nas configurações do cofre dos Serviços de Recuperação, a seção **Monitoramento** fornece trabalhos e alertas embutidos.

![Monitoramento embutido do cofre RS](media/backup-azure-monitoring-laworkspace/rs-vault-inbuiltmonitoring.png)

Os trabalhos são gerados quando operações como configurar backup, backup, restauração, exclusão de backup e assim por diante são executadas.

Os trabalhos das seguintes soluções de backup do Azure são mostrados aqui:

- Backup da VM do Azure
- Backup do arquivo Azure
- Backup de carga de trabalho do Azure, como SQL e SAP HANA
- Agente de Backup do Azure (MAB)

Os trabalhos do System Center Data Protection Manager (SC-DPM), Microsoft Azure Backup Server (MABS) NÃO são exibidos.

> [!NOTE]
> Cargas de trabalho do Azure, como backups SQL e SAP HANA dentro das VMs do Azure, têm um grande número de trabalhos de backup. Por exemplo, backups de log podem ser executados a cada 15 minutos. Assim, para tais cargas de trabalho DB, apenas as operações acionadas pelo usuário são exibidas. As operações de backup programadas NÃO são exibidas.

## <a name="backup-alerts-in-recovery-services-vault"></a>Alertas de backup no cofre dos Serviços de Recuperação

Os alertas são principalmente cenários em que os usuários são notificados para que possam tomar medidas relevantes. A seção **Alertas de backup** mostra os alertas gerados pelo serviço de backup do Azure. Esses alertas são definidos pelo serviço e o usuário não pode criar alertas personalizados.

### <a name="alert-scenarios"></a>Cenários de alerta

Os seguintes cenários são definidos pelo serviço como cenários alertáveis.

- Falhas de backup/restauração
- Backup bem-sucedido com avisos para o Agente de Backup do Azure (MAB)
- Parar a proteção com reter dados/Parar de proteção com dados de exclusão

### <a name="alerts-from-the-following-azure-backup-solutions-are-shown-here"></a>Os alertas das seguintes soluções de backup do Azure são mostrados aqui

- Backups de VM do Azure
- Backups de arquivo do Azure
- Backups de carga de trabalho do Azure, como SQL, SAP HANA
- Agente de Backup do Azure (MAB)

> [!NOTE]
> Os alertas do System Center Data Protection Manager (SC-DPM), Microsoft Azure Backup Server (MABS) NÃO são exibidos aqui.

### <a name="consolidated-alerts"></a>Alertas Consolidados

Para soluções de backup de carga de trabalho do Azure, como SQL e SAP HANA, os backups de log podem ser gerados com muita freqüência (até a cada 15 minutos de acordo com a política). Portanto, também é possível que as falhas de backup de log também sejam muito freqüentes (até a cada 15 minutos). Neste cenário, o usuário final ficará sobrecarregado se um alerta for levantado para cada ocorrência de falha. Assim, um alerta é enviado para a primeira ocorrência e se as falhas subseqüentes são por causa da mesma causa raiz, então novos alertas não são gerados. O primeiro alerta é atualizado com a contagem de falhas. Mas se o alerta for inativado pelo usuário, a próxima ocorrência acionará outro alerta e este será tratado como o primeiro alerta para essa ocorrência. É assim que o Azure Backup executa a consolidação de alerta sql e sap hana backups.

### <a name="exceptions-when-an-alert-is-not-raised"></a>Exceções quando um alerta não é levantado

Há poucas exceções quando um alerta não é levantado sobre uma falha. Eles são:

- O usuário cancelou explicitamente o trabalho de execução
- O trabalho falha porque outro trabalho de backup está em andamento (nada para agir aqui, já que só temos que esperar o trabalho anterior terminar)
- O trabalho de backup da VM falha porque a VM azure de backup não existe mais
- [Alertas Consolidados](#consolidated-alerts)

As exceções acima são projetadas a partir do entendimento de que o resultado dessas operações (principalmente o usuário acionado) aparece imediatamente em clientes portal/PS/CLI. Assim, o usuário está imediatamente ciente e não precisa de uma notificação.

### <a name="alert-types"></a>Tipos de alerta

Com base na gravidade do alerta, os alertas podem ser definidos em três tipos:

- **Crítico**: Em princípio, qualquer falha de backup ou recuperação (programada ou acionada pelo usuário) levaria à geração de um alerta e seria mostrada como um alerta crítico e também operações destrutivas, como exclusão de backup.
- **Aviso**: Se a operação de backup for bem sucedida, mas com poucos avisos, eles serão listados como alertas de aviso.
- **Informações**: A partir de hoje, nenhum alerta informativo é gerado pelo serviço de backup do Azure.

## <a name="notification-for-backup-alerts"></a>Notificação para alertas de backup

> [!NOTE]
> A configuração da notificação só pode ser feita através do Portal Azure. O suporte ao modelo ps/CLI/REST API/Azure Resource Manager não é suportado.

Uma vez que um alerta é levantado, os usuários são notificados. O Azure Backup fornece um mecanismo de notificação embutido por e-mail. Pode-se especificar endereços de e-mail individuais ou listas de distribuição a serem notificados quando um alerta é gerado. Você também pode escolher se deve ser notificado para cada alerta individual ou agrupá-los em um digerir de hora em hora e, em seguida, ser notificado.

![Notificação de e-mail incorporada do RS Vault](media/backup-azure-monitoring-laworkspace/rs-vault-inbuiltnotification.png)

Quando a notificação estiver configurada, você receberá um e-mail de boas-vindas ou introdutório. Isso confirma que o Azure Backup pode enviar e-mails para esses endereços quando um alerta é levantado.<br>

Se a freqüência foi definida para uma digestão horária e um alerta foi levantado e resolvido dentro de uma hora, não será uma parte da próxima digestão horária.

> [!NOTE]
>
> - Se uma operação destrutiva, como **a proteção de stop com dados de exclusão,** um alerta é levantado e um e-mail é enviado para proprietários de assinaturas, admins e co-admins, mesmo que as notificações NÃO estejam configuradas para o cofre do Serviço de Recuperação.
> - Para configurar a notificação para trabalhos bem-sucedidos, use [o Log Analytics](backup-azure-monitoring-use-azuremonitor.md#using-log-analytics-workspace).

## <a name="inactivating-alerts"></a>Alertas de inativação

Para inativar/resolver um alerta ativo, você pode clicar no item da lista correspondente ao alerta que deseja inativar. Isso abre uma tela que exibe informações detalhadas sobre o alerta, com um botão 'Inativar' na parte superior. Clicar neste botão mudaria o status do alerta para 'Inativo'. Você também pode inativar um alerta clicando com o botão direito do mouse no item da lista correspondente a esse alerta e selecionando 'Inativar'.

![Inativação do alerta do Cofre RS](media/backup-azure-monitoring-laworkspace/vault-alert-inactivation.png)

## <a name="next-steps"></a>Próximas etapas

[Monitore as cargas de trabalho de backup do Azure usando o Monitor do Azure](backup-azure-monitoring-use-azuremonitor.md)
