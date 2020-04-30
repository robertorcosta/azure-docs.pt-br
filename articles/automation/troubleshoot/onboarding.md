---
title: Solucionando problemas de integração das soluções de gerenciamento de automação do Azure
description: Saiba como solucionar erros de integração da solução.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 05/22/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: da5152b459f54cbaae5ec168f103f23a237edebd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81679226"
---
# <a name="troubleshoot-solution-onboarding"></a>Solução de problemas de integração da solução

Você pode receber erros ao integrar a solução de Gerenciamento de Atualizações ou a solução de Controle de Alterações e de inventário. Este artigo descreve os vários erros que podem ocorrer e como resolvê-los.

## <a name="known-issues"></a>Problemas conhecidos

### <a name="scenario-renaming-a-registered-node-requires-unregister-or-register-again"></a><a name="node-rename"></a>Cenário: renomear um nó registrado requer cancelar o registro ou registrar novamente

#### <a name="issue"></a>Problema

Um nó é registrado na automação do Azure e, em seguida, o nome do computador do sistema operacional é alterado. Os relatórios do nó continuam a aparecer com o nome original.

#### <a name="cause"></a>Causa

Renomear nós registrados não atualiza o nome do nó na automação do Azure.

#### <a name="resolution"></a>Resolução

Cancele o registro do nó da configuração de estado da automação do Azure e registre-o novamente. Os relatórios publicados no serviço antes dessa hora não estarão mais disponíveis.

### <a name="scenario-re-signing-certificates-via-https-proxy-is-not-supported"></a><a name="resigning-cert"></a>Cenário: não há suporte para assinar certificados novamente por meio do proxy HTTPS

#### <a name="issue"></a>Problema

Ao se conectar por meio de uma solução de proxy que termina o tráfego HTTPS e, em seguida, criptografa novamente o tráfego usando um novo certificado, o serviço não permite a conexão.

#### <a name="cause"></a>Causa

A automação do Azure não dá suporte à assinatura de certificados usados para criptografar o tráfego.

#### <a name="resolution"></a>Resolução

Atualmente, não há nenhuma solução alternativa para esse problema.

## <a name="general-errors"></a>Erros gerais

### <a name="scenario-onboarding-fails-with-the-message---the-solution-cannot-be-enabled"></a><a name="missing-write-permissions"></a>Cenário: a integração falha com a mensagem-a solução não pode ser habilitada

#### <a name="issue"></a>Problema

Você recebe uma das seguintes mensagens quando tenta carregar uma VM para uma solução:

```error
The solution cannot be enabled due to missing permissions for the virtual machine or deployments
```

```error
The solution cannot be enabled on this VM because the permission to read the workspace is missing
```

#### <a name="cause"></a>Causa

Esse erro é causado por permissões incorretas ou ausentes na VM ou no espaço de trabalho ou para o usuário.

#### <a name="resolution"></a>Resolução

