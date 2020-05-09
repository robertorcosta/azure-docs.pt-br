---
title: Solucionando problemas de configuração de estado de automação do Azure
description: Este artigo fornece informações sobre como solucionar problemas de configuração de estado da automação do Azure.
services: automation
ms.service: automation
ms.subservice: ''
author: mgoedtel
ms.author: magoedte
ms.date: 04/16/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: d0801bb44fc0c08df1adee1f817e8fccab166fb5
ms.sourcegitcommit: d662eda7c8eec2a5e131935d16c80f1cf298cb6b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82652805"
---
# <a name="troubleshoot-issues-with-azure-automation-state-configuration"></a>Solucionar problemas com a configuração de estado da automação do Azure

Este artigo fornece informações sobre como solucionar problemas que surgem enquanto você compila ou implanta configurações na configuração de estado da automação do Azure.

>[!NOTE]
>Este artigo foi atualizado para usar o novo módulo Az do Azure PowerShell. Você ainda pode usar o módulo AzureRM, que continuará a receber as correções de bugs até pelo menos dezembro de 2020. Para saber mais sobre o novo módulo Az e a compatibilidade com o AzureRM, confira [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para obter instruções de instalação do módulo Az no seu Hybrid Runbook Worker, confira [Instalar o módulo do Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para sua conta de automação, você pode atualizar seus módulos para a versão mais recente seguindo as etapas em [como atualizar os módulos de Azure PowerShell na automação do Azure](../automation-update-azure-modules.md).

## <a name="diagnose-an-issue"></a>Diagnosticar um problema

Quando você recebe um erro de compilação ou implantação para configuração, aqui estão algumas etapas para ajudá-lo a diagnosticar o problema.

### <a name="1-ensure-that-your-configuration-compiles-successfully-on-the-local-machine"></a>1. Certifique-se de que a configuração seja compilada com êxito no computador local

A configuração de estado da automação do Azure é criada na DSC (configuração de estado desejado) do PowerShell. Você pode encontrar a documentação para a linguagem DSC e a sintaxe nos [documentos DSC do PowerShell](https://docs.microsoft.com/powershell/scripting/overview).

Ao compilar uma configuração de DSC em seu computador local, você pode descobrir e resolver erros comuns, como:

   - Módulos ausentes.
   - Erros de sintaxe.
   - Erros lógicos.

### <a name="2-view-dsc-logs-on-your-node"></a>2. exibir logs de DSC em seu nó

Se sua configuração for compilada com êxito, mas falhar quando aplicada a um nó, você poderá encontrar informações detalhadas nos logs de DSC. Para obter informações sobre onde encontrar esses logs, consulte [onde estão os logs de eventos de DSC](/powershell/scripting/dsc/troubleshooting/troubleshooting#where-are-dsc-event-logs).

O módulo [xDscDiagnostics](https://github.com/PowerShell/xDscDiagnostics) pode ajudá-lo a analisar informações detalhadas dos logs de DSC. Se você contatar o suporte, ele exigirá esses logs para diagnosticar seu problema.

Você pode instalar o `xDscDiagnostics` módulo em seu computador local seguindo as instruções em [instalar o módulo de versão estável](https://github.com/PowerShell/xDscDiagnostics#install-the-stable-version-module).

Para instalar o `xDscDiagnostics` módulo em seu computador do Azure, use [Invoke-AzVMRunCommand](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand?view=azps-3.7.0). Você também pode usar a opção **executar comando** no portal do Azure seguindo as etapas em [executar scripts do PowerShell em sua VM do Windows com o comando executar](../../virtual-machines/windows/run-command.md).

Para obter informações sobre como usar o **xDscDiagnostics**, consulte [usando o xDscDiagnostics para analisar os logs de DSC](/powershell/scripting/dsc/troubleshooting/troubleshooting#using-xdscdiagnostics-to-analyze-dsc-logs). Consulte também [cmdlets xDscDiagnostics](https://github.com/PowerShell/xDscDiagnostics#cmdlets).

### <a name="3-ensure-that-nodes-and-the-automation-workspace-have-required-modules"></a>3. Verifique se os nós e o espaço de trabalho de automação têm módulos necessários

A DSC depende dos módulos instalados no nó. Ao usar a configuração de estado da automação do Azure, importe os módulos necessários para sua conta de automação seguindo as etapas em [Importar módulos](../shared-resources/modules.md#import-modules). As configurações também podem ter uma dependência em versões específicas de módulos. Para obter mais informações, consulte [solucionar problemas de módulos](shared-resources.md#modules).

## <a name="scenario-a-configuration-with-special-characters-cant-be-deleted-from-the-portal"></a><a name="unsupported-characters"></a>Cenário: uma configuração com caracteres especiais não pode ser excluída do portal

### <a name="issue"></a>Problema

Ao tentar excluir uma configuração DSC do portal, você verá o seguinte erro:

```error
An error occurred while deleting the DSC configuration '<name>'.  Error-details: The argument configurationName with the value <name> is not valid.  Valid configuration names can contain only letters,  numbers, and underscores.  The name must start with a letter.  The length of the name must be between 1 and 64 characters.
```

### <a name="cause"></a>Causa

Esse erro é um problema temporário que está planejado para ser resolvido.

### <a name="resolution"></a>Resolução

Use o cmdlet [remove-AzAutomationDscConfiguration]https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationDscConfiguration?view=azps-3.7.0 (para excluir a configuração.

## <a name="scenario-failed-to-register-the-dsc-agent"></a><a name="failed-to-register-agent"></a>Cenário: falha ao registrar o agente DSC

### <a name="issue"></a>Problema

Quando [set-DscLocalConfigurationManager](https://docs.microsoft.com/powershell/module/psdesiredstateconfiguration/set-dsclocalconfigurationmanager?view=powershell-5.1) ou outro cmdlet de DSC, você receberá o erro:

```error
Registration of the Dsc Agent with the server
https://<location>-agentservice-prod-1.azure-automation.net/accounts/00000000-0000-0000-0000-000000000000 failed. The
underlying error is: Failed to register Dsc Agent with AgentId 00000000-0000-0000-0000-000000000000 with the server htt
ps://<location>-agentservice-prod-1.azure-automation.net/accounts/00000000-0000-0000-0000-000000000000/Nodes(AgentId='00000000-0000-0000-0000-000000000000'). .
    + CategoryInfo          : InvalidResult: (root/Microsoft/...gurationManager:String) [], CimException
    + FullyQualifiedErrorId : RegisterDscAgentCommandFailed,Microsoft.PowerShell.DesiredStateConfiguration.Commands.Re
   gisterDscAgentCommand
    + PSComputerName        : <computerName>
```

### <a name="cause"></a>Causa

Esse erro normalmente é causado por um firewall, o computador está atrás de um servidor proxy ou outros erros de rede.

### <a name="resolution"></a>Resolução

Verifique se seu computador tem acesso aos pontos de extremidade apropriados para DSC e tente novamente. Para obter uma lista de portas e endereços necessários, consulte [planejamento de rede](../automation-dsc-overview.md#network-planning).

## <a name="a-nameunauthorizedscenario-status-reports-return-the-response-code-unauthorized"></a><a name="unauthorized"><a/>Cenário: relatórios de status retornam o código de resposta não autorizado

### <a name="issue"></a>Problema

Ao registrar um nó com a configuração de estado da automação do Azure, você recebe uma das seguintes mensagens de erro:

```error
The attempt to send status report to the server https://{your Automation account URL}/accounts/xxxxxxxxxxxxxxxxxxxxxx/Nodes(AgentId='xxxxxxxxxxxxxxxxxxxxxxxxx')/SendReport returned unexpected response code Unauthorized.
```

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC / Registration of the Dsc Agent with the server failed.
```

### <a name="cause"></a>Causa

Esse problema é causado por um certificado insatisfatório ou expirado. Consulte [expiração e registro de certificado](../automation-dsc-onboarding.md#re-registering-a-node).

Esse problema também pode ser causado por uma configuração de proxy que não permite o acesso a ***. Azure-Automation.net**. Para obter mais informações, consulte [configuração de redes privadas](../automation-dsc-overview.md#network-planning). 

### <a name="resolution"></a>Resolução

Use as etapas a seguir para registrar novamente o nó de DSC com falha.

#### <a name="step-1-unregister-the-node"></a>Etapa 1: cancelar o registro do nó

1. Na portal do Azure, acesse **página inicial** > **contas de automação** > (sua conta de automação) > **configuração de estado (DSC)**.
1. Selecione **nós**e selecione o nó que está tendo problemas.
1. Selecione **Cancelar registro** para cancelar o registro do nó.

#### <a name="step-2-uninstall-the-dsc-extension-from-the-node"></a>Etapa 2: desinstalar a extensão de DSC do nó

1. Na portal do Azure, acesse a **Home** > **máquina virtual** inicial > (nó com falha) > **extensões**.
1. Selecione **Microsoft. PowerShell. DSC**, a extensão de DSC do PowerShell.
1. Selecione **desinstalar** para desinstalar a extensão.

#### <a name="step-3-remove-all-bad-or-expired-certificates-from-the-node"></a>Etapa 3: remover todos os certificados inválidos ou expirados do nó

No nó com falha em um prompt do PowerShell com privilégios elevados, execute estes comandos:

```powershell
$certs = @()
$certs += dir cert:\localmachine\my | ?{$_.FriendlyName -like "DSC"}
$certs += dir cert:\localmachine\my | ?{$_.FriendlyName -like "DSC-OaaS Client Authentication"}
$certs += dir cert:\localmachine\CA | ?{$_.subject -like "CN=AzureDSCExtension*"}
"";"== DSC Certificates found: " + $certs.Count
$certs | FL ThumbPrint,FriendlyName,Subject
If (($certs.Count) -gt 0)
{ 
    ForEach ($Cert in $certs) 
    {
        RD -LiteralPath ($Cert.Pspath) 
    }
}
```

#### <a name="step-4-reregister-the-failing-node"></a>Etapa 4: registrar novamente o nó com falha

1. Na portal do Azure, acesse **página inicial** > **contas de automação** > (sua conta de automação) > **configuração de estado (DSC)**.
1. Selecione **nós**.
1. Selecione **Adicionar**.
1. Selecione o nó com falha.
1. Selecione **conectar**e selecione as opções desejadas.

## <a name="scenario-node-is-in-failed-status-with-a-not-found-error"></a><a name="failed-not-found"></a>Cenário: o nó está com um status de falha e um erro “Não encontrado”

### <a name="issue"></a>Problema

O nó tem um relatório com status de falha e contém o erro:

```error
The attempt to get the action from server https://<url>//accounts/<account-id>/Nodes(AgentId=<agent-id>)/GetDscAction failed because a valid configuration <guid> cannot be found.
```

### <a name="cause"></a>Causa

Esse erro normalmente ocorre quando o nó é atribuído a um nome de configuração, por exemplo, **ABC**, em vez de um nome de configuração de nó (arquivo MOF), por exemplo, **ABC. Servidor**da Web.

### <a name="resolution"></a>Resolução

* Certifique-se de que você está atribuindo o nó com o nome de configuração do nó e não o nome da configuração.
* Você pode atribuir uma configuração de nó a um nó usando o portal do Azure ou com um cmdlet do PowerShell.

  * Na portal do Azure, acesse **página inicial** > **contas de automação** > (sua conta de automação) > **configuração de estado (DSC)**. Em seguida, selecione um nó e selecione **atribuir configuração de nó**.
  * Use o cmdlet [set-AzAutomationDscNode](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationDscNode?view=azps-3.7.0) .

## <a name="scenario-no-node-configurations-mof-files-were-produced-when-a-configuration-was-compiled"></a><a name="no-mof-files"></a>Cenário: não foram produzidas configurações de nó (Arquivos MOF) quando uma configuração foi compilada

### <a name="issue"></a>Problema

Seu trabalho de compilação do DSC é suspenso com o erro:

```error
Compilation completed successfully, but no node configuration **.mof** files were generated.
```

### <a name="cause"></a>Causa

Quando a expressão após a `Node` palavra-chave na configuração DSC é avaliada como, nenhuma configuração de `$null`nó é produzida.

### <a name="resolution"></a>Resolução

Use uma das soluções a seguir para corrigir o problema:

* Verifique se a expressão ao lado da `Node` palavra-chave na definição de configuração não está avaliando como nula.
* Se você estiver passando [ConfigurationData](../automation-dsc-compile.md) ao compilar a configuração, certifique-se de que você está passando os valores que a configuração espera dos dados de configuração.

## <a name="scenario-the-dsc-node-report-becomes-stuck-in-the-in-progress-state"></a><a name="dsc-in-progress"></a>Cenário: o relatório do nó DSC fica preso no estado em andamento

### <a name="issue"></a>Problema

O agente DSC produz:

```error
No instance found with given property values
```

### <a name="cause"></a>Causa

Você atualizou sua versão do Windows Management Framework (WMF) e danificou Instrumentação de Gerenciamento do Windows (WMI).

### <a name="resolution"></a>Resolução

Siga as instruções em [problemas conhecidos e limitações do DSC](https://docs.microsoft.com/powershell/scripting/wmf/known-issues/known-issues-dsc).

## <a name="scenario-unable-to-use-a-credential-in-a-dsc-configuration"></a><a name="issue-using-credential"></a>Cenário: não é possível usar uma credencial em uma configuração DSC

### <a name="issue"></a>Problema

Seu trabalho de compilação DSC foi suspenso com o erro:

```error
System.InvalidOperationException error processing property 'Credential' of type <some resource name>: Converting and storing an encrypted password as plaintext is allowed only if PSDscAllowPlainTextPassword is set to true.
```

### <a name="cause"></a>Causa

Você usou uma credencial em uma configuração, mas não forneceu `ConfigurationData` adequado `PSDscAllowPlainTextPassword` para definir como true para cada configuração de nó.

### <a name="resolution"></a>Resolução

Certifique-se de transmitir o apropriado `ConfigurationData` para definir `PSDscAllowPlainTextPassword` como true para cada configuração de nó mencionada na configuração. Consulte [compilando configurações de DSC na configuração de estado da automação do Azure](../automation-dsc-compile.md).

## <a name="scenario-failure-processing-extension-error-when-onboarding-from-a-dsc-extension"></a><a name="failure-processing-extension"></a>Cenário: erro de "extensão de processamento de falha" ao realizar a integração de uma extensão de DSC

### <a name="issue"></a>Problema

Quando você se integra usando uma extensão de DSC, ocorre uma falha que contém o erro:

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC'. Error message: \"DSC COnfiguration 'RegistrationMetaConfigV2' completed with error(s). Following are the first few: Registration of the Dsc Agent with the server <url> failed. The underlying error is: The attempt to register Dsc Agent with Agent Id <ID> with the server <url> return unexpected response code BadRequest. .\".
```

### <a name="cause"></a>Causa

Esse erro normalmente ocorre quando o nó recebe um nome de configuração de nó que não existe no serviço.

### <a name="resolution"></a>Resolução

* Certifique-se de que você está atribuindo o nó com um nome que corresponda exatamente ao nome no serviço.
* Você pode optar por não incluir o nome da configuração do nó, o que resulta na integração do nó, mas não à atribuição de uma configuração de nó.

## <a name="scenario-one-or-more-errors-occurred-error-when-registering-a-node-by-using-powershell"></a><a name="cross-subscription"></a>Cenário: erro "ocorreu um ou mais erros" ao registrar um nó usando o PowerShell

### <a name="issue"></a>Problema

Ao registrar um nó usando [Register-AzAutomationDSCNode](https://docs.microsoft.com/powershell/module/az.automation/register-azautomationdscnode?view=azps-3.7.0) ou [Register-AzureRMAutomationDSCNode](https://docs.microsoft.com/powershell/module/azurerm.automation/register-azurermautomationdscnode?view=azurermps-6.13.0), você receberá o seguinte erro:

```error
One or more errors occurred.
```

### <a name="cause"></a>Causa

Esse erro ocorre quando você tenta registrar um nó em uma assinatura separada do que é usada pela conta de automação.

### <a name="resolution"></a>Resolução

Trate o nó de assinatura cruzada como se ele estivesse definido para uma nuvem separada ou localmente. Registre o nó usando uma dessas opções de integração:

* Windows: [máquinas físicas/virtuais do Windows locais ou em uma nuvem diferente do Azure/AWS](../automation-dsc-onboarding.md#onboarding-physicalvirtual-windows-machines).
* Linux: [máquinas Linux físicas/virtuais locais ou em uma nuvem diferente do Azure](../automation-dsc-onboarding.md#onboarding-physicalvirtual-linux-machines).

## <a name="scenario-provisioning-has-failed-error-message"></a><a name="agent-has-a-problem"></a>Cenário: mensagem de erro "o provisionamento falhou"

### <a name="issue"></a>Problema

Ao registrar um nó, você verá o erro:

```error
Provisioning has failed
```

### <a name="cause"></a>Causa

Essa mensagem ocorre quando há um problema com a conectividade entre o nó e o Azure.

### <a name="resolution"></a>Resolução

Determine se o nó está em uma VPN (rede virtual privada) ou se há outros problemas para se conectar ao Azure. Consulte [solucionar erros ao realizar soluções de integração](onboarding.md).

## <a name="scenario-failure-with-a-general-error-when-applying-a-configuration-in-linux"></a><a name="failure-linux-temp-noexec"></a>Cenário: falha com um erro geral ao aplicar uma configuração no Linux

### <a name="issue"></a>Problema

Quando você aplica uma configuração no Linux, ocorre uma falha que contém o erro:

```error
This event indicates that failure happens when LCM is processing the configuration. ErrorId is 1. ErrorDetail is The SendConfigurationApply function did not succeed.. ResourceId is [resource]name and SourceInfo is ::nnn::n::resource. ErrorMessage is A general error occurred, not covered by a more specific error code..
```

### <a name="cause"></a>Causa

Se o local **/tmp** for definido como `noexec`, a versão atual do DSC falhará ao aplicar as configurações.

### <a name="resolution"></a>Resolução

Remova a `noexec` opção do local **/tmp** .

## <a name="scenario-node-configuration-names-that-overlap-can-result-in-a-bad-release"></a><a name="compilation-node-name-overlap"></a>Cenário: nomes de configuração de nó que se sobrepõem podem resultar em uma versão incorreta

### <a name="issue"></a>Problema

Quando você usa um único script de configuração para gerar várias configurações de nó e alguns nomes de configuração de nó são subconjuntos de outros nomes, o serviço de compilação pode acabar atribuindo a configuração errada. Esse problema ocorre apenas quando você usa um único script para gerar configurações com dados de configuração por nó e somente quando a sobreposição de nome ocorre no início da cadeia de caracteres. Um exemplo é um script de configuração única usado para gerar configurações com base nos dados do nó passados como uma tabela de hash usando cmdlets e os dados do nó incluem servidores nomeados **Server** e **1server**.

### <a name="cause"></a>Causa

Esse é um problema conhecido com o serviço de compilação.

### <a name="resolution"></a>Resolução

A melhor solução é compilar localmente ou em um pipeline de CI/CD e carregar os arquivos MOF de configuração de nó diretamente para o serviço. Se a compilação no serviço for um requisito, a próxima solução alternativa é dividir os trabalhos de compilação para que não haja sobreposição em nomes.

## <a name="scenario-gateway-timeout-error-on-dsc-configuration-upload"></a><a name="gateway-timeout"></a>Cenário: erro de tempo limite do gateway no carregamento da configuração DSC

#### <a name="issue"></a>Problema

Você recebe um `GatewayTimeout` erro ao carregar uma configuração DSC. 

### <a name="cause"></a>Causa

As configurações DSC que levam muito tempo para serem compiladas podem causar esse erro.

### <a name="resolution"></a>Resolução

Você pode fazer com que suas configurações de DSC sejam analisadas `ModuleName` mais rapidamente, incluindo explicitamente o parâmetro para quaisquer chamadas [Import-DSCResource](https://docs.microsoft.com/powershell/scripting/dsc/configurations/import-dscresource?view=powershell-5.1) .

## <a name="next-steps"></a>Próximas etapas

Se você não encontrar seu problema aqui ou não puder resolver o problema, tente um dos seguintes canais para obter suporte adicional:

* Obtenha respostas de especialistas do Azure por meio dos [fóruns do Azure](https://azure.microsoft.com/support/forums/).
* Conecte- [@AzureSupport](https://twitter.com/azuresupport)se com o, a conta de Microsoft Azure oficial para melhorar a experiência do cliente. O suporte do Azure conecta a Comunidade do Azure a respostas, suporte e especialistas.
* Registrar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/)e selecione **obter suporte**.
