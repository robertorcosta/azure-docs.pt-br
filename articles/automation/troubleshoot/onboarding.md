---
title: Solucionar problemas de integração das soluções de gerenciamento de automação do Azure
description: Aprenda a solucionar erros de integração com as soluções Gerenciamento de atualizações, Controle de alterações e Inventário
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 05/22/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 737b963074a2bec851882bddd78ad0b89f48d1d9
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75769890"
---
# <a name="troubleshoot-errors-when-onboarding-update-management-change-tracking-and-inventory"></a>Solucionar erros ao realizar a integração de Gerenciamento de Atualizações, Controle de Alterações e inventário

Você pode encontrar erros ao integrar soluções como o Gerenciamento de Atualizações ou o Rastreamento de Mudanças e o Inventário. Este artigo descreve os vários erros que podem ocorrer e como resolvê-los.

## <a name="known-issues"></a>Problemas conhecidos

### <a name="node-rename"></a>Cenário: renomear um nó registrado requer cancelar o registro/registro novamente

#### <a name="issue"></a>Problema

Um nó é registrado na automação do Azure e, em seguida, o sistema operacional ComputerName é alterado.  Os relatórios do nó continuam a aparecer com o nome original.

#### <a name="cause"></a>Causa

Renomear nós registrados não atualiza o nome do nó na automação do Azure.

#### <a name="resolution"></a>Resolução

Cancele o registro do nó da configuração de estado da automação do Azure e registre-o novamente.  Os relatórios publicados no serviço antes dessa hora não estarão mais disponíveis.


### <a name="resigning-cert"></a>Cenário: não há suporte para assinar certificados novamente por meio do proxy HTTPS

#### <a name="issue"></a>Problema

Os clientes relataram que, ao se conectar por meio de uma solução de proxy que encerra o tráfego HTTPS e, em seguida, criptografa novamente o tráfego usando um novo certificado, o serviço não permite a conexão.

#### <a name="cause"></a>Causa

A automação do Azure não dá suporte à assinatura de certificados usados para criptografar o tráfego.

#### <a name="resolution"></a>Resolução

Não há nenhuma solução alternativa para esse problema.

## <a name="general-errors"></a>Erros gerais

### <a name="missing-write-permissions"></a>Cenário: a integração falha com a mensagem-a solução não pode ser habilitada

#### <a name="issue"></a>Problema

Você recebe uma das seguintes mensagens ao tentar carregar uma máquina virtual em uma solução:

```error
The solution cannot be enabled due to missing permissions for the virtual machine or deployments
```

```error
The solution cannot be enabled on this VM because the permission to read the workspace is missing
```

#### <a name="cause"></a>Causa

Esse erro é causado por permissões incorretas ou ausentes na máquina virtual, no espaço de trabalho ou no usuário.

#### <a name="resolution"></a>Resolução

