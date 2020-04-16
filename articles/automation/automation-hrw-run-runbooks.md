---
title: Executar runbooks no Hybrid Runbook Worker da Automação do Azure
description: Este artigo fornece informações sobre a execução de runbooks em computadores em seu datacenter local ou provedor de nuvem com a função de Hybrid Runbook Worker.
services: automation
ms.subservice: process-automation
ms.date: 01/29/2019
ms.topic: conceptual
ms.openlocfilehash: b65c72e0c65cf9aa84cb614478fbdf78258f3054
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81405818"
---
# <a name="running-runbooks-on-a-hybrid-runbook-worker"></a>Executar runbooks em um Hybrid Runbook Worker

Os runbooks que têm como alvo um Hybrid Runbook Worker normalmente gerenciam recursos no computador local ou contra recursos no ambiente local onde o trabalhador é implantado. Os runbooks na Automação do Azure normalmente gerenciam recursos na nuvem do Azure. Embora sejam usados de forma diferente, os runbooks que são executados no Azure Automation e runbooks que são executados em um Hybrid Runbook Worker são idênticos na estrutura.

Ao criar um runbook para executar em um Híbrido Runbook Worker, você deve editar e testar o runbook na máquina que hospeda o trabalhador. A máquina host tem todos os módulos PowerShell e acesso à rede necessários para gerenciar e acessar os recursos locais. Depois de testar o manual na máquina Hybrid Runbook Worker, você pode carregá-lo para o ambiente azure Automation, onde ele pode ser executado no trabalhador. 

