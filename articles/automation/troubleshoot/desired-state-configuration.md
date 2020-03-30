---
title: Solução de problemas Configuração do estado desejado (DSC) de automação do Azure
description: Este artigo fornece informações sobre solução de problemas de configuração de estado desejado (DSC)
services: automation
ms.service: automation
ms.subservice: ''
author: mgoedtel
ms.author: magoedte
ms.date: 04/16/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 99220fdf5dfb47f235637f83ba9be4ec015758bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294432"
---
# <a name="troubleshoot-issues-with-azure-automation-desired-state-configuration-dsc"></a>Problemas de solução de problemas com o DSC (Desired State Configuration, configuração do estado desejado da automação do Azure)

Este artigo fornece informações sobre como solucionar problemas com a DSC (Configuração de Estado Desejado).

## <a name="diagnosing-an-issue"></a>Diagnosticando um problema

Quando você tem erros ao compilar ou implantar configurações na Configuração do Estado do Azure, aqui estão alguns passos para ajudá-lo a diagnosticar o problema.

### <a name="1-ensure-that-your-configuration-compiles-successfully-on-the-local-machine"></a>1. Certifique-se de que sua configuração seja compilada com sucesso na máquina local

A configuração do estado do Azure é construída no PowerShell DSC. Você pode encontrar a documentação para a linguagem DSC e sintaxe no [PowerShell DSC Docs](https://docs.microsoft.com/powershell/scripting/overview).

Ao compilar sua configuração de DSC em sua máquina local, você pode descobrir e resolver erros comuns, como:

   - Módulos ausentes
   - Erros de sintaxe
   - Erros de lógica

### <a name="2-view-dsc-logs-on-your-node"></a>2. Exibir logs de DSC em seu nó

Se sua configuração for compilada com sucesso, mas falhar quando aplicada a um nó, você poderá encontrar informações detalhadas nos registros do DSC. Para obter informações sobre onde encontrar esses registros, consulte [Onde estão os registros de eventos do DSC](/powershell/scripting/dsc/troubleshooting/troubleshooting#where-are-dsc-event-logs).

O módulo [xDscDiagnostics](https://github.com/PowerShell/xDscDiagnostics) pode ajudá-lo a analisar informações detalhadas dos registros do DSC. Se você entrar em contato com o suporte, eles requerem esses registros para diagnosticar o seu problema.

Você pode instalar o módulo xDscDiagnostics em sua máquina local usando as instruções encontradas no [Install the stable version module](https://github.com/PowerShell/xDscDiagnostics#install-the-stable-version-module).

Para instalar o módulo xDscDiagnostics na máquina Azure, use [Invoke-AzVMRunCommand](/powershell/module/azurerm.compute/invoke-azurermvmruncommand). Você também pode usar a opção **executar** comando a partir do portal, seguindo as [etapas encontradas em executar scripts PowerShell em sua VM windows com comando Executar](../../virtual-machines/windows/run-command.md).

Para obter informações sobre o uso do xDscDiagnostics, consulte [Usando xDscDiagnostics para analisar registros DSC](/powershell/scripting/dsc/troubleshooting/troubleshooting#using-xdscdiagnostics-to-analyze-dsc-logs). Veja também [os cmdlets xDscDiagnostics](https://github.com/PowerShell/xDscDiagnostics#cmdlets).

### <a name="3-ensure-that-nodes-and-the-automation-workspace-have-required-modules"></a>3. Certifique-se de que os nódulos e o espaço de trabalho de automação tenham módulos necessários

O DSC depende dos módulos instalados no nó. Ao usar a configuração do estado de automação do Azure, importe todos os módulos necessários para sua conta de automação usando as etapas listadas em [Módulos de Importação](../shared-resources/modules.md#import-modules). As configurações também podem ter uma dependência de versões específicas de módulos. Para obter mais informações, consulte [Módulos de solução de problemas](shared-resources.md#modules).

## <a name="common-errors-when-working-with-dsc"></a>Erros comuns ao trabalhar com DSC

### <a name="scenario-a-configuration-with-special-characters-cannot-be-deleted-from-the-portal"></a><a name="unsupported-characters"></a>Cenário: Uma configuração com caracteres especiais não pode ser excluída do portal

#### <a name="issue"></a>Problema

Ao tentar excluir uma configuração DSC do portal, você verá o seguinte erro:

```error
An error occurred while deleting the DSC configuration '<name>'.  Error-details: The argument configurationName with the value <name> is not valid.  Valid configuration names can contain only letters,  numbers, and underscores.  The name must start with a letter.  The length of the name must be between 1 and 64 characters.
```

#### <a name="cause"></a>Causa

Este erro é uma questão temporária que está prevista para ser resolvida.

#### <a name="resolution"></a>Resolução

* Use o Az Cmdlet "Remove-AzAutomationDscConfiguration" para excluir a configuração.
* A documentação deste cmdlet ainda não foi atualizada.  Até lá, consulte a documentação do módulo AzureRM.
  * [Remove-AzureRmAutomationDSCConfiguração](/powershell/module/azurerm.automation/Remove-AzureRmAutomationDscConfiguration)

### <a name="scenario-failed-to-register-dsc-agent"></a><a name="failed-to-register-agent"></a>Cenário: Falha no registro do Agente DSC

#### <a name="issue"></a>Problema

Ao tentar executar `Set-DscLocalConfigurationManager` ou outro cmdlet DSC você recebe o erro:

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

#### <a name="cause"></a>Causa

Esse erro é normalmente causado por um firewall, a máquina estar por trás de um servidor proxy ou outros erros de rede.

#### <a name="resolution"></a>Resolução

Verifique se sua máquina tem acesso aos pontos finais apropriados para o Azure Automation DSC e tente novamente. Para obter uma lista de portas e endereços necessários, consulte [o planejamento da rede](../automation-dsc-overview.md#network-planning)

### <a name="a-nameunauthorizedscenario-status-reports-return-response-code-unauthorized"></a><a name="unauthorized"><a/>Cenário: Status relata código de resposta de retorno "Não Autorizado"

#### <a name="issue"></a>Problema

Ao registrar um nó com Configuração de Estado (DSC), você recebe uma das seguintes mensagens de erro:

```error
The attempt to send status report to the server https://{your Automation account URL}/accounts/xxxxxxxxxxxxxxxxxxxxxx/Nodes(AgentId='xxxxxxxxxxxxxxxxxxxxxxxxx')/SendReport returned unexpected response code Unauthorized.
```

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC / Registration of the Dsc Agent with the server failed.
```

### <a name="cause"></a>Causa

Este problema é causado por um certificado ruim ou vencido.  Para obter mais informações, consulte [O vencimento do Certificado e o recadastramento](../automation-dsc-onboarding.md#re-registering-a-node).

### <a name="resolution"></a>Resolução

Siga as etapas listadas abaixo para reregistrar o nó DSC em falha.

Primeiro, desregistre o nó usando as seguintes etapas.

1. A partir do portal Azure, em **Contas de** -> **Automação**Residencial -> {Sua conta de automação} -> **configuração de Estado (DSC)**
2. Clique em "Nós", e clique no nó com problemas.
3. Clique em "Cancelar o registro" para cancelar o registro do nó.

Em segundo lugar, desinstale a extensão DSC do nó.

1. Do portal Azure, em **Home** -> **Virtual Machine** -> {Fail node} -> **Extensões**
2. Clique em "Microsoft.Powershell.DSC".
3. Clique em "Desinstalar", para desinstalar a extensão PowerShell DSC.

Em terceiro lugar, remova todos os certificados ruins ou vencidos do nó.

No nó de falha de um Prompt Powershell elevado, execute o seguinte:

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

Finalmente, registre o nó de falha usando as seguintes etapas.

1. A partir do portal Azure, em **Contas de** -> **Automação** Residencial -> {Sua conta de automação} -> **configuração de Estado (DSC)**
2. Clique em "Nodes".
3. Clique no botão "Adicionar".
4. Selecione o nó de falha.
5. Clique em "Conectar", e selecione as opções desejadas.

### <a name="scenario-node-is-in-failed-status-with-a-not-found-error"></a><a name="failed-not-found"></a>Cenário: o nó está com um status de falha e um erro “Não encontrado”

#### <a name="issue"></a>Problema

O nó tem um relatório com o status **Falha** e contendo o erro:

```error
The attempt to get the action from server https://<url>//accounts/<account-id>/Nodes(AgentId=<agent-id>)/GetDscAction failed because a valid configuration <guid> cannot be found.
```

#### <a name="cause"></a>Causa

Esse erro normalmente ocorre quando o nó é atribuído a um nome de configuração (por exemplo, ABC) em vez de um nome de configuração de nó (por exemplo, ABC.WebServer).

#### <a name="resolution"></a>Resolução

* Certifique-se de que está atribuindo o nó com "nome de configuração de nó" e não com o "nome de configuração".
* Você pode atribuir uma configuração de nó para um nó usando o Portal do Azure ou com um cmdlet do PowerShell.

  * Para atribuir uma configuração de nó a um nó usando o portal Azure, abra a página **'Nodes' do DSC** e, em seguida, selecione um nó e clique no botão **Dernete de configuração.**
  * Para atribuir uma configuração de nó a um nó usando cmdlet PowerShell, use **set-AzureRmAutomationDscNode** cmdlet

### <a name="scenario-no-node-configurations-mof-files-were-produced-when-a-configuration-is-compiled"></a><a name="no-mof-files"></a>Cenário: Nenhuma configuração de nó (arquivos MOF) foi produzida quando uma configuração é compilada

#### <a name="issue"></a>Problema

Seu trabalho de compilação do DSC é suspenso com o erro:

```error
Compilation completed successfully, but no node configuration.mofs were generated.
```

#### <a name="cause"></a>Causa

Quando a expressão após a palavra-chave **Node** na configuração do DSC for avaliada como `$null`, nenhuma configuração de nó será produzida.

#### <a name="resolution"></a>Resolução

Qualquer uma das soluções a seguir corrige o problema:

* Certifique-se de que a expressão ao lado da palavra-chave **Nó** na definição de configuração não esteja avaliando $null.
* Se você estiver passando ConfigurationData ao compilar a configuração, certifique-se de que esteja passando os valores esperados e que a configuração exige de [ConfigurationData](../automation-dsc-compile.md).

### <a name="scenario-the-dsc-node-report-becomes-stuck-in-progress-state"></a><a name="dsc-in-progress"></a>Cenário: O relatório do nó do DSC fica preso "em andamento" estado

#### <a name="issue"></a>Problema

O agente DSC produz:

```error
No instance found with given property values
```

#### <a name="cause"></a>Causa

Você atualizou sua versão do WMF e tem o WMI corrompido.

#### <a name="resolution"></a>Resolução

Para corrigir o problema, siga as instruções do artigo [dsc conhecido sobre questões e limitações.](https://docs.microsoft.com/powershell/scripting/wmf/known-issues/known-issues-dsc)

### <a name="scenario-unable-to-use-a-credential-in-a-dsc-configuration"></a><a name="issue-using-credential"></a>Cenário: Não é possível usar uma credencial em uma configuração DSC

#### <a name="issue"></a>Problema

Seu trabalho de compilação do DSC foi suspenso com o erro:

```error
System.InvalidOperationException error processing property 'Credential' of type <some resource name>: Converting and storing an encrypted password as plaintext is allowed only if PSDscAllowPlainTextPassword is set to true.
```

#### <a name="cause"></a>Causa

Você usou uma credencial em uma configuração, mas não forneceu **dados de configuração** adequados para definir **PSDscAllowPlainTextPassword** como verdadeiro para cada configuração de nó.

#### <a name="resolution"></a>Resolução

* Certifique-se de passar na **Configuração adequadaData** para definir **PSDscAllowPlainTextPassword** para true para cada configuração de nó mencionada na configuração. Para obter mais informações, consulte [Compilar configurações de DSC na configuração do estado de automação do Azure](../automation-dsc-compile.md).

### <a name="scenario-onboarding-from-dsc-extension-failure-processing-extension-error"></a><a name="failure-processing-extension"></a>Cenário: Onboarding da extensão dsc, erro de "extensão de processamento de falha"

#### <a name="issue"></a>Problema

Ao embarcar usando a extensão DSC, ocorre uma falha que contém o erro:

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC'. Error message: \"DSC COnfiguration 'RegistrationMetaConfigV2' completed with error(s). Following are the first few: Registration of the Dsc Agent with the server <url> failed. The underlying error is: The attempt to register Dsc Agent with Agent Id <ID> with the server <url> return unexpected response code BadRequest. .\".
```

#### <a name="cause"></a>Causa

Esse erro normalmente ocorre quando o nó é atribuído a um nome de configuração de nó que não existe no serviço.

#### <a name="resolution"></a>Resolução

* Certifique-se de que você está atribuindo o nó com um nome de configuração de nó que corresponda exatamente ao nome no serviço.
* Você pode optar por não incluir o nome de configuração do nó, o que resultará em onboarding do nó, mas não atribuindo uma configuração de nó

### <a name="scenario-registering-a-node-with-powershell-returns-the-error-one-or-more-errors-occurred"></a><a name="cross-subscription"></a>Cenário: Registrar um nó com o PowerShell retorna o erro "Ocorreu um ou mais erros"

#### <a name="issue"></a>Problema

Ao registrar um nó `Register-AzAutomationDSCNode` `Register-AzureRMAutomationDSCNode`usando ou , você recebe o seguinte erro.

```error
One or more errors occurred.
```

#### <a name="cause"></a>Causa

Esse erro ocorre quando você tenta registrar um nó que vive em uma assinatura separada da conta Automação.

#### <a name="resolution"></a>Resolução

Trate o nó de assinatura cruzada como se ele vivesse em uma nuvem separada, ou no local.

Siga as etapas abaixo para registrar o nó.

* Windows - [Máquinas Windows físicas/virtuais no local ou em uma nuvem diferente do Azure/AWS](../automation-dsc-onboarding.md#onboarding-physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances).
* Linux - [Máquinas Linux físicas/virtuais no local, ou em uma nuvem diferente do Azure](../automation-dsc-onboarding.md#onboarding-physicalvirtual-linux-machines-on-premises-or-in-a-cloud-other-than-azure).

### <a name="scenario-error-message---provisioning-failed"></a><a name="agent-has-a-problem"></a>Cenário: Mensagem de erro - "Falha no provisionamento"

#### <a name="issue"></a>Problema

Ao registrar um nó, você vê o erro:

```error
Provisioning has failed
```

#### <a name="cause"></a>Causa

Essa mensagem ocorre quando há um problema de conectividade entre o nó e o Azure.

#### <a name="resolution"></a>Resolução

Determine se seu nó está em uma rede virtual privada ou tem outros problemas de conexão com o Azure.

Para obter mais informações, consulte [Solucionar erros ao embarcar em soluções](onboarding.md).

### <a name="scenario-applying-a-configuration-in-linux-a-failure-occurs-with-a-general-error"></a><a name="failure-linux-temp-noexec"></a>Cenário: Aplicando uma configuração no Linux, uma falha ocorre com um erro geral

#### <a name="issue"></a>Problema

Ao aplicar uma configuração no Linux, ocorre uma falha contendo o erro:

```error
This event indicates that failure happens when LCM is processing the configuration. ErrorId is 1. ErrorDetail is The SendConfigurationApply function did not succeed.. ResourceId is [resource]name and SourceInfo is ::nnn::n::resource. ErrorMessage is A general error occurred, not covered by a more specific error code..
```

#### <a name="cause"></a>Causa

Os clientes identificaram que, se a `/tmp` localização estiver definida para `noexec`, a versão atual do DSC não irá aplicar configurações.

#### <a name="resolution"></a>Resolução

* Remova `noexec` a opção `/tmp` do local.

### <a name="scenario-node-configuration-names-that-overlap-could-result-in-bad-release"></a><a name="compilation-node-name-overlap"></a>Cenário: Nomes de configuração de nó que se sobrepõem podem resultar em má liberação

#### <a name="issue"></a>Problema

Se um único script de configuração for usado para gerar configurações de nó múltiplos, e algumas das configurações do nó tiverem um nome que seja um subconjunto de outros, um problema no serviço de compilação pode resultar na atribuição da configuração errada.  Isso só ocorre ao usar um único script para gerar configurações com dados de configuração por nó, e somente quando a sobreposição de nome ocorre no início da string.

Por exemplo, se um único script de configuração for usado para gerar configurações com base em dados de nó passados como um hashtable usando cmdlets, e os dados do nó incluem um servidor chamado "servidor" e "1server".

#### <a name="cause"></a>Causa

Problema conhecido com o serviço de compilação.

#### <a name="resolution"></a>Resolução

A melhor solução seria compilar localmente ou em um pipeline de CI/CD e carregar os arquivos MOF diretamente para o serviço.  Se a compilação no serviço é um requisito, a próxima melhor solução seria dividir os trabalhos de compilação para que não haja sobreposição de nomes.

### <a name="scenario-gateway-timeout-error-on-dsc-configuration-upload"></a><a name="gateway-timeout"></a>Cenário: Erro de tempo de tempo do gateway no upload da configuração do DSC

#### <a name="issue"></a>Problema

Você recebe `GatewayTimeout` um erro ao carregar uma configuração DSC. 

#### <a name="cause"></a>Causa

Configurações de DSC que levam muito tempo para serem compiladas podem causar esse erro.

#### <a name="resolution"></a>Resolução

Você pode fazer com que suas configurações de `ModuleName` DSC `Import-DscResource` analisem mais rapidamente, incluindo explicitamente o parâmetro para quaisquer chamadas. Para obter mais informações, consulte [Usando O Recurso de Importação-DSC .](https://docs.microsoft.com/powershell/scripting/dsc/configurations/import-dscresource?view=powershell-5.1)

## <a name="next-steps"></a>Próximas etapas

Se você não encontrou seu problema ou não conseguiu resolver seu problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure através [do Azure Forums](https://azure.microsoft.com/support/forums/).
* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) – a conta oficial do Microsoft Azure para melhorar a experiência do cliente conectando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.
* Se precisar de mais ajuda, você pode registrar um incidente de suporte do Azure. Vá ao site de suporte do [Azure](https://azure.microsoft.com/support/options/) e **selecione Obter suporte**.
