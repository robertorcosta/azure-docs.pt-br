---
title: Usando configurações de diagnóstico para cofres de serviços de recuperação
description: Um artigo descrevendo como usar os eventos de diagnóstico antigos e novos para o Azure Backup
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: e3919d120e5f741af6cd30dd27e5a1dfa2b06cf2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79136932"
---
# <a name="using-diagnostics-settings-for-recovery-services-vaults"></a>Usar configurações de diagnóstico para os Cofres dos Serviços de Recuperação

O Azure Backup envia eventos diagnósticos que podem ser coletados e usados para fins de análise, alerta e emissão de relatórios. 

Você pode configurar as configurações de diagnóstico para um Cofre de Serviços de Recuperação através do portal Azure, navegando até o cofre e clicando no item do menu **Configurações de diagnóstico.** Clicar em **+ Adicionar configuração de diagnóstico** permite enviar um ou mais eventos de diagnóstico para uma conta de armazenamento, event hub ou um espaço de trabalho de Log Analytics (LA).

![Lâmina de configurações de diagnóstico](./media/backup-azure-diagnostics-events/diagnostics-settings-blade.png)

## <a name="diagnostics-events-available-for-azure-backup-users"></a>Eventos de diagnóstico disponíveis para usuários de backup do Azure

O Azure Backup fornece os seguintes eventos de diagnóstico, cada um dos quais fornece dados detalhados sobre um conjunto específico de artefatos relacionados ao backup:

* CoreAzureBackup
* AddonAzureBackupAlertas
* AddonAzureBackupProtectedInstance
* AddonAzureBackupJobs
* Política de backup do AddonAzure
* AddonAzureBackupArmazenamento

