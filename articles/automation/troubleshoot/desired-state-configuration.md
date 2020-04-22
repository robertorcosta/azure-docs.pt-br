---
title: Solução de problemas Configuração do estado de automação do Azure (DSC)
description: Este artigo fornece informações sobre a solução de problemas do Azure Automation State Configuration (DSC).
services: automation
ms.service: automation
ms.subservice: ''
author: mgoedtel
ms.author: magoedte
ms.date: 04/16/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 4c045e110e21ed201278dcd84f38cb4a376ae8db
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81679316"
---
# <a name="troubleshoot-issues-with-azure-automation-state-configuration-dsc"></a>Problemas de solução de problemas com o DSC (Azure Automation State Configuration, configuração do estado de automação do azure)

Este artigo fornece informações sobre problemas de solução de problemas que surgem ao compilar ou implantar configurações na Configuração do Estado de Automação do Azure (DSC).

>[!NOTE]
>Este artigo foi atualizado para usar o novo módulo Az do Azure PowerShell. Você ainda pode usar o módulo AzureRM, que continuará a receber as correções de bugs até pelo menos dezembro de 2020. Para saber mais sobre o novo módulo Az e a compatibilidade com o AzureRM, confira [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para obter instruções de instalação do módulo AZ no trabalhador do runbook híbrido, consulte [Instalar o Módulo PowerShell do Azure](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para sua conta de Automação, você pode atualizar seus módulos para a versão mais recente usando [Como atualizar módulos Azure PowerShell no Azure Automation](../automation-update-azure-modules.md).

## <a name="diagnosing-an-issue"></a>Diagnosticando um problema

Quando você recebe um erro de compilação ou implantação para configuração, aqui estão alguns passos para ajudá-lo a diagnosticar o problema.

### <a name="1-ensure-that-your-configuration-compiles-successfully-on-the-local-machine"></a>1. Certifique-se de que sua configuração seja compilada com sucesso na máquina local

O DSC (Azure Automation State Configuration, configuração do estado de automação do azure) é construído na Configuração do Estado Desejado (DSC) do PowerShell. Você pode encontrar a documentação para a linguagem DSC e sintaxe no [PowerShell DSC Docs](https://docs.microsoft.com/powershell/scripting/overview).

Ao compilar uma configuração de DSC em sua máquina local, você pode descobrir e resolver erros comuns, como:

   - Módulos ausentes
   - Erros de sintaxe
   - Erros de lógica

### <a name="2-view-dsc-logs-on-your-node"></a>2. Exibir logs de DSC em seu nó

Se sua configuração for compilada com sucesso, mas falhar quando aplicada a um nó, você poderá encontrar informações detalhadas nos registros do DSC. Para obter informações sobre onde encontrar esses registros, consulte [Onde estão os registros de eventos do DSC](/powershell/scripting/dsc/troubleshooting/troubleshooting#where-are-dsc-event-logs).

O módulo [xDscDiagnostics](https://github.com/PowerShell/xDscDiagnostics) pode ajudá-lo a analisar informações detalhadas dos registros do DSC. Se você entrar em contato com o suporte, eles requerem esses registros para diagnosticar o seu problema.

Você pode `xDscDiagnostics` instalar o módulo em sua máquina local usando as instruções encontradas no [Install the stable version module](https://github.com/PowerShell/xDscDiagnostics#install-the-stable-version-module).

Para instalar `xDscDiagnostics` o módulo na máquina Azure, use [Invoke-AzVMRunCommand](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand?view=azps-3.7.0). Você também pode usar a opção **executar comando** no portal Azure seguindo as etapas encontradas em [scripts Run PowerShell em sua VM windows com comando Executar](../../virtual-machines/windows/run-command.md).

Para obter informações sobre o uso **do xDscDiagnostics,** consulte [Usando xDscDiagnostics para analisar registros DSC](/powershell/scripting/dsc/troubleshooting/troubleshooting#using-xdscdiagnostics-to-analyze-dsc-logs). Veja também [os cmdlets xDscDiagnostics](https://github.com/PowerShell/xDscDiagnostics#cmdlets).

### <a name="3-ensure-that-nodes-and-the-automation-workspace-have-required-modules"></a>3. Certifique-se de que os nódulos e o espaço de trabalho de automação tenham módulos necessários

O DSC depende dos módulos instalados no nó. Ao usar a configuração do estado de automação do Azure, importe todos os módulos necessários para sua conta de automação usando as etapas em [Módulos de Importação](../shared-resources/modules.md#importing-modules). As configurações também podem ter uma dependência de versões específicas de módulos. Para obter mais informações, consulte [módulos de solução de problemas](shared-resources.md#modules).

## <a name="scenario-a-configuration-with-special-characters-cannot-be-deleted-from-the-portal"></a><a name="unsupported-characters"></a>Cenário: Uma configuração com caracteres especiais não pode ser excluída do portal

### <a name="issue"></a>Problema

Ao tentar excluir uma configuração DSC do portal, você verá o seguinte erro:

```error
An error occurred while deleting the DSC configuration '<name>'.  Error-details: The argument configurationName with the value <name> is not valid.  Valid configuration names can contain only letters,  numbers, and underscores.  The name must start with a letter.  The length of the name must be between 1 and 64 characters.
```

### <a name="cause"></a>Causa

Este erro é uma questão temporária que está prevista para ser resolvida.

### <a name="resolution"></a>Resolução

Use o cmdlet [Remove-AzAutomationDscConfiguration](cmdlethttps://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationDscConfiguration?view=azps-3.7.0 para excluir a configuração).

### <a name="scenario-failed-to-register-dsc-agent"></a><a name="failed-to-register-agent"></a>Cenário: Falha no registro do Agente DSC

### <a name="issue"></a>Problema

Quando [Set-DscLocalConfigurationManager](https://docs.microsoft.com/powershell/module/psdesiredstateconfiguration/set-dsclocalconfigurationmanager?view=powershell-5.1) ou outro cmdlet DSC, você recebe o erro:

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

Esse erro é normalmente causado por um firewall, a máquina estar por trás de um servidor proxy ou outros erros de rede.

### <a name="resolution"></a>Resolução

Verifique se sua máquina tem acesso aos pontos finais apropriados para DSC e tente novamente. Para obter uma lista de portas e endereços necessários, consulte [o planejamento da rede](../automation-dsc-overview.md#network-planning)

## <a name="a-nameunauthorizedscenario-status-reports-return-response-code-unauthorized"></a><a name="unauthorized"><a/>Cenário: Status relata código de resposta de retorno Não autorizado

### <a name="issue"></a>Problema

Ao registrar um nó com a configuração do estado de automação do Azure, você recebe uma das seguintes mensagens de erro:

```error
The attempt to send status report to the server https://{your Automation account URL}/accounts/xxxxxxxxxxxxxxxxxxxxxx/Nodes(AgentId='xxxxxxxxxxxxxxxxxxxxxxxxx')/SendReport returned unexpected response code Unauthorized.
```

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC / Registration of the Dsc Agent with the server failed.
```

### <a name="cause"></a>Causa

Este problema é causado por um certificado ruim ou vencido. Consulte [o vencimento do certificado e o recadastramento](../automation-dsc-onboarding.md#re-registering-a-node).

Esse problema também pode ser causado por uma configuração proxy que não permite acesso a ***.azure-automation.net**. Para obter mais informações, consulte [Configuração de redes privadas](../automation-dsc-overview.md#network-planning). 

### <a name="resolution"></a>Resolução

Use as etapas abaixo para reregistrar o nó DSC em falha.

Passo 1 - Desregistre o nó.

1. No portal Azure, navegue até **contas de** -> **automação** residencial - > (sua conta de automação) -> **Configuração de Estado (DSC)**.
2. Selecione **''''Nós'** e clique no nó com problemas.
3. Clique **em Cancelar o registro** para cancelar o registro do nó.

Passo 2 - Desinstale a extensão DSC do nó.

1. No portal Azure, navegue até **home** -> **virtual machine** -> (nó de falha) -> **Extensões**.
2. Selecione **Microsoft.Powershell.DSC**, a extensão PowerShell DSC.
3. Clique **em Desinstalar** para desinstalar a extensão.

Passo 3 - Remova todos os certificados ruins ou vencidos do nó.

No nó de falha de um prompt PowerShell elevado, execute estes comandos:

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

Passo 4 - Reregistre o nó de falha.

1. No portal Dozure, navegue até **a** -> **configuração** de Estado > de Automação Residencial -> (sua conta de automação) -> **State (DSC)**
2. Selecione **'Nós '**
3. Clique em **Adicionar**.
4. Selecione o nó de falha.
5. Clique **em Conectar** e selecione as opções desejadas.

## <a name="scenario-node-is-in-failed-status-with-a-not-found-error"></a><a name="failed-not-found"></a>Cenário: o nó está com um status de falha e um erro “Não encontrado”

### <a name="issue"></a>Problema

O nó tem um relatório com o status Falha e contendo o erro:

```error
The attempt to get the action from server https://<url>//accounts/<account-id>/Nodes(AgentId=<agent-id>)/GetDscAction failed because a valid configuration <guid> cannot be found.
```

### <a name="cause"></a>Causa

Esse erro normalmente ocorre quando o nó é atribuído a um nome de configuração, por exemplo, **ABC**, em vez de um nome de configuração de nó (arquivo MOF), por exemplo, **ABC. Servidor Web**.

### <a name="resolution"></a>Resolução

* Certifique-se de que está atribuindo o nó com o nome da configuração do nó e não com o nome da configuração.
* Você pode atribuir uma configuração de nó para um nó usando o Portal do Azure ou com um cmdlet do PowerShell.

  * No portal Azure, navegue até a**configuração** **de** -> (Deca) -> (sua conta de automação) -> **State (DSC)** e selecione um nó e clique em Atribuir a **configuração de nó**.
  * Use o [cmdlet Set-AzAutomationDscNode.](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationDscNode?view=azps-3.7.0)

## <a name="scenario-no-node-configurations-mof-files-were-produced-when-a-configuration-was-compiled"></a><a name="no-mof-files"></a>Cenário: Nenhuma configuração de nó (arquivos MOF) foi produzida quando uma configuração foi compilada

### <a name="issue"></a>Problema

Seu trabalho de compilação do DSC é suspenso com o erro:

```error
Compilation completed successfully, but no node configuration **.mof** files were generated.
```

### <a name="cause"></a>Causa

Quando a expressão `Node` seguindo a palavra-chave na `$null`configuração DSC é avaliada para , nenhuma configuração de nó é produzida.

### <a name="resolution"></a>Resolução

Use uma das seguintes soluções para corrigir o problema:

* Certifique-se de que `Node` a expressão ao lado da palavra-chave na definição de configuração não esteja avaliando para Nulo.
* Se você estiver passando [ConfigurationData](../automation-dsc-compile.md) ao compilar a configuração, certifique-se de que você está passando os valores que a configuração espera dos dados de configuração.

### <a name="scenario-the-dsc-node-report-becomes-stuck-in-the-in-progress-state"></a><a name="dsc-in-progress"></a>Cenário: O relatório do nó do DSC fica preso no estado em andamento

### <a name="issue"></a>Problema

O agente DSC produz:

```error
No instance found with given property values
```

### <a name="cause"></a>Causa

Você atualizou sua versão do Windows Management Framework (WMF) e corrompeu o WMI (Windows Management Instrumentation, instrumentação de gerenciamento do Windows).

### <a name="resolution"></a>Resolução

Siga as instruções em [dSC problemas e limitações conhecidas](https://docs.microsoft.com/powershell/scripting/wmf/known-issues/known-issues-dsc).

## <a name="scenario-unable-to-use-a-credential-in-a-dsc-configuration"></a><a name="issue-using-credential"></a>Cenário: Não é possível usar uma credencial em uma configuração DSC

### <a name="issue"></a>Problema

Seu trabalho de compilação dSC suspenso com o erro:

```error
System.InvalidOperationException error processing property 'Credential' of type <some resource name>: Converting and storing an encrypted password as plaintext is allowed only if PSDscAllowPlainTextPassword is set to true.
```

### <a name="cause"></a>Causa

Você usou uma credencial em uma configuração, `ConfigurationData` mas `PSDscAllowPlainTextPassword` não forneceu o adequado para definir como verdadeiro para cada configuração de nó.

### <a name="resolution"></a>Resolução

Certifique-se de passar `ConfigurationData` no `PSDscAllowPlainTextPassword` próprio para definir como verdadeiro para cada configuração de nó mencionada na configuração. Consulte [Compilar configurações de DSC na configuração do estado de automação do Azure](../automation-dsc-compile.md).

## <a name="scenario-failure-processing-extension-error-when-onboarding-from-dsc-extension"></a><a name="failure-processing-extension"></a>Cenário: Erro de "extensão de processamento de falha" ao embarcar na extensão do DSC

### <a name="issue"></a>Problema

Ao embarcar usando uma extensão DSC, ocorre uma falha contendo o erro:

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC'. Error message: \"DSC COnfiguration 'RegistrationMetaConfigV2' completed with error(s). Following are the first few: Registration of the Dsc Agent with the server <url> failed. The underlying error is: The attempt to register Dsc Agent with Agent Id <ID> with the server <url> return unexpected response code BadRequest. .\".
```

### <a name="cause"></a>Causa

Esse erro normalmente ocorre quando o nó é atribuído a um nome de configuração de nó que não existe no serviço.

### <a name="resolution"></a>Resolução

* Certifique-se de que você está atribuindo o nó com um nome que corresponda exatamente ao nome no serviço.
* Você pode optar por não incluir o nome de configuração do nó, o que resulta em onboarding do nó, mas não atribuindo uma configuração de nó.

## <a name="scenario-one-or-more-errors-occurred-error-when-registering-a-node-using-powershell"></a><a name="cross-subscription"></a>Cenário: Erro "Ocorreu um ou mais erros" ao registrar um nó usando o PowerShell

### <a name="issue"></a>Problema

Ao registrar um nó usando [Register-AzAutomationDSCNode](https://docs.microsoft.com/powershell/module/az.automation/register-azautomationdscnode?view=azps-3.7.0) ou [Register-AzureRMAutomationDSCNode,](https://docs.microsoft.com/powershell/module/azurerm.automation/register-azurermautomationdscnode?view=azurermps-6.13.0)você recebe o seguinte erro:

```error
One or more errors occurred.
```

### <a name="cause"></a>Causa

Esse erro ocorre quando você tenta registrar um nó em uma assinatura separada da usada pela conta Automação.

### <a name="resolution"></a>Resolução

Trate o nó de assinatura cruzada como se fosse definido para uma nuvem separada ou no local. Registre o nó usando uma dessas opções de onboarding:

* Windows - [Máquinas Windows físicas/virtuais no local ou em uma nuvem diferente do Azure/AWS](../automation-dsc-onboarding.md#onboarding-physicalvirtual-windows-machines).
* Linux - [Máquinas Linux físicas/virtuais no local, ou em uma nuvem diferente do Azure](../automation-dsc-onboarding.md#onboarding-physicalvirtual-linux-machines).

## <a name="scenario-error-message---provisioning-failed"></a><a name="agent-has-a-problem"></a>Cenário: Mensagem de erro - "Falha no provisionamento"

### <a name="issue"></a>Problema

Ao registrar um nó, você vê o erro:

```error
Provisioning has failed
```

### <a name="cause"></a>Causa

Essa mensagem ocorre quando há um problema com a conectividade entre o nó e o Azure.

### <a name="resolution"></a>Resolução

Determine se seu nó está em uma rede virtual privada (VPN) ou tem outros problemas de conexão com o Azure. Consulte [Problemas ao abordar soluções de onboarding](onboarding.md).

## <a name="scenario-failure-with-a-general-error-when-applying-a-configuration-in-linux"></a><a name="failure-linux-temp-noexec"></a>Cenário: Falha com um erro geral ao aplicar uma configuração no Linux

### <a name="issue"></a>Problema

Ao aplicar uma configuração no Linux, ocorre uma falha contendo o erro:

```error
This event indicates that failure happens when LCM is processing the configuration. ErrorId is 1. ErrorDetail is The SendConfigurationApply function did not succeed.. ResourceId is [resource]name and SourceInfo is ::nnn::n::resource. ErrorMessage is A general error occurred, not covered by a more specific error code..
```

### <a name="cause"></a>Causa

Se o local **/tmp** estiver definido como `noexec`, a versão atual do DSC não aplicará configurações.

### <a name="resolution"></a>Resolução

Remova `noexec` a opção do **local /tmp.**

## <a name="scenario-node-configuration-names-that-overlap-can-result-in-bad-release"></a><a name="compilation-node-name-overlap"></a>Cenário: Nomes de configuração de nó que se sobrepõem podem resultar em má liberação

### <a name="issue"></a>Problema

Quando você usa um único script de configuração para gerar configurações de nó múltiplos e alguns nomes de configuração de nó são subconjuntos de outros nomes, o serviço de compilação pode acabar atribuindo a configuração errada. Esse problema só ocorre quando se usa um único script para gerar configurações com dados de configuração por nó, e somente quando a sobreposição de nome ocorre no início da string. Um exemplo é um único script de configuração usado para gerar configurações com base em dados de nó passados como um hashtable usando cmdlets, e os dados do nó incluem servidores nomeados **servidor** e **1server**.

### <a name="cause"></a>Causa

Este é um problema conhecido com o serviço de compilação.

### <a name="resolution"></a>Resolução

A melhor solução é compilar localmente ou em um pipeline de CI/CD e carregar os arquivos MOF de configuração do nó diretamente para o serviço. Se a compilação no serviço é um requisito, a próxima melhor solução é dividir os trabalhos de compilação para que não haja sobreposição de nomes.

## <a name="scenario-gateway-timeout-error-on-dsc-configuration-upload"></a><a name="gateway-timeout"></a>Cenário: Erro de tempo de tempo do gateway no upload da configuração do DSC

#### <a name="issue"></a>Problema

Você recebe `GatewayTimeout` um erro ao carregar uma configuração DSC. 

### <a name="cause"></a>Causa

Configurações de DSC que levam muito tempo para serem compiladas podem causar esse erro.

### <a name="resolution"></a>Resolução

Você pode fazer com que suas configurações de `ModuleName` DSC analisem mais rapidamente, incluindo explicitamente o parâmetro para quaisquer chamadas [Import-DSCResource.](https://docs.microsoft.com/powershell/scripting/dsc/configurations/import-dscresource?view=powershell-5.1)

## <a name="next-steps"></a>Próximas etapas

Se você não vê o seu problema acima ou não consegue resolver o seu problema, tente um dos seguintes canais para obter suporte adicional:

* Obtenha respostas de especialistas do Azure através [do Azure Forums](https://azure.microsoft.com/support/forums/).
* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport), a conta oficial do Microsoft Azure para melhorar a experiência do cliente conectando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.
* Registrar um incidente de suporte do Azure. Vá ao site de suporte do [Azure](https://azure.microsoft.com/support/options/) e **selecione Obter suporte**.