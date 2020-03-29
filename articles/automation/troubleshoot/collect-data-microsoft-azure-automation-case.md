---
title: Dados a serem coletados ao abrir um case para a Microsoft Azure Automation| Microsoft Docs
description: Este artigo descreve algumas das informações básicas que você deve reunir antes de abrir um case para a Azure Automation com o Microsoft Azure Support.
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
ms.openlocfilehash: 4839ce7a0188c782656fd3a4c42cbdd116b165e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74849370"
---
# <a name="data-to-collect-when-you-open-a-case-for-microsoft-azure-automation"></a>Dados a serem coletados quando você abre um caso para a Automação do Microsoft Azure

Este artigo descreve algumas das informações básicas que você deve reunir antes de abrir um case para a Azure Automation com o Microsoft Azure Support. Essas informações não são necessárias para abrir o caso. No entanto, ele pode ajudar a Microsoft a resolver seu problema mais rapidamente. Além disso, você pode ser solicitado por esses dados pelo engenheiro de suporte depois de abrir o caso.

## <a name="collect-more-information"></a>Coletar mais informações

Antes de abrir um case para o Microsoft Azure Automation Support, recomendamos que você colete as seguintes informações.

### <a name="basic-data-collection"></a>Coleta básica de dados

Coletar os dados descritos no seguinte artigo da Base de Conhecimento:

* [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics) Como capturar diagnósticos com script de automação do Azure

## <a name="collect-data-depending-on-issue"></a>Coletar dados dependendo do problema
 
### <a name="for-issues-that-affect-update-management-on-linux"></a>Para problemas que afetam o gerenciamento de atualizações no Linux

1. Além dos itens listados no KB [4034605,](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics)execute a seguinte ferramenta de coleta de log:

   [Coletor de log do agente Linux OMS](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/tools/LogCollector/OMS_Linux_Agent_Log_Collector.md)
 
2. Compactar o conteúdo da pasta a seguir e, em seguida, enviar o arquivo compactado para o suporte do Azure:

   ``/var/opt/microsoft/omsagent/run/automationworker/``
 
3. Verifique se o ID do espaço de trabalho que o AGENTE LINUX do OMS está relatando é o mesmo que o espaço de trabalho que está sendo monitorado para atualizações.

### <a name="for-issues-that-affect-update-management-on-windows"></a>Para problemas que afetam o gerenciamento de atualizações no Windows

Além dos itens listados no [4034605,](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics)faça o seguinte:

1. Exportar os seguintes logs de eventos para o formato EVTX:

   * Sistema
   * Aplicativo
   * Segurança
   * Operations Manager
   * Microsoft-SMA/Operacional

2. Verifique se o ID do espaço de trabalho ao qual o agente está relatando é o mesmo que o espaço de trabalho que está sendo monitorado pelo Windows Updates.

### <a name="for-issues-that-affect-a-job"></a>Para questões que afetam um trabalho

Além dos itens listados no [4034605,](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics)colete as seguintes informações:

1. Colete o número **do JobID** (para o trabalho que está enfrentando um problema):

   1. No portal Azure, vá para a lâmina **contas de automação.**
   2. Selecione a **conta de automação** que você está solucionando problemas.
   3. Selecione **Trabalhos**.
   4. Selecione o trabalho que você está solucionando problemas.
   5. Em **Resumo de Emprego,** procure um **ID de Trabalho** (GUID).

   ![ID de trabalho dentro do painel de resumo de emprego](media/collect-data-microsoft-azure-automation-case/job-summary-job-id.png)

2. Coletar o nome da conta:

   1. No portal Azure, vá para a lâmina **contas de automação.**
   2. Obtenha o nome da conta de **automação** que você está solucionando problemas.

3. Coletar amostra do script que você está executando.

4. Coletar os arquivos de log:

   1. No portal Azure, vá para a lâmina **contas de automação.**
   2. Selecione a **conta de automação** que você está solucionando problemas.
   3. Selecione **Trabalhos**.
   4. Selecione o trabalho que você está solucionando problemas.
   5. Selecione **Todos os registros**.
   6. Na lâmina resultante, colete os dados.

   ![Dados listados em Todos os Registros](media/collect-data-microsoft-azure-automation-case/all-logs-data.png)

### <a name="for-issues-that-affect-modules"></a>Para problemas que afetam módulos

Além dos itens em "Coleta básica de dados", reúna as seguintes informações:

* Os passos que você seguiu para que o problema possa ser reproduzido.
* Uma captura de tela de qualquer mensagem de erro.
* Uma captura de tela dos módulos atuais e seus números de versão.

## <a name="next-steps"></a>Próximas etapas

Se você precisar de mais ajuda em qualquer momento deste artigo, entre em contato com os especialistas do Azure nos [fóruns MSDN Azure e Stack Overflow](https://azure.microsoft.com/support/forums/).

Alternativamente, registre um incidente de suporte ao Azure. Vá ao site de suporte do [Azure](https://azure.microsoft.com/support/options/) e **selecione Obter suporte**.
