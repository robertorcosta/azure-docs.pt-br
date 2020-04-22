---
title: Dados a serem coletados ao abrir um case para a Microsoft Azure Automation| Microsoft Docs
description: Este artigo descreve algumas das informações que você deve reunir antes de abrir um case para a Azure Automation com o Microsoft Azure Support.
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
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81679412"
---
# <a name="data-to-collect-when-you-open-a-case-for-microsoft-azure-automation"></a>Dados a serem coletados quando você abre um caso para a Automação do Microsoft Azure

Este artigo descreve algumas das informações que você deve reunir antes de abrir um case para a Azure Automation com o Microsoft Azure Support. Essas informações não são necessárias para abrir o caso. No entanto, ele pode ajudar a Microsoft a resolver seu problema mais rapidamente. Além disso, você pode ser solicitado por esses dados pelo engenheiro de suporte depois de abrir o caso.

## <a name="basic-data"></a>Dados básicos

Coletar os dados básicos descritos no artigo [4034605 da Base de Conhecimento - Como capturar diagnósticos com script de Automação Azure](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics).

## <a name="data-for-update-management-issues-on-linux"></a>Dados para problemas de gerenciamento de atualizações no Linux

1. Além dos itens listados no KB [4034605,](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics)execute a seguinte ferramenta de coleta de log:

   [Coletor de log do agente Linux OMS](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/tools/LogCollector/OMS_Linux_Agent_Log_Collector.md)
 
2. Compactar o conteúdo da pasta **/var/opt/microsoft/omsagent/run/automationworker/e,** em seguida, envie o arquivo compactado para o Suporte do Azure.
 
3. Verifique se o ID para o espaço de trabalho ao qual o agente log analytics para Linux relata é o mesmo que o ID para o espaço de trabalho que está sendo monitorado para atualizações.

## <a name="data-for-update-management-issues-on-windows"></a>Dados para problemas de gerenciamento de atualizações no Windows

1. Coletar dados para os itens listados em [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics).

2. Exportar os seguintes logs de eventos para o formato EVTX:

   * Sistema
   * Aplicativo
   * Segurança
   * Operations Manager
   * Microsoft-SMA/Operacional

3. Verifique se o ID do espaço de trabalho ao qual o agente relata é o mesmo que o ID para o espaço de trabalho que está sendo monitorado pelo Windows Updates.

## <a name="data-for-job-issues"></a>Dados para problemas de trabalho

1. Coletar dados para os itens listados em [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics).

2. Colete a carteira de trabalho para o trabalho que tem um problema:

   1. No portal Azure, vá para **Contas de Automação**.
   2. Selecione a conta Automação que você está solucionando problemas e anote o nome.
   3. Selecione **Trabalhos**.
   4. Escolha o trabalho que você está solucionando.
   5. No painel Resumo de Trabalho, procure o valor GUID em **Job ID**.

   ![ID de trabalho dentro do painel de resumo de emprego](media/collect-data-microsoft-azure-automation-case/job-summary-job-id.png)

3. Coletar uma amostra do script que você está executando.

4. Coletar os arquivos de log:

   1. No portal Azure, vá para **Contas de Automação**.
   2. Selecione a conta Automação que você está solucionando problemas.
   3. Selecione **Trabalhos**.
   4. Escolha o trabalho que você está solucionando.
   5. Selecione **Todos os registros**.
   6. No painel resultante, colete os dados.

   ![Dados listados em Todos os Registros](media/collect-data-microsoft-azure-automation-case/all-logs-data.png)

## <a name="data-for-module-issues"></a>Dados para problemas de módulo

Além dos [itens básicos de dados,](#basic-data)reúna as seguintes informações:

* Os passos que você seguiu, para que o problema possa ser reproduzido.
* Capturas de tela de qualquer mensagem de erro.
* Capturas de tela dos módulos atuais e seus números de versão.

## <a name="next-steps"></a>Próximas etapas

Se você precisar de mais ajuda:

* Obtenha respostas de especialistas do Azure através [do Azure Forums](https://azure.microsoft.com/support/forums/).
* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport), a conta oficial do Microsoft Azure para melhorar a experiência do cliente conectando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.
* Registrar um incidente de suporte do Azure. Vá ao site de suporte do [Azure](https://azure.microsoft.com/support/options/) e **selecione Obter suporte**.