Verifique se você tem [as permissões corretas necessárias para carregar computadores](../automation-role-based-access-control.md#onboarding-permissions) e tente realizar a integração da solução novamente. Se você receber o erro `The solution cannot be enabled on this VM because the permission to read the workspace is missing`, certifique-se de que `Microsoft.OperationalInsights/workspaces/read` você tem permissão para encontrar se a VM está integrada a um espaço de trabalho.

### <a name="scenario-onboarding-fails-with-the-message-failed-to-configure-automation-account-for-diagnostic-logging"></a><a name="diagnostic-logging"></a>Cenário: a integração falha com a mensagem: falha ao configurar a conta de automação para o log de diagnóstico

#### <a name="issue"></a>Problema

Você recebe a seguinte mensagem quando tenta carregar uma VM para uma solução:

```error
Failed to configure automation account for diagnostic logging
```

#### <a name="cause"></a>Causa

Esse erro pode ser causado se o tipo de preço não corresponder ao modelo de cobrança da assinatura. Consulte [monitoramento de uso e custos estimados em Azure monitor](https://aka.ms/PricingTierWarning).

#### <a name="resolution"></a>Resolução

Crie seu espaço de trabalho do Log Analytics manualmente e repita o processo de integração para selecionar o espaço de trabalho criado.

### <a name="scenario-computergroupqueryformaterror"></a><a name="computer-group-query-format-error"></a>Cenário: ComputerGroupQueryFormatError

#### <a name="issue"></a>Problema

Esse código de erro significa que a consulta de grupo de computadores de pesquisa salva usada para direcionar a solução não está formatada corretamente. 

#### <a name="cause"></a>Causa

Você pode ter alterado a consulta ou o sistema pode tê-la alterado.

#### <a name="resolution"></a>Resolução

Você pode excluir a consulta para a solução e, em seguida, integrar a solução novamente, o que recriará a consulta. A consulta pode ser encontrada em seu espaço de trabalho, em **pesquisas salvas**. O nome da consulta é **MicrosoftDefaultComputerGroup**e a categoria da consulta é o nome da solução associada. Se várias soluções estiverem habilitadas, a consulta **MicrosoftDefaultComputerGroup** mostrará várias vezes em **pesquisas salvas**.

### <a name="scenario-policyviolation"></a><a name="policy-violation"></a>Cenário: PolicyViolation

#### <a name="issue"></a>Problema

Esse código de erro indica que a implantação falhou devido a uma violação de uma ou mais políticas.

#### <a name="cause"></a>Causa 

Uma política está impedindo a conclusão da operação.

#### <a name="resolution"></a>Resolução

Para implantar a solução com êxito, você deve considerar alterar a política indicada. Como há muitos tipos diferentes de políticas que podem ser definidas, as alterações necessárias dependem da política violada. Por exemplo, se uma política for definida em um grupo de recursos que nega permissão para alterar o conteúdo de alguns recursos contidos, você poderá escolher uma destas correções:

* Remover completamente a política.
* Tente carregar a solução para um grupo de recursos diferente.
* Redirecione a política para um recurso específico, por exemplo, uma conta de automação.
* Revisar o conjunto de recursos que a política está configurada para negar.

Verifique as notificações no canto superior direito do portal do Azure ou navegue até o grupo de recursos que contém sua conta de automação e selecione **implantações** em **configurações** para exibir a implantação com falha. Para saber mais sobre Azure Policy, consulte [visão geral do Azure Policy](../../governance/policy/overview.md?toc=%2fazure%2fautomation%2ftoc.json).

### <a name="scenario-errors-trying-to-unlink-a-workspace"></a><a name="unlink"></a>Cenário: erros ao tentar desvincular um espaço de trabalho

#### <a name="issue"></a>Problema

Você recebe o seguinte erro ao tentar desvincular um espaço de trabalho:

```error
The link cannot be updated or deleted because it is linked to Update Management and/or ChangeTracking Solutions.
```

#### <a name="cause"></a>Causa

Esse erro ocorre quando você ainda tem soluções ativas em seu espaço de trabalho Log Analytics que dependem de sua conta de automação e Log Analytics espaço de trabalho que está sendo vinculado.

### <a name="resolution"></a>Resolução

Remova as seguintes soluções do seu espaço de trabalho se você as estiver usando:

* Gerenciamento de atualizações
* Controle de Alterações e Inventário
* Iniciar/Parar VMs durante os horários inativos

Depois de remover as soluções, você pode desvincular seu espaço de trabalho. É importante limpar todos os artefatos existentes dessas soluções do seu espaço de trabalho e da sua conta de automação 

* Para Gerenciamento de Atualizações, remova as implantações de atualização (agendas) da sua conta de automação.
* Para iniciar/parar VMs fora do horário comercial, remova os bloqueios nos componentes da solução em sua conta de automação em**bloqueios**de **configurações** > . Consulte [remover a solução de iniciar/parar VMs fora do horário comercial](../automation-solution-vm-management.md#remove-the-solution).

## <a name="log-analytics-for-windows-extension-failures"></a><a name="mma-extension-failures"></a>Log Analytics para falhas de extensão do Windows

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 

Uma instalação do agente do Log Analytics para extensão do Windows pode falhar por vários motivos. A seção a seguir descreve os problemas de integração que podem causar falhas durante a implantação da extensão do agente de Log Analytics para Windows.

>[!NOTE]
>Log Analytics Agent para Windows é o nome usado atualmente na automação do Azure para o Microsoft Monitoring Agent (MMA).

### <a name="scenario-an-exception-occurred-during-a-webclient-request"></a><a name="webclient-exception"></a>Cenário: ocorreu uma exceção durante uma solicitação de WebClient

O Log Analytics para extensão do Windows na VM não é capaz de se comunicar com recursos externos e a implantação falha.

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

### <a name="scenario-install-failed-because-of-a-transient-environment-issues"></a><a name="transient-environment-issue"></a>Cenário: falha na instalação devido a problemas de um ambiente transitório

Falha na instalação da extensão do Log Analytics para Windows durante a implantação devido a outra instalação ou ação que está bloqueando a instalação

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
* O sistema é disparado para reinicialização durante a implantação do modelo.

#### <a name="resolution"></a>Resolução

Esse erro é transitório por natureza. Repita a implantação para instalar a extensão.

### <a name="scenario-installation-timeout"></a><a name="installation-timeout"></a> Cenário: tempo limite de instalação

A instalação do Log Analytics para a extensão do Windows não foi concluída devido a um tempo limite.

#### <a name="issue"></a>Problema

Veja a seguir um exemplo de uma mensagem de erro que pode ser retornada:

```error
Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.4) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.4\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 15614
```

#### <a name="cause"></a>Causa

Esse tipo de erro ocorre porque a VM está sob uma carga pesada durante a instalação.

### <a name="resolution"></a>Resolução

Tente instalar a extensão do agente de Log Analytics para Windows quando a VM estiver sob uma carga inferior.

## <a name="next-steps"></a>Próximas etapas

Se você não encontrar o problema acima ou não conseguir resolver o problema, tente um dos seguintes canais para obter suporte adicional:

* Obtenha respostas de especialistas do Azure por meio dos [fóruns do Azure](https://azure.microsoft.com/support/forums/).
* Conecte- [@AzureSupport](https://twitter.com/azuresupport)se com o, a conta de Microsoft Azure oficial para melhorar a experiência do cliente conectando a Comunidade do Azure aos recursos certos: respostas, suporte e especialistas.
* Registrar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione **obter suporte**.
