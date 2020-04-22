---
title: Solução de problemas para o onboarding de soluções de gerenciamento de automação do Azure
description: Aprenda a solucionar problemas de problemas de embarque.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 05/22/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: da5152b459f54cbaae5ec168f103f23a237edebd
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81679226"
---
# <a name="troubleshoot-solution-onboarding"></a>Solução de solução de solução de solução de solução de solucionar

Você pode receber erros ao embarcar na solução de gerenciamento de atualizações ou na solução De rastreamento e inventário de alterações. Este artigo descreve os vários erros que podem ocorrer e como resolvê-los.

## <a name="known-issues"></a>Problemas conhecidos

### <a name="scenario-renaming-a-registered-node-requires-unregister-or-register-again"></a><a name="node-rename"></a>Cenário: Renomear um nó registrado requer não registrar ou registrar novamente

#### <a name="issue"></a>Problema

Um nó é registrado no Azure Automation e, em seguida, o nome do computador do sistema operacional é alterado. Os relatórios do nó continuam a aparecer com o nome original.

#### <a name="cause"></a>Causa

Renomear os álos registrados não atualiza o nome do nó no Azure Automation.

#### <a name="resolution"></a>Resolução

Desregistre o nó da configuração do estado de automação do Azure e registre-o novamente. Relatórios publicados no serviço antes desse horário não estarão mais disponíveis.

### <a name="scenario-re-signing-certificates-via-https-proxy-is-not-supported"></a><a name="resigning-cert"></a>Cenário: Não é suportado certificados de reassinatura via proxy https

#### <a name="issue"></a>Problema

Ao conectar-se através de uma solução proxy que encerra o tráfego HTTPS e, em seguida, recriptografa o tráfego usando um novo certificado, o serviço não permite a conexão.

#### <a name="cause"></a>Causa

O Azure Automation não suporta re-assinatura de certificados usados para criptografar tráfego.

#### <a name="resolution"></a>Resolução

No momento, não há solução para esta questão.

## <a name="general-errors"></a>Erros gerais

### <a name="scenario-onboarding-fails-with-the-message---the-solution-cannot-be-enabled"></a><a name="missing-write-permissions"></a>Cenário: Onboarding falha com a mensagem - A solução não pode ser habilitada

#### <a name="issue"></a>Problema

Você recebe uma das seguintes mensagens quando tenta embarcar uma VM para uma solução:

```error
The solution cannot be enabled due to missing permissions for the virtual machine or deployments
```

```error
The solution cannot be enabled on this VM because the permission to read the workspace is missing
```

#### <a name="cause"></a>Causa

Este erro é causado por permissões incorretas ou ausentes na VM ou no espaço de trabalho, ou para o usuário.

#### <a name="resolution"></a>Resolução