>[!NOTE]
>Este artigo foi atualizado para usar o novo módulo Az do Azure PowerShell. Você ainda pode usar o módulo AzureRM, que continuará a receber as correções de bugs até pelo menos dezembro de 2020. Para saber mais sobre o novo módulo Az e a compatibilidade com o AzureRM, confira [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para obter instruções de instalação do módulo AZ no trabalhador do runbook híbrido, consulte [Instalar o Módulo PowerShell do Azure](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para sua conta de Automação, você pode atualizar seus módulos para a versão mais recente usando [Como atualizar módulos Azure PowerShell no Azure Automation](automation-update-azure-modules.md).

## <a name="runbook-permissions-for-a-hybrid-runbook-worker"></a>Permissões de runbook para um trabalhador híbrido de runbook

Como eles estão acessando recursos não-Azure, os runbooks que executam em um Hybrid Runbook Worker não podem usar o mecanismo de autenticação normalmente usado por runbooks autenticando recursos do Azure. Um runbook fornece sua própria autenticação aos recursos locais ou configura a autenticação usando [identidades gerenciadas para recursos do Azure](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager). Você também pode especificar uma conta Run As para fornecer um contexto de usuário para todos os runbooks.

### <a name="runbook-authentication"></a>Autenticação de runbook

Por padrão, os runbooks são executados no computador local. Para windows, eles são executados no contexto da conta do **sistema** local. Para Linux, eles são executados no contexto da conta de usuário especial **nxautomation**. Em ambos os cenários, os runbooks devem fornecer sua própria autenticação aos recursos que eles acessam.

Você pode usar [ativos de credencial](automation-credentials.md) e [certificado](automation-certificates.md) em seu livro de execução com cmdlets que permitem especificar credenciais para que o runbook possa autenticar para diferentes recursos. O exemplo a seguir mostra uma parte de um runbook que reinicia um computador. Ele recupera credenciais de um ativo de credencial e o nome do computador `Restart-Computer` a partir de um ativo variável e, em seguida, usa esses valores com o cmdlet.

```powershell
$Cred = Get-AutomationPSCredential -Name "MyCredential"
$Computer = Get-AutomationVariable -Name "ComputerName"

Restart-Computer -ComputerName $Computer -Credential $Cred
```

Você também pode usar uma atividade [InlineScript.](automation-powershell-workflow.md#inlinescript) `InlineScript`permite executar blocos de código em outro computador com credenciais especificadas pelo [parâmetro comum PSCredential](/powershell/module/psworkflow/about/about_workflowcommonparameters).

### <a name="run-as-account"></a>Conta Executar como

Em vez de ter seu runbook fornecer sua própria autenticação aos recursos locais, você pode especificar uma conta Run As para um grupo Hybrid Runbook Worker. Para isso, você deve definir um [ativo de credencial](automation-credentials.md) que tenha acesso aos recursos locais. Esses recursos incluem lojas de certificados e todos os runbooks executados sob essas credenciais em um Trabalhador de Runbook Híbrido no grupo.

O nome de usuário da credencial deve estar em um dos seguintes formatos:

* domínio\nome de usuário
* username@domain
* nome de usuário (para contas locais do computador local)

Use o procedimento a seguir para especificar uma conta Run As para um grupo Hybrid Runbook Worker.

1. Crie um [ativo de credencial](automation-credentials.md) com acesso a recursos locais.
2. Abra a conta de Automação no Portal do Azure.
3. Escolha o bloco **Grupos do Hybrid Worker** e selecione o grupo.
4. Selecione **Todas as configurações,** seguidas pelas **configurações do grupo de trabalhadores híbridos**.
5. Alterar o valor de **Executar como** de **Padrão** para **Personalizado**.
6. Escolha a credencial e clique em **Salvar**.

### <a name="managed-identities-for-azure-resources"></a><a name="managed-identities-for-azure-resources"></a>Identidades gerenciadas para recursos do Azure

Os trabalhadores híbridos do runbook em máquinas virtuais Do Azure podem usar identidades gerenciadas para os recursos do Azure para autenticar os recursos do Azure. Usar identidades gerenciadas para recursos do Azure em vez de contas run As fornece benefícios porque você não precisa:

* Exporte o certificado Run As e, em seguida, importe-o para o Trabalhador do Runbook Híbrido.
* Renove o certificado usado pela conta Run As.
* Manuseie o objeto de conexão Executar como no código do livro de execução.

Siga os próximos passos para usar uma identidade gerenciada para os recursos do Azure em um Hybrid Runbook Worker.

1. Crie uma VM Azure.
2. Configure identidades gerenciadas para recursos do Azure na VM. Consulte [Configurar identidades gerenciadas para recursos do Azure em uma VM usando o portal Azure](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-on-an-existing-vm).
3. Dê à VM acesso a um grupo de recursos no Resource Manager. Consulte [Usar uma identidade gerenciada atribuída ao sistema VM do Windows VM para acessar o Resource Manager](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager).
4. Instale o trabalhador do Runbook Híbrido na VM. Consulte [Implantar um trabalhador de runbook híbrido do Windows](automation-windows-hrw-install.md).
5. Atualize o manual para usar o cmdlet `Identity` [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0) com o parâmetro para autenticar os recursos do Azure. Essa configuração reduz a necessidade de usar uma conta Run As e executar o gerenciamento da conta associada.

```powershell
    # Connect to Azure using the managed identities for Azure resources identity configured on the Azure VM that is hosting the hybrid runbook worker
    Connect-AzAccount -Identity

    # Get all VM names from the subscription
    Get-AzVM | Select Name
```

> [!NOTE]
> `Connect-AzAccount -Identity`funciona para um Trabalhador de Runbook Híbrido usando uma identidade atribuída ao sistema e uma única identidade atribuída pelo usuário. Se você usar várias identidades atribuídas pelo usuário no Hybrid `AccountId` Runbook `Connect-AzAccount` Worker, seu manual deverá especificar o parâmetro para selecionar uma identidade específica atribuída pelo usuário.

### <a name="automation-run-as-account"></a><a name="runas-script"></a>Conta de automação Executar como

Como parte do processo de compilação automatizada para implantação de recursos no Azure, você pode exigir acesso a sistemas locais para suportar uma tarefa ou conjunto de etapas em sua seqüência de implantação. Para fornecer autenticação contra o Azure usando a conta Executar as, você deve instalar o certificado de conta Run As.

O manual do PowerShell a seguir, chamado **Export-RunAsCertificateToHybridWorker,** exporta o certificado Run As de sua conta de Automação Azure. O runbook baixa e importa o certificado para a loja local de certificados de máquina em um Trabalhador de Runbook Híbrido que está conectado à mesma conta. Uma vez concluída essa etapa, o manual verifica se o trabalhador pode autenticar com sucesso o Azure usando a conta Run As.

```azurepowershell-interactive
<#PSScriptInfo
.VERSION 1.0
.GUID 3a796b9a-623d-499d-86c8-c249f10a6986
.AUTHOR Azure Automation Team
.COMPANYNAME Microsoft
.COPYRIGHT
.TAGS Azure Automation
.LICENSEURI
.PROJECTURI
.ICONURI
.EXTERNALMODULEDEPENDENCIES
.REQUIREDSCRIPTS
.EXTERNALSCRIPTDEPENDENCIES
.RELEASENOTES
#>

<#
.SYNOPSIS
Exports the Run As certificate from an Azure Automation account to a hybrid worker in that account.

.DESCRIPTION
This runbook exports the Run As certificate from an Azure Automation account to a hybrid worker in that account. Run this runbook on the hybrid worker where you want the certificate installed. This allows the use of the AzureRunAsConnection to authenticate to Azure and manage Azure resources from runbooks running on the hybrid worker.

.EXAMPLE
.\Export-RunAsCertificateToHybridWorker

.NOTES
LASTEDIT: 2016.10.13
#>

# Generate the password used for this certificate
Add-Type -AssemblyName System.Web -ErrorAction SilentlyContinue | Out-Null
$Password = [System.Web.Security.Membership]::GeneratePassword(25, 10)

# Stop on errors
$ErrorActionPreference = 'stop'

# Get the management certificate that will be used to make calls into Azure Service Management resources
$RunAsCert = Get-AutomationCertificate -Name "AzureRunAsCertificate"

# location to store temporary certificate in the Automation service host
$CertPath = Join-Path $env:temp  "AzureRunAsCertificate.pfx"

# Save the certificate
$Cert = $RunAsCert.Export("pfx",$Password)
Set-Content -Value $Cert -Path $CertPath -Force -Encoding Byte | Write-Verbose

Write-Output ("Importing certificate into $env:computername local machine root store from " + $CertPath)
$SecurePassword = ConvertTo-SecureString $Password -AsPlainText -Force
Import-PfxCertificate -FilePath $CertPath -CertStoreLocation Cert:\LocalMachine\My -Password $SecurePassword -Exportable | Write-Verbose

# Test to see if authentication to Azure Resource Manager is working
$RunAsConnection = Get-AutomationConnection -Name "AzureRunAsConnection"

Connect-AzAccount `
    -ServicePrincipal `
    -Tenant $RunAsConnection.TenantId `
    -ApplicationId $RunAsConnection.ApplicationId `
    -CertificateThumbprint $RunAsConnection.CertificateThumbprint | Write-Verbose

Set-AzContext -Subscription $RunAsConnection.SubscriptionID | Write-Verbose

# List automation accounts to confirm that Azure Resource Manager calls are working
Get-AzAutomationAccount | Select-Object AutomationAccountName
```

>[!NOTE]
>Para runbooks powershell e `Add-AzAccount` `Add-AzureRMAccount` são `Connect-AzAccount`pseudônimos para . Ao pesquisar seus itens da biblioteca, `Connect-AzAccount`se você `Add-AzAccount`não ver, você pode usar ou atualizar seus módulos em sua conta de Automação.

Para terminar de preparar a conta Run As:

1. Salve o **manual export-RunAsCertificateToHybridWorker** no seu computador com uma extensão **.ps1.**
2. Importe-o para sua conta de Automação.
3. Edite o manual, alterando `Password` o valor da variável para sua própria senha. 
4. Publique o manual.
5. Execute o manual, visando o grupo Hybrid Runbook Worker que executa e autentica runbooks usando a conta Executar As. 
6. Examine o fluxo de trabalho para ver se ele relata a tentativa de importar o certificado para a loja de máquinas local, e segue com várias linhas. Esse comportamento depende de quantas contas de Automação você define em sua assinatura e o grau de sucesso da autenticação.

## <a name="job-behavior-on-hybrid-runbook-workers"></a>Comportamento do trabalho em Trabalhadores híbridos de runbook

A Azure Automation lida com trabalhos em Trabalhadores híbridos de runbook um pouco diferente dos empregos executados em caixas de areia do Azure. Uma diferença fundamental é que não há limite de duração do trabalho nos trabalhadores do manual. Os runbooks executados em caixas de areia do Azure são limitados a três horas por causa da [participação justa](automation-runbook-execution.md#fair-share).

Para um manual de longa duração, você deseja ter certeza de que ele é resistente a uma possível reinicialização, por exemplo, se a máquina que hospeda o trabalhador reinicializa. Se a máquina host Hybrid Runbook Worker for reiniciada, qualquer trabalho de execução será reiniciado desde o início ou no último ponto de verificação dos runbooks do PowerShell Workflow. Depois que um trabalho de runbook é reiniciado mais de três vezes, ele é suspenso.

Lembre-se que os trabalhos para trabalhadores do runbook híbrido são executados sob a conta do sistema local no Windows ou a conta **de automação nx** no Linux. Para o Linux, você deve garantir que a conta **nxautomation** tenha acesso ao local onde os módulos do runbook são armazenados. Ao usar o cmdlet [install-module, certifique-se](/powershell/module/powershellget/install-module) `Scope` de especificar AllUsers para o parâmetro para garantir que a conta **nxautomation** tenha acesso. Para obter mais informações sobre o PowerShell no Linux, consulte [Problemas conhecidos para PowerShell em plataformas não-windows](https://docs.microsoft.com/powershell/scripting/whats-new/known-issues-ps6?view=powershell-6#known-issues-for-powershell-on-non-windows-platforms).

## <a name="starting-a-runbook-on-a-hybrid-runbook-worker"></a>Iniciando um runbook em um Trabalhador híbrido de runbook

[Iniciar um runbook no Azure Automation](automation-starting-a-runbook.md) descreve diferentes métodos para iniciar um runbook. A inicialização de um runbook em um Trabalhador de runbook híbrido usa uma opção **Executar na** opção que permite especificar o nome de um grupo hybrid Runbook Worker. Quando um grupo é especificado, um dos trabalhadores desse grupo recupera e executa o manual. Se o seu runbook não especificar essa opção, o Azure Automation executará o manual normalmente.

Ao iniciar um runbook no portal Azure, você é apresentado com a opção **Executar na** qual você pode selecionar **Azure** ou **Hybrid Worker**. Se você selecionar **Hybrid Worker,** você pode escolher o grupo Hybrid Runbook Worker a partir de uma gota.

Use `RunOn` o parâmetro com o [cmdlet Start-AzAutomationRunbook.](https://docs.microsoft.com/powershell/module/Az.Automation/Start-AzAutomationRunbook?view=azps-3.7.0) O exemplo a seguir usa o Windows PowerShell para iniciar um runbook chamado **Test-Runbook** em um grupo híbrido de trabalhador de runbook chamado MyHybridGroup.

```azurepowershell-interactive
Start-AzAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -RunOn "MyHybridGroup"
```

> [!NOTE]
> Você deve [baixar a versão mais recente do PowerShell](https://azure.microsoft.com/downloads/) se tiver uma anterior instalada. Instale esta versão apenas na estação de trabalho onde você está iniciando o runbook do PowerShell. Você não precisa instalá-lo no computador Hybrid Runbook Worker, a menos que você pretenda iniciar runbooks a partir deste computador.

## <a name="working-with-signed-runbooks-on-a-windows-hybrid-runbook-worker"></a>Trabalhando com runbooks assinados em um Trabalhador de Runbook Híbrido do Windows

Você pode configurar um Windows Hybrid Runbook Worker para executar apenas runbooks assinados.

> [!IMPORTANT]
> Após configurar um Hybrid Runbook Worker para executar somente runbooks assinados, os runbooks que não foram assinados não serão executados no trabalho.

### <a name="create-signing-certificate"></a>Criar certificado de assinatura

O exemplo a seguir cria um certificado autoassinado que pode ser usado para assinar runbooks. Este código cria o certificado e exporta-o para que o Trabalhador do Runbook Híbrido possa importá-lo mais tarde. A impressão digital também é devolvida para uso posterior no referenciamento do certificado.

```powershell
# Create a self-signed certificate that can be used for code signing
$SigningCert = New-SelfSignedCertificate -CertStoreLocation cert:\LocalMachine\my `
                                        -Subject "CN=contoso.com" `
                                        -KeyAlgorithm RSA `
                                        -KeyLength 2048 `
                                        -Provider "Microsoft Enhanced RSA and AES Cryptographic Provider" `
                                        -KeyExportPolicy Exportable `
                                        -KeyUsage DigitalSignature `
                                        -Type CodeSigningCert


# Export the certificate so that it can be imported to the hybrid workers
Export-Certificate -Cert $SigningCert -FilePath .\hybridworkersigningcertificate.cer

# Import the certificate into the trusted root store so the certificate chain can be validated
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\Root

# Retrieve the thumbprint for later use
$SigningCert.Thumbprint
```

### <a name="import-certificate-and-configure-workers-for-signature-validation"></a>Certificado de importação e configurar trabalhadores para validação de assinatura

Copie o certificado que você criou para cada Trabalhador híbrido do runbook em um grupo. Execute o script a seguir para importar o certificado e configure os trabalhadores para usar a validação de assinatura em runbooks.

```powershell
# Install the certificate into a location that will be used for validation.
New-Item -Path Cert:\LocalMachine\AutomationHybridStore
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\AutomationHybridStore

# Import the certificate into the trusted root store so the certificate chain can be validated
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\Root

# Configure the hybrid worker to use signature validation on runbooks.
Set-HybridRunbookWorkerSignatureValidation -Enable $true -TrustedCertStoreLocation "Cert:\LocalMachine\AutomationHybridStore"
```

### <a name="sign-your-runbooks-using-the-certificate"></a>Assine seus runbooks usando o certificado

Com o Hybrid Runbook Workers configurado para usar apenas runbooks assinados, você deve assinar runbooks que devem ser usados no Hybrid Runbook Worker. Use o seguinte código PowerShell de amostra para assinar esses runbooks.

```powershell
$SigningCert = ( Get-ChildItem -Path cert:\LocalMachine\My\<CertificateThumbprint>)
Set-AuthenticodeSignature .\TestRunbook.ps1 -Certificate $SigningCert
```

Quando um runbook foi assinado, você deve importá-lo para sua conta de Automação e publicá-lo com o bloco de assinatura. Para saber como importar os runbooks, consulte [Importando um runbook de um arquivo para a Automação do Azure](manage-runbooks.md#importing-a-runbook).

## <a name="working-with-signed-runbooks-on-a-linux-hybrid-runbook-worker"></a>Trabalhando com runbooks assinados em um Linux Hybrid Runbook Worker

Para poder trabalhar com runbooks assinados, um Linux Hybrid Runbook Worker deve ter o [GPG](https://gnupg.org/index.html) executável na máquina local.

> [!IMPORTANT]
> Após configurar um Hybrid Runbook Worker para executar somente runbooks assinados, os runbooks que não foram assinados não serão executados no trabalho.

### <a name="create-a-gpg-keyring-and-keypair"></a>Criar um token de autenticação e um par de chaves do GPG

Para criar o chaveiro GPG e o par de chaves, use a conta de **automação nxautomation** hybrid Runbook Worker.

1. Use o aplicativo sudo para fazer login como a conta **nxautomation.**

    ```bash
    sudo su – nxautomation
    ```

2. Uma vez que você esteja usando **nxautomation,** gere o par de tecla GPG. O GPG orienta você através das etapas. Você deve fornecer nome, endereço de e-mail, tempo de expiração e senha. Em seguida, você espera até que haja entropia suficiente na máquina para que a chave seja gerada.

    ```bash
    sudo gpg --generate-key
    ```

3. Como o diretório GPG foi gerado com sudo, você deve mudar seu proprietário para **nxautomation** usando o seguinte comando.

    ```bash
    sudo chown -R nxautomation ~/.gnupg
    ```

### <a name="make-the-keyring-available-to-the-hybrid-runbook-worker"></a>Disponibilize o chaveiro para o Híbrido Runbook Worker

Uma vez que o chaveiro tenha sido criado, disponibilize-o para o Hybrid Runbook Worker. Modifique o arquivo de configurações **/var/opt/microsoft/omsagent/state/automationworker/diy/worker.conf** para `[worker-optional]`incluir o seguinte código de exemplo na seção de arquivos .

```bash
gpg_public_keyring_path = /var/opt/microsoft/omsagent/run/.gnupg/pubring.kbx
```

### <a name="verify-that-signature-validation-is-on"></a>Verifique se a validação de assinatura está em

Se a validação de assinatura tiver sido desativada na máquina, você deve atilá-la executando o seguinte comando sudo. Substitua pelo `<LogAnalyticsworkspaceId>` seu ID de espaço de trabalho.

```bash
sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --true <LogAnalyticsworkspaceId>
```

### <a name="sign-a-runbook"></a>Assinar um runbook

Depois de configurar a validação de assinatura, use o seguinte comando GPG para assinar um runbook.

```bash
gpg –-clear-sign <runbook name>
```

O runbook assinado é chamado ** <runbook name>.asc**.

Agora você pode carregar o manual assinado para o Azure Automation e executá-lo como um runbook regular.

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre os métodos para iniciar um runbook, consulte [Iniciar um Runbook no Azure Automation](automation-starting-a-runbook.md).
* Para entender como usar o editor textual para trabalhar com runbooks powershell no Azure Automation, consulte [Edição de um Runbook na Automação Azure](automation-edit-textual-runbook.md).
* Se seus runbooks não forem concluídos com sucesso, revise o guia de solução de problemas para [falhas de execução de runbook](troubleshoot/hybrid-runbook-worker.md#runbook-execution-fails).
* Para obter mais informações sobre o PowerShell, incluindo os módulos de referência e aprendizagem do idioma, consulte o [PowerShell Docs](https://docs.microsoft.com/powershell/scripting/overview).
* Para obter uma referência de cmdlet PowerShell, consulte [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).
