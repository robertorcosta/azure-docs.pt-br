---
title: Solução de problemas sobre o embarque de soluções de gerenciamento de automação do Azure
description: Aprenda a solucionar erros de integração com as soluções Gerenciamento de atualizações, Controle de alterações e Inventário
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 05/22/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: ae359e5210a9a11c33dd3ff9b474e28aa2548c57
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536957"
---
# <a name="troubleshoot-errors-when-onboarding-update-management-change-tracking-and-inventory"></a>Solucionando problemas ao embarcar no gerenciamento de atualizações, no rastreamento de alterações e no inventário

Você pode encontrar erros ao integrar soluções como o Gerenciamento de Atualizações ou o Rastreamento de Mudanças e o Inventário. Este artigo descreve os vários erros que podem ocorrer e como resolvê-los.

## <a name="known-issues"></a>Problemas conhecidos

### <a name="scenario-renaming-a-registered-node-requires-unregister--register-again"></a><a name="node-rename"></a>Cenário: Renomear um nó registrado requer não registro/ registro novamente

#### <a name="issue"></a>Problema

Um nó é registrado no Azure Automation e, em seguida, o nome do computador do sistema operacional é alterado.  Os relatórios do nó continuam a aparecer com o nome original.

#### <a name="cause"></a>Causa

Renomear os álos registrados não atualiza o nome do nó no Azure Automation.

#### <a name="resolution"></a>Resolução

Desregistre o nó da configuração do estado de automação do Azure e registre-o novamente.  Relatórios publicados no serviço antes desse horário não estarão mais disponíveis.


### <a name="scenario-re-signing-certificates-via-https-proxy-is-not-supported"></a><a name="resigning-cert"></a>Cenário: Não é suportado certificados de reassinatura via proxy https

#### <a name="issue"></a>Problema

Os clientes relataram que, ao se conectar através de uma solução proxy que encerra o tráfego https e, em seguida, recriptografa o tráfego usando um novo certificado, o serviço não permite a conexão.

#### <a name="cause"></a>Causa

O Azure Automation não suporta re-assinatura de certificados usados para criptografar tráfego.

#### <a name="resolution"></a>Resolução

Não há nenhuma solução alternativa para esse problema.

## <a name="general-errors"></a>Erros gerais

### <a name="scenario-onboarding-fails-with-the-message---the-solution-cannot-be-enabled"></a><a name="missing-write-permissions"></a>Cenário: Onboarding falha com a mensagem - A solução não pode ser habilitada

#### <a name="issue"></a>Problema

Você recebe uma das seguintes mensagens quando tenta embarcar uma máquina virtual para uma solução:

```error
The solution cannot be enabled due to missing permissions for the virtual machine or deployments
```

```error
The solution cannot be enabled on this VM because the permission to read the workspace is missing
```

#### <a name="cause"></a>Causa

Este erro é causado por permissões incorretas ou ausentes na máquina virtual, no espaço de trabalho ou para o usuário.

#### <a name="resolution"></a>Resolução