Verifique se que você tem as permissões corretas para integrar a máquina virtual. Examine as [permissões necessárias para integrar máquinas](../automation-role-based-access-control.md#onboarding) e tente integrar a solução novamente. Se você receber o erro `The solution cannot be enabled on this VM because the permission to read the workspace is missing`, verifique se você tem a permissão `Microsoft.OperationalInsights/workspaces/read` para poder encontrar se a VM está integrada a um espaço de trabalho.

### <a name="diagnostic-logging"></a>Cenário: a integração falha com a mensagem-falha ao configurar a conta de automação para o log de diagnóstico

#### <a name="issue"></a>Problema

Você recebe a seguinte mensagem ao tentar integrar uma máquina virtual a uma solução:

```error
Failed to configure automation account for diagnostic logging
```

#### <a name="cause"></a>Causa

Esse erro pode ser causado se o tipo de preço não corresponder ao modelo de cobrança da assinatura. Para obter mais informações, consulte [monitoramento de uso e custos estimados em Azure monitor](https://aka.ms/PricingTierWarning).

#### <a name="resolution"></a>Resolução

Crie seu espaço de trabalho do Log Analytics manualmente e repita o processo de integração para selecionar o espaço de trabalho criado.

### <a name="computer-group-query-format-error"></a>Cenário: ComputerGroupQueryFormatError

#### <a name="issue"></a>Problema

Esse código de erro significa que a consulta de pesquisa salva referente ao grupo de computadores e usada para a solução de destino não foi formatada corretamente. 

#### <a name="cause"></a>Causa

Você pode ter alterado a consulta ou ela pode ter sido alterada pelo sistema.

#### <a name="resolution"></a>Resolução

Você pode excluir a consulta dessa solução e reintegrar a solução, o que recria a consulta. A consulta pode ser encontrada em seu workspace, em **Pesquisas salvas**. O nome da consulta é **MicrosoftDefaultComputerGroup**, e a categoria da consulta é o nome da solução associada a essa consulta. Se várias soluções estiverem habilitadas, o **MicrosoftDefaultComputerGroup** mostra várias vezes em**Pesquisas salvas**.

### <a name="policy-violation"></a>Cenário: PolicyViolation

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

Verifique as notificações no canto superior direito do portal do Azure ou navegue até o grupo de recursos que contém sua conta de automação e selecione **implantações** em **configurações** para exibir a implantação com falha. Para saber mais sobre o Azure Policy, consulte: [Visão geral do Azure Policy](../../governance/policy/overview.md?toc=%2fazure%2fautomation%2ftoc.json).

### <a name="unlink"></a>Cenário: erros ao tentar desvincular um espaço de trabalho

#### <a name="issue"></a>Problema

Você recebe o seguinte erro ao tentar desvincular um espaço de trabalho:

```error
The link cannot be updated or deleted because it is linked to Update Management and/or ChangeTracking Solutions.
```

#### <a name="cause"></a>Causa

Esse erro ocorre quando você ainda tem soluções ativas em seu espaço de trabalho Log Analytics que dependem de sua conta de automação e Log Analytics espaço de trabalho que está sendo vinculado.

### <a name="resolution"></a>Resolução

Para resolver isso, você precisará remover as seguintes soluções do seu espaço de trabalho se você as estiver usando:

* Gerenciamento de atualização
* Alterar acompanhamento
* Inicie/pare VMs durante os horários inativos

Depois de remover as soluções, você pode desvincular seu espaço de trabalho. É importante limpar todos os artefatos existentes dessas soluções do seu espaço de trabalho e conta de automação também.  

* Gerenciamento de atualização
  * Remover implantações de atualização (agendas) de sua conta de automação
* Inicie/pare VMs durante os horários inativos
  * Remova os bloqueios nos componentes da solução em sua conta de automação em **configurações** > **bloqueios**.
  * Para obter etapas adicionais para remover a solução Iniciar/Parar VMs fora do horário comercial consulte, [remova a solução iniciar/parar VM fora do horário comercial](../automation-solution-vm-management.md##remove-the-solution).

## <a name="mma-extension-failures"></a>falhas de extensão do MMA

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 

Ao implantar uma solução, vários recursos relacionados são implantados. Um desses recursos é o Microsoft Monitoring Agent Extension ou o agente do Log Analytics para Linux. Essas são extensões de máquina virtual instaladas pelo agente convidado da máquina virtual que é responsável por se comunicar com o espaço de trabalho Log Analytics configurado, para a finalidade de coordenação posterior do download de binários e outros arquivos que o a solução que você está integrando depende de quando começa a execução.
Em geral, você primeiro toma conhecimento das falhas de instalação do MMA ou do agente do Log Analytics para Linux a partir de uma notificação exibida no Hub de Notificações. Clicar nessa notificação fornece mais informações sobre a falha específica. A navegação para o recurso Grupos de Recursos e, em seguida, para o elemento Deployments dentro dele também fornece detalhes sobre as falhas de implantação que ocorreram.
A instalação do Agente MMA ou do Log Analytics para Linux pode falhar por diversos motivos, e as etapas a tomar para solucionar essas falhas variam, dependendo do problema. Seguem etapas específicas de solução de problemas.

A seção a seguir descreve vários problemas que você pode percorrer ao realizar a integração que causa uma falha na implantação da extensão MMA.

### <a name="webclient-exception"></a>Cenário: ocorreu uma exceção durante uma solicitação de WebClient

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

* Há um proxy configurado na VM, que permite apenas portas específicas.

* Uma configuração de firewall bloqueou o acesso às portas e endereços necessários.

#### <a name="resolution"></a>Resolução

Certifique-se de ter as portas e os endereços adequados abertos para comunicação. Para obter uma lista de portas e endereços, consulte [planejando sua rede](../automation-hybrid-runbook-worker.md#network-planning).

### <a name="transient-environment-issue"></a>Cenário: falha na instalação devido a problemas de um ambiente transitório

Falha na instalação da extensão de Microsoft Monitoring Agent durante a implantação devido a outra instalação ou ação que está bloqueando a instalação

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
* O sistema é disparado para reinicialização durante a implantação do modelo

#### <a name="resolution"></a>Resolução

Este erro é um erro transitório na natureza. Repita a implantação para instalar a extensão.

### <a name="installation-timeout"></a> Cenário: tempo limite de instalação

A instalação da extensão MMA não foi concluída devido a um tempo limite.

#### <a name="issue"></a>Problema

O exemplo a seguir é de uma mensagem de erro que pode ser retornada:

```error
Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.4) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.4\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 15614
```

#### <a name="cause"></a>Causa

Esse erro ocorre porque a máquina virtual está sob uma carga pesada durante a instalação.

### <a name="resolution"></a>Resolução

Tente instalar a extensão MMA quando a VM estiver sob uma carga menor.

## <a name="next-steps"></a>Próximos passos

Se você não encontrou seu problema ou não conseguiu resolver seu problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure por meio de [Fóruns do Azure](https://azure.microsoft.com/support/forums/)
* Conecte-se a [@AzureSupport](https://twitter.com/azuresupport) – a conta oficial do Microsoft Azure para melhorar a experiência do cliente conectando-se à comunidade do Azure para os recursos certos: respostas, suporte e especialistas.
* Se precisar de mais ajuda, você pode registrar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione **Obter Suporte**.