[Modelo de dados para eventos de diagnóstico de backup do Azure](https://docs.microsoft.com/azure/backup/backup-azure-reports-data-model)

Os dados desses eventos podem ser enviados para uma conta de armazenamento, espaço de trabalho de LA ou um Event Hub. Se você estiver enviando esses dados para um espaço de trabalho la, você precisa selecionar o alternador específico de **recursos** na tela **Configuração de Diagnósticos** (veja mais informações nas seções abaixo).

## <a name="using-diagnostics-settings-with-log-analytics-la"></a>Usando configurações de diagnóstico com análise de log (LA)

Alinhando-se com o roteiro do Azure Log Analytics, o Azure Backup agora permite enviar dados de diagnóstico de cofre para tabelas de LA dedicadas para backup. Estas são referidas como [tabelas específicas de recursos](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-workspace#resource-specific).

Para enviar os dados de diagnóstico do cofre para LA:

1.  Navegue até o cofre e clique em **Configurações de diagnóstico**. Clique **+ Adicionar configuração de diagnóstico**.
2.  Dê um nome à configuração Diagnósticos.
3.  Marque a caixa **Envie para o Log Analytics** e selecione um espaço de trabalho do Log Analytics.
4.  Selecione **recurso específico** no alternar e verifique os seis eventos a seguir - **CoreAzureBackup**, **AddonAzureBackupAlerts,** **AddonAzureBackupProtectedInstance,** **AddonAzureBackupJobs,** **AddonAzureBackupPolicy**e **AddonAzureBackupStorage**.
5.  Clique em **Salvar**.

![Modo específico do recurso](./media/backup-azure-diagnostics-events/resource-specific-blade.png)

Uma vez que os dados fluam para o Espaço de Trabalho de LA, tabelas dedicadas para cada um desses eventos são criadas em seu espaço de trabalho. Você pode consultar qualquer uma dessas tabelas diretamente e também realizar adesões ou sindicatos entre essas tabelas, se necessário.

> [!IMPORTANT]
> Os seis eventos acima, ou seja, CoreAzureBackup, AddonAzureBackupAlerts, AddonAzureBackupProtectedInstance, AddonAzureBackupJobs, AddonAzureBackupPolicy e AddonAzureBackupStorage, são suportados **apenas** no modo específico de recursos. **Observe que se você tentar enviar dados para esses seis eventos no Modo Diagnóstico Do Azure, nenhum dado fluirá para o Espaço de Trabalho de LA.**

## <a name="legacy-event"></a>Evento Legado

Tradicionalmente, todos os dados de diagnóstico relacionados ao backup de um cofre foram contidos em um único evento chamado 'AzureBackupReport'. Os seis eventos descritos acima são, em essência, uma decomposição de todos os dados contidos no AzureBackupReport. 

Atualmente, continuamos a suportar o evento AzureBackupReport para retrocompatibilidade, nos casos em que os usuários têm consultas personalizadas existentes neste evento, por exemplo, alertas de log personalizados, visualizações personalizadas etc. No entanto, **recomendamos a mudança para os novos eventos o mais cedo possível,** uma vez que isso torna os dados muito mais fáceis de trabalhar em consultas de log, proporciona melhor a descoberta de esquemas e sua estrutura, melhora o desempenho tanto em tempos de latência de ingestão quanto de consulta. **O suporte para usar o modo Diagnóstico sinuoso será eventualmente eliminado e, portanto, a escolha dos novos eventos pode ajudá-lo a evitar migrações complexas em uma data posterior**.

Use a diretiva integrada do Azure Backup para adicionar uma nova configuração de diagnóstico com os 6 novos eventos, para todos os seus cofres em um escopo especificado: [Configure configurações de diagnóstico de cofre em escala](https://docs.microsoft.com/azure/backup/azure-policy-configure-diagnostics)

Você pode optar por criar configurações de diagnóstico separadas para o AzureBackupReport e os seis novos eventos, até que você tenha migrado todas as suas consultas personalizadas para usar dados das novas tabelas. A imagem abaixo mostra um exemplo de um cofre com duas configurações de diagnóstico. A primeira configuração, chamada **Setting1,** envia dados do evento AzureBackupReport para um modo LA Workspace no modo AzureDiagnostics. A segunda configuração, chamada **Configuração2,** envia dados dos seis novos eventos de backup do Azure para um espaço de trabalho la no modo Específico de Recursos.

![Duas configurações](./media/backup-azure-diagnostics-events/two-settings-example.png)

> [!IMPORTANT]
> O evento AzureBackupReport é suportado **apenas** no Modo Diagnóstico Do Azure. **Observe que se você tentar enviar dados para este evento no modo específico de recursos, nenhum dado fluirá para o Espaço de Trabalho la.**

> [!NOTE]
> O alternador para Azure Diagnostics / Resource Specific só aparece se o usuário verificar **Enviar para o Log Analytics**. Para enviar dados para uma conta de armazenamento ou um Hub de eventos, um usuário pode simplesmente selecionar o destino necessário e verificar qualquer um dos eventos desejados, sem quaisquer entradas adicionais. Novamente, recomenda-se não escolher o evento legado AzureBackupReport, daqui para frente.

## <a name="users-sending-azure-site-recovery-events-to-log-analytics-la"></a>Usuários que enviam eventos de recuperação do site do Azure para o Log Analytics (LA)

Os eventos de recuperação do azure e do site do Azure são enviados do mesmo Cofre de Serviços de Recuperação. Como a recuperação do site do Azure não está atualmente a bordo em tabelas específicas de recursos, os usuários que desejam enviar eventos de recuperação do site do Azure para LA são direcionados apenas para usar **o** Modo Diagnóstico do Azure (veja imagem abaixo). **A escolha do modo específico de recursos para os eventos de recuperação do site do Azure impedirá que os dados necessários sejam enviados para o Espaço de Trabalho la**.

![Eventos de recuperação do site](./media/backup-azure-diagnostics-events/site-recovery-settings.png)

Para resumir as nuances acima:

* Se você já tiver diagnósticos de LA configurados com o Azure Diagnostics e tiver escrito consultas personalizadas em cima dele, mantenha essa configuração **intacta,** até que você migre suas consultas para usar dados dos novos eventos.
* Se você também quiser embarcar em novas tabelas (conforme recomendado), crie uma **nova** configuração de diagnóstico, escolha **Específico de recurso** e selecione os seis novos eventos conforme especificado acima.
* Se você estiver enviando eventos de recuperação do site do Azure para LA, **não** escolha o modo Específico de recurso para esses eventos, caso contrário, os dados para esses eventos não fluirão para o seu Espaço de Trabalho LA. Em vez disso, crie uma **configuração de diagnóstico adicional,** selecione **Azure Diagnostics**e escolha os eventos relevantes de recuperação do site do Azure.

A imagem abaixo mostra um exemplo de um usuário tendo três configurações de diagnóstico para um cofre. A primeira configuração, chamada **Setting1,** envia dados do evento AzureBackupReport para um espaço de trabalho la no modo AzureDiagnostics. A segunda configuração, chamada **Configuração2,** envia dados dos seis novos eventos de backup do Azure para um espaço de trabalho la no modo Específico de Recursos. A terceira configuração, chamada **Setting3,** envia dados dos eventos de recuperação do site do Azure para um espaço de trabalho la no modo diagnóstico do Azure.

![Três configurações](./media/backup-azure-diagnostics-events/three-settings-example.png)

## <a name="next-steps"></a>Próximas etapas

[Aprenda o modelo de dados do Log Analytics para os eventos de diagnóstico](https://docs.microsoft.com/azure/backup/backup-azure-reports-data-model)