Certifique-se de que você tem [permissões corretas necessárias para a bordo de máquinas](../automation-role-based-access-control.md#onboarding-permissions) e, em seguida, tente a bordo da solução novamente. Se você receber `The solution cannot be enabled on this VM because the permission to read the workspace is missing`o erro, `Microsoft.OperationalInsights/workspaces/read` certifique-se de que você tem a permissão para ser capaz de encontrar se a VM está a bordo de um espaço de trabalho.

### <a name="scenario-onboarding-fails-with-the-message-failed-to-configure-automation-account-for-diagnostic-logging"></a><a name="diagnostic-logging"></a>Cenário: Onboarding falha com a mensagem: Falha ao configurar a conta de automação para registro de diagnóstico

#### <a name="issue"></a>Problema

Você recebe a seguinte mensagem quando tenta embarcar uma VM para uma solução:

```error
Failed to configure automation account for diagnostic logging
```

#### <a name="cause"></a>Causa

Esse erro pode ser causado se a camada de preços não corresponder ao modelo de faturamento da assinatura. Consulte [O uso de monitoramento e os custos estimados no Monitor Azure](https://aka.ms/PricingTierWarning).

#### <a name="resolution"></a>Resolução

Crie seu espaço de trabalho log analytics manualmente e repita o processo de onboarding para selecionar o espaço de trabalho criado.

### <a name="scenario-computergroupqueryformaterror"></a><a name="computer-group-query-format-error"></a>Cenário: ComputerGroupQueryFormatError

#### <a name="issue"></a>Problema

Este código de erro significa que a consulta de grupo de computador de pesquisa salva usada para segmentar a solução não está formatada corretamente. 

#### <a name="cause"></a>Causa

Você pode ter alterado a consulta, ou o sistema pode tê-la alterado.

#### <a name="resolution"></a>Resolução

Você pode excluir a consulta para a solução e, em seguida, a bordo da solução novamente, que recria a consulta. A consulta pode ser encontrada em seu espaço de trabalho, em **pesquisas salvas**. O nome da consulta é **MicrosoftDefaultComputerGroup**, e a categoria da consulta é o nome da solução associada. Se várias soluções estiverem habilitadas, a consulta **MicrosoftDefaultComputerGroup** será mostrada várias vezes em **Pesquisas salvas**.

### <a name="scenario-policyviolation"></a><a name="policy-violation"></a>Cenário: PolicyViolation

#### <a name="issue"></a>Problema

Este código de erro indica que a implantação falhou devido à violação de uma ou mais políticas.

#### <a name="cause"></a>Causa 

Uma política está impedindo a operação de ser completada.

#### <a name="resolution"></a>Resolução

Para implantar a solução com sucesso, você deve considerar alterar a política indicada. Como existem muitos tipos diferentes de políticas que podem ser definidas, as mudanças necessárias dependem da política que é violada. Por exemplo, se uma diretiva for definida em um grupo de recursos que negue permissão para alterar o conteúdo de alguns recursos contidos, você poderá escolher uma dessas correções:

* Remover completamente a política.
* Tente embarcar na solução para um grupo de recursos diferente.
* Redirecione a política para um recurso específico, por exemplo, uma conta de Automação.
* Revise o conjunto de recursos que a política está configurada para negar.

Verifique as notificações no canto superior direito do portal Azure ou navegue até o grupo de recursos que contém sua conta de Automação e selecione **Implantações** em **Configurações** para exibir a implantação com falha. Para saber mais sobre a Política do Azure, consulte [Visão Geral da Política do Azure](../../governance/policy/overview.md?toc=%2fazure%2fautomation%2ftoc.json).

### <a name="scenario-errors-trying-to-unlink-a-workspace"></a><a name="unlink"></a>Cenário: Erros ao tentar desvincular um espaço de trabalho

#### <a name="issue"></a>Problema

Você recebe o seguinte erro ao tentar desvincular um espaço de trabalho:

```error
The link cannot be updated or deleted because it is linked to Update Management and/or ChangeTracking Solutions.
```

#### <a name="cause"></a>Causa

Esse erro ocorre quando você ainda tem soluções ativas em seu espaço de trabalho do Log Analytics que dependem da vinculação da sua conta de Automação e do espaço de trabalho do Log Analytics.

### <a name="resolution"></a>Resolução

Remova as seguintes soluções do seu espaço de trabalho se estiver usando-as:

* Gerenciamento de atualizações
* Controle de Alterações e Inventário
* Iniciar/Parar VMs durante os horários inativos

Uma vez que você remova as soluções, você pode desvincular seu espaço de trabalho. É importante limpar quaisquer artefatos existentes dessas soluções do seu espaço de trabalho e da sua conta de Automação 

* Para gerenciamento de atualizações, remova as implantações de atualização (Agendas) da sua conta de automação.
* Para VMs start/stop durante as horas de folga, remova quaisquer bloqueios nos componentes da solução em sua conta de automação em **Configurações** > **Bloqueios**. Consulte [Remover as VMs start/stop durante a solução fora do horário de expediente](../automation-solution-vm-management.md#remove-the-solution).

## <a name="log-analytics-for-windows-extension-failures"></a><a name="mma-extension-failures"></a>Análise de log para falhas de extensão do Windows

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 

Uma instalação do agente Log Analytics para extensão do Windows pode falhar por uma variedade de razões. A seção a seguir descreve problemas de onboarding que podem causar falhas durante a implantação do agente Log Analytics para extensão do Windows.

>[!NOTE]
>O agente log analytics para Windows é o nome usado atualmente no Azure Automation para o Microsoft Monitoring Agent (MMA).

### <a name="scenario-an-exception-occurred-during-a-webclient-request"></a><a name="webclient-exception"></a>Cenário: ocorreu uma exceção durante uma solicitação de WebClient

A extensão Log Analytics for Windows na VM não consegue se comunicar com recursos externos e a implantação falha.

#### <a name="issue"></a>Problema

A seguir, exemplos de mensagens de erro retornadas:

```error
Please verify the VM has a running VM agent, and can establish outbound connections to Azure storage.
```

```error
'Manifest download error from https://<endpoint>/<endpointId>/Microsoft.EnterpriseCloud.Monitoring_MicrosoftMonitoringAgent_australiaeast_manifest.xml. Error: UnknownError. An exception occurred during a WebClient request.
```

#### <a name="cause"></a>Causa

Algumas causas possíveis para esse erro são:

* Um proxy configurado na VM só permite portas específicas.
* Uma configuração de firewall bloqueou o acesso às portas e endereços necessários.

#### <a name="resolution"></a>Resolução

Certifique-se de ter as portas e os endereços adequados abertos para comunicação. Para obter uma lista de portas e endereços, consulte [planejando sua rede](../automation-hybrid-runbook-worker.md#network-planning).

### <a name="scenario-install-failed-because-of-a-transient-environment-issues"></a><a name="transient-environment-issue"></a>Cenário: Falha na instalação devido a problemas ambientais transitórios

A instalação da extensão Log Analytics for Windows falhou durante a implantação devido a outra instalação ou ação que bloqueou a instalação

#### <a name="issue"></a>Problema

A seguir, exemplos de mensagens de erro podem ser retornados:

```error
The Microsoft Monitoring Agent failed to install on this machine. Please try to uninstall and reinstall the extension. If the issue persists, please contact support.
```

```error
'Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.4) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.4\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 1618'
```

```error
'Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.2) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.2\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 1601'
```

#### <a name="cause"></a>Causa

Algumas causas possíveis para esse erro são:

* Outra instalação está em andamento.
* O sistema é acionado para reinicializar durante a implantação do modelo.

#### <a name="resolution"></a>Resolução

Este erro é transitório por natureza. Repita a implantação para instalar a extensão.

### <a name="scenario-installation-timeout"></a><a name="installation-timeout"></a> Cenário: tempo limite de instalação

A instalação da extensão Log Analytics for Windows não foi concluída por causa de um intervalo de tempo.

#### <a name="issue"></a>Problema

A seguir, um exemplo de uma mensagem de erro que pode ser devolvida:

```error
Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.4) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.4\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 15614
```

#### <a name="cause"></a>Causa

Esse tipo de erro ocorre porque a VM está sob uma carga pesada durante a instalação.

### <a name="resolution"></a>Resolução

Tente instalar o agente Log Analytics para extensão do Windows quando a VM estiver sob uma carga menor.

## <a name="next-steps"></a>Próximas etapas

Se você não vê o seu problema acima ou não consegue resolver o seu problema, tente um dos seguintes canais para obter suporte adicional:

* Obtenha respostas de especialistas do Azure através [do Azure Forums](https://azure.microsoft.com/support/forums/).
* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport), a conta oficial do Microsoft Azure para melhorar a experiência do cliente conectando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.
* Registrar um incidente de suporte do Azure. Vá ao site de suporte do [Azure](https://azure.microsoft.com/support/options/) e **selecione Obter suporte**.
