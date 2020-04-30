---
title: Dados a serem coletados quando você abre um caso para Microsoft Azure automação | Microsoft Docs
description: Este artigo descreve algumas das informações que você deve coletar antes de abrir um caso para a automação do Azure com suporte Microsoft Azure.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 09/23/2019
ms.author: v-miegge
ms.openlocfilehash: e1d5d791a58f301991819b41757b9021f6e30fc0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81679412"
---
# <a name="data-to-collect-when-you-open-a-case-for-microsoft-azure-automation"></a>Dados a serem coletados quando você abre um caso para a Automação do Microsoft Azure

Este artigo descreve algumas das informações que você deve coletar antes de abrir um caso para a automação do Azure com suporte Microsoft Azure. Essas informações não são necessárias para abrir o caso. No entanto, ele pode ajudar a Microsoft a resolver seu problema mais rapidamente. Além disso, você pode ser solicitado a fornecer esses dados pelo engenheiro de suporte depois de abrir o caso.

## <a name="basic-data"></a>Dados básicos

Colete os dados básicos descritos no artigo da base de dados de conhecimento [4034605-como capturar a automação do Azure-diagnóstico com script](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics).

## <a name="data-for-update-management-issues-on-linux"></a>Dados para problemas de Gerenciamento de Atualizações no Linux

1. Além dos itens listados em KB [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics), execute a seguinte ferramenta de coleta de logs:

   [Coletor de logs do agente do OMS para Linux](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/tools/LogCollector/OMS_Linux_Agent_Log_Collector.md)
 
2. Compacte o conteúdo da pasta **/var/opt/Microsoft/omsagent/Run/automationworker/** e, em seguida, envie o arquivo compactado para o suporte do Azure.
 
3. Verifique se a ID do espaço de trabalho para a qual o agente do Log Analytics para Linux se reporta é igual à ID do espaço de trabalho que está sendo monitorado para atualizações.

## <a name="data-for-update-management-issues-on-windows"></a>Dados para problemas de Gerenciamento de Atualizações no Windows

1. Coletar dados para os itens listados em [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics).

2. Exporte os seguintes logs de eventos para o formato EVTX:

   * System (sistema)
   * Aplicativo
   * Segurança
   * Operations Manager
   * Microsoft-SMA/operacional

3. Verifique se a ID do espaço de trabalho que o agente se reporta é igual à ID do espaço de trabalho que está sendo monitorado pelas atualizações do Windows.

## <a name="data-for-job-issues"></a>Dados para problemas de trabalho

1. Coletar dados para os itens listados em [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics).

2. Colete a ID do trabalho que tem um problema:

   1. Na portal do Azure, acesse **contas de automação**.
   2. Selecione a conta de automação para a qual você está Solucionando problemas e anote o nome.
   3. Selecione **trabalhos**.
   4. Escolha o trabalho que você está solucionando.
   5. No painel Resumo do trabalho, procure o valor GUID na **ID do trabalho**.

   ![ID do trabalho no painel Resumo do trabalho](media/collect-data-microsoft-azure-automation-case/job-summary-job-id.png)

3. Colete uma amostra do script que você está executando.

4. Coletar os arquivos de log:

   1. Na portal do Azure, acesse **contas de automação**.
   2. Selecione a conta de automação para a qual você está Solucionando problemas.
   3. Selecione **trabalhos**.
   4. Escolha o trabalho que você está solucionando.
   5. Selecione **todos os logs**.
   6. No painel resultante, colete os dados.

   ![Dados listados em todos os logs](media/collect-data-microsoft-azure-automation-case/all-logs-data.png)

## <a name="data-for-module-issues"></a>Dados para problemas de módulo

Além dos itens de [dados básicos](#basic-data), reúna as seguintes informações:

* As etapas que você seguiu, para que o problema possa ser reproduzido.
* Capturas de tela de qualquer mensagem de erro.
* Capturas de tela dos módulos atuais e seus números de versão.

## <a name="next-steps"></a>Próximas etapas

Se você precisar de mais ajuda:

* Obtenha respostas de especialistas do Azure por meio dos [fóruns do Azure](https://azure.microsoft.com/support/forums/).
* Conecte- [@AzureSupport](https://twitter.com/azuresupport)se com o, a conta de Microsoft Azure oficial para melhorar a experiência do cliente conectando a Comunidade do Azure aos recursos certos: respostas, suporte e especialistas.
* Registrar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione **obter suporte**.