Verifique se que você tem as permissões corretas para integrar a máquina virtual. Examine as [permissões necessárias para integrar máquinas](../automation-role-based-access-control.md#onboarding-permissions) e tente integrar a solução novamente. Se você receber `The solution cannot be enabled on this VM because the permission to read the workspace is missing`o erro, `Microsoft.OperationalInsights/workspaces/read` certifique-se de que você tem a permissão para ser capaz de encontrar se a VM está a bordo de um espaço de trabalho.

### <a name="scenario-onboarding-fails-with-the-message---failed-to-configure-automation-account-for-diagnostic-logging"></a><a name="diagnostic-logging"></a>Cenário: Onboarding falha com a mensagem - Falha ao configurar conta de automação para registro de diagnóstico

#### <a name="issue"></a>Problema

Você recebe a seguinte mensagem ao tentar integrar uma máquina virtual a uma solução:

```error
Failed to configure automation account for diagnostic logging
```

#### <a name="cause"></a>Causa

Esse erro pode ser causado se a camada de preços não corresponder ao modelo de faturamento da assinatura. Para obter mais informações, consulte [O uso do monitoramento e os custos estimados no Monitor Do Azure](https://aka.ms/PricingTierWarning).

#### <a name="resolution"></a>Resolução

Crie seu espaço de trabalho log analytics manualmente e repita o processo de onboarding para selecionar o espaço de trabalho criado.

### <a name="scenario-computergroupqueryformaterror"></a><a name="computer-group-query-format-error"></a>Cenário: ComputerGroupQueryFormatError

#### <a name="issue"></a>Problema

Esse código de erro significa que a consulta de pesquisa salva referente ao grupo de computadores e usada para a solução de destino não foi formatada corretamente. 

#### <a name="cause"></a>Causa

Você pode ter alterado a consulta ou ela pode ter sido alterada pelo sistema.

#### <a name="resolution"></a>Resolução

Você pode excluir a consulta dessa solução e reintegrar a solução, o que recria a consulta. A consulta pode ser encontrada em seu workspace, em **Pesquisas salvas**. O nome da consulta é **MicrosoftDefaultComputerGroup**, e a categoria da consulta é o nome da solução associada a essa consulta. Se várias soluções estiverem habilitadas, o **MicrosoftDefaultComputerGroup** mostra várias vezes em**Pesquisas salvas**.

### <a name="scenario-policyviolation"></a><a name="policy-violation"></a>Cenário: PolicyViolation

#### <a name="issue"></a>Problema

Esse código de erro significa que a implantação falhou devido à violação de uma ou mais políticas.

#### <a name="cause"></a>Causa 

Uma política está em vigor que está impedindo que a operação seja concluída.

#### <a name="resolution"></a>Resolução

Para implantar a solução com êxito, você precisa considerar alterar a política indicada. Como há muitos tipos diferentes de políticas que podem ser definidas, as alterações específicas necessárias dependem da política violada. Por exemplo, se uma política for definida em um grupo de recursos sem permissão para alterar o conteúdo de certos tipos de recursos nesse grupo de recursos, você pode, por exemplo, fazer qualquer uma das seguintes ações:

* Remover completamente a política.
* Tentar se integrar a um grupo de recursos diferente.
* Revisar a política, por exemplo:
  * Refazer a segmentação da política para um recurso específico (como para uma conta de automação específica).
  * Revisando o conjunto de recursos ao qual a política foi configurada para negar.

Verifique as notificações no canto superior direito do portal Azure ou navegue até o grupo de recursos que contém sua conta de automação e selecione **Implantações** em **Configurações** para exibir a implantação com falha. Para saber mais sobre o Azure Policy, consulte: [Visão geral do Azure Policy](../../governance/policy/overview.md?toc=%2fazure%2fautomation%2ftoc.json).

### <a name="scenario-errors-trying-to-unlink-a-workspace"></a><a name="unlink"></a>Cenário: Erros ao tentar desvincular um espaço de trabalho

#### <a name="issue"></a>Problema

Você recebe o seguinte erro ao tentar desvincular um espaço de trabalho:

```error
The link cannot be updated or deleted because it is linked to Update Management and/or ChangeTracking Solutions.
```

#### <a name="cause"></a>Causa

Esse erro ocorre quando você ainda tem soluções ativas em seu espaço de trabalho do Log Analytics que dependem da vinculação do seu espaço de trabalho de Conta de Automação e Log Analytics.

### <a name="resolution"></a>Resolução

Para resolver isso, você precisará remover as seguintes soluções do seu Espaço de Trabalho se estiver usando-as:

* Gerenciamento de atualizações
* Controle de Alterações
* Iniciar/Parar VMs durante os horários inativos

Depois de remover as soluções, poderá desvincular seu espaço de trabalho. É importante limpar quaisquer artefatos existentes dessas soluções de seu espaço de trabalho e conta de automação também.  

* Gerenciamento de atualizações
  * Remova implantações de atualização (agendas) de sua conta de automação
* Iniciar/Parar VMs durante os horários inativos
  * Remova quaisquer bloqueios nos componentes da solução em sua conta de automação em **Bloqueios de** > **configurações**.
  * Para obter etapas adicionais para remover as VMs Start/Stop durante a solução off-hours, consulte, [Remova a VM Start/Stop durante a solução off-hours](../automation-solution-vm-management.md#remove-the-solution).

## <a name="mma-extension-failures"></a><a name="mma-extension-failures"></a>falhas de extensão do MMA

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 

Ao implantar uma solução, vários recursos relacionados são implantados. Um desses recursos é o Microsoft Monitoring Agent Extension ou o agente do Log Analytics para Linux. Estas são extensões de máquina virtual instaladas pelo Agente Convidado da máquina virtual que é responsável por se comunicar com o espaço de trabalho configurado do Log Analytics, com o propósito de coordenar posteriormente o download de binários e outros arquivos que a solução de que você está embarcando depende uma vez que ele começa a execução.
Em geral, você primeiro toma conhecimento das falhas de instalação do MMA ou do agente do Log Analytics para Linux a partir de uma notificação exibida no Hub de Notificações. Clicar nessa notificação fornece mais informações sobre a falha específica. A navegação para o recurso Grupos de Recursos e, em seguida, para o elemento Deployments dentro dele também fornece detalhes sobre as falhas de implantação que ocorreram.
A instalação do Agente MMA ou do Log Analytics para Linux pode falhar por diversos motivos, e as etapas a tomar para solucionar essas falhas variam, dependendo do problema. Seguem etapas específicas de solução de problemas.

A seção a seguir descreve vários problemas que você pode encontrar ao embarcar que causam uma falha na implantação da extensão do MMA.

### <a name="scenario-an-exception-occurred-during-a-webclient-request"></a><a name="webclient-exception"></a>Cenário: ocorreu uma exceção durante uma solicitação de WebClient

A extensão do MMA na máquina virtual não consegue se comunicar com recursos externos e a implantação falha.

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

* Há um proxy configurado na VM, que só permite portas específicas.

* Uma configuração de firewall bloqueou o acesso às portas e endereços necessários.

#### <a name="resolution"></a>Resolução

Certifique-se de ter as portas e os endereços adequados abertos para comunicação. Para obter uma lista de portas e endereços, consulte [planejando sua rede](../automation-hybrid-runbook-worker.md#network-planning).

### <a name="scenario-install-failed-because-of-a-transient-environment-issues"></a><a name="transient-environment-issue"></a>Cenário: Falha na instalação devido a problemas ambientais transitórios

A instalação da extensão do Microsoft Monitoring Agent falhou durante a implantação devido a outra instalação ou ação que bloqueou a instalação

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

* Outra instalação está em progresso
* O sistema é acionado para reinicializar durante a implantação do modelo

#### <a name="resolution"></a>Resolução

Este erro é um erro transitório na natureza. Repita a implantação para instalar a extensão.

### <a name="scenario-installation-timeout"></a><a name="installation-timeout"></a> Cenário: tempo limite de instalação

A instalação da extensão do MMA não foi concluída por causa de um intervalo.

#### <a name="issue"></a>Problema

O exemplo a seguir é uma mensagem de erro que pode ser devolvida:

```error
Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.4) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.4\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 15614
```

#### <a name="cause"></a>Causa

Esse erro ocorre porque a máquina virtual está sob uma carga pesada durante a instalação.

### <a name="resolution"></a>Resolução

Tente instalar a extensão MMA quando a VM estiver sob uma carga menor.

## <a name="next-steps"></a>Próximas etapas

Se você não encontrou seu problema ou não conseguiu resolver seu problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure por meio de [Fóruns do Azure](https://azure.microsoft.com/support/forums/)
* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) – a conta oficial do Microsoft Azure para melhorar a experiência do cliente conectando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.
* Se precisar de mais ajuda, você pode registrar um incidente de suporte do Azure. Vá ao site de suporte do [Azure](https://azure.microsoft.com/support/options/) e **selecione Obter suporte**.
