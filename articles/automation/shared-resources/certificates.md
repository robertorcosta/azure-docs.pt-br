---
title: Gerenciar certificados na automação do Azure
description: A automação do Azure armazena certificados com segurança, de modo que os runbooks ou as configurações DSC possam acessá-los para autenticar no Azure e em recursos de terceiros. Este artigo explica os detalhes dos certificados e como trabalhar com eles na criação gráfica e textual.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 04/02/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 2793679fb4588d00ea4e37340b19183398cb9d90
ms.sourcegitcommit: 602e6db62069d568a91981a1117244ffd757f1c2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82864310"
---
# <a name="manage-certificates-in-azure-automation"></a>Gerenciar certificados na automação do Azure

A automação do Azure armazena certificados com segurança para acesso por runbooks e configurações DSC, usando o cmdlet [Get-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationCertificate?view=azps-3.7.0) para recursos de Azure Resource Manager. O armazenamento de certificado seguro permite criar runbooks e configurações DSC que usam certificados para autenticação ou adicioná-los ao Azure ou a recursos de terceiros.

>[!NOTE]
>Os ativos protegidos na Automação do Azure incluem credenciais, certificados, conexões e variáveis criptografadas. Esses ativos são criptografados e armazenados na automação usando uma chave exclusiva que é gerada para cada conta de automação. A automação armazena a chave no serviço de Key Vault gerenciado pelo sistema. Antes de armazenar um ativo seguro, a automação carrega a chave de Key Vault e, em seguida, a usa para criptografar o ativo. 

>[!NOTE]
>Este artigo mostra como usar o módulo Azure PowerShell AZ. Você ainda pode usar o módulo AzureRM. Para saber mais sobre o módulo AZ e a compatibilidade do AzureRM, confira [introdução ao novo módulo Azure PowerShell AZ](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para obter instruções de instalação do módulo AZ no seu Hybrid Runbook Worker, consulte [instalar o módulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para sua conta de automação, você pode atualizar seus módulos para a versão mais recente usando [como atualizar os módulos Azure PowerShell na automação do Azure](../automation-update-azure-modules.md).

## <a name="powershell-cmdlets-to-access-certificates"></a>Cmdlets do PowerShell para acessar certificados

Os cmdlets na tabela a seguir criam e gerenciam certificados de automação com o PowerShell. Eles são fornecidos como parte dos [módulos AZ](modules.md#az-modules).

|Cmdlet |Descrição|
| --- | ---|
|[Get-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationCertificate?view=azps-3.7.0)|Obtém informações sobre um certificado a ser usado em um runbook ou configuração DSC. Você só pode recuperar o certificado em si usando o cmdlet `Get-AutomationCertificate` interno.|
|[New-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationCertificate?view=azps-3.7.0)|Cria um novo certificado na automação.|
|[Remove-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationCertificate?view=azps-3.7.0)|Remove um certificado da automação.|
|[Set-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationCertificate?view=azps-3.7.0)|Define as propriedades de um certificado existente, incluindo o carregamento do arquivo de certificado e a definição da senha para um arquivo **. pfx** .|

O cmdlet [Add-azurecertificate pelo](/powershell/module/servicemanagement/azure/add-azurecertificate) também pode ser usado para carregar um certificado de serviço para o serviço de nuvem especificado.

## <a name="internal-cmdlets-to-access-certificates"></a>Cmdlets internos para acessar certificados

O cmdlet interno na tabela a seguir é usado para acessar certificados em seus runbooks. Esse cmdlet vem com o módulo `Orchestrator.AssetManagement.Cmdlets`global. Para obter mais informações, consulte [cmdlets internos](modules.md#internal-cmdlets).

| Cmdlet interno | Descrição |
|:---|:---|
|`Get-AutomationCertificate`|Obtém um certificado a ser usado em um runbook ou configuração DSC. Retorna um objeto [System.Security.Cryptography.X509Certificates.X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2).|

> [!NOTE] 
> Evite usar variáveis no `Name` parâmetro de uma configuração de `Get-AutomationCertificate` runbook ou DSC. Essas variáveis podem complicar a descoberta de dependências entre runbooks ou configurações DSC e variáveis de automação em tempo de design.

## <a name="python-2-functions-to-access-certificates"></a>Funções do Python 2 para acessar certificados

Use a função na tabela a seguir para acessar certificados em um runbook Python 2.

| Função | Descrição |
|:---|:---|
| `automationassets.get_automation_certificate` | Recupera informações sobre um ativo de certificado. |

> [!NOTE]
> Você deve importar o `automationassets` módulo no início do seu runbook do Python para acessar as funções de ativo.

## <a name="create-a-new-certificate"></a>Criar um novo certificado

Ao criar um novo certificado, você carrega um arquivo. cer ou. pfx na automação. Se você marcar o certificado como exportável, poderá transferi-lo para fora do repositório de certificados de automação. Se não for exportável, ele só poderá ser usado para assinatura dentro do runbook ou da configuração DSC. A automação requer que o certificado tenha o provedor **Microsoft Enhanced RSA e o provedor criptográfico do AES**.

### <a name="create-a-new-certificate-with-the-azure-portal"></a>Criar um novo certificado com o portal do Azure

1. Na sua conta de automação, selecione **ativos** > **certificados** > **Adicionar um certificado**.
1. No campo **nome** , digite um nome para o certificado.
1. Para procurar um arquivo **. cer** ou **. pfx** , em **carregar um arquivo de certificado**, escolha **selecionar um arquivo**. Se você selecionar um arquivo **. pfx** , especifique uma senha e indique se ela pode ser exportada.
1. Selecione **criar** para salvar o novo ativo de certificado.

### <a name="create-a-new-certificate-with-powershell"></a>Criar um novo certificado com o PowerShell

O exemplo a seguir mostra como criar um novo Certificado de automação e marcá-lo como exportável. Este exemplo importa um arquivo **. pfx** existente.

```powershell-interactive
$certificateName = 'MyCertificate'
$PfxCertPath = '.\MyCert.pfx'
$CertificatePassword = ConvertTo-SecureString -String 'P@$$w0rd' -AsPlainText -Force
$ResourceGroup = "ResourceGroup01"

New-AzAutomationCertificate -AutomationAccountName "MyAutomationAccount" -Name $certificateName -Path $PfxCertPath –Password $CertificatePassword -Exportable -ResourceGroupName $ResourceGroup
```

### <a name="create-a-new-certificate-with-a-resource-manager-template"></a>Criar um novo certificado com um modelo do Resource Manager

O exemplo a seguir demonstra como implantar um certificado em sua conta de automação usando um modelo do Resource Manager por meio do PowerShell:

```powershell-interactive
$AutomationAccountName = "<automation account name>"
$PfxCertPath = '<PFX cert path'
$CertificatePassword = '<password>'
$certificateName = '<certificate name>'
$AutomationAccountName = '<automation account name>'
$flags = [System.Security.Cryptography.X509Certificates.X509KeyStorageFlags]::Exportable `
    -bor [System.Security.Cryptography.X509Certificates.X509KeyStorageFlags]::PersistKeySet `
    -bor [System.Security.Cryptography.X509Certificates.X509KeyStorageFlags]::MachineKeySet
# Load the certificate into memory
$PfxCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($PfxCertPath, $CertificatePassword, $flags)
# Export the certificate and convert into base 64 string
$Base64Value = [System.Convert]::ToBase64String($PfxCert.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12))
$Thumbprint = $PfxCert.Thumbprint


$json = @"
{
    '`$schema': 'https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#',
    'contentVersion': '1.0.0.0',
    'resources': [
        {
            'name': '$AutomationAccountName/$certificateName',
            'type': 'Microsoft.Automation/automationAccounts/certificates',
            'apiVersion': '2015-10-31',
            'properties': {
                'base64Value': '$Base64Value',
                'thumbprint': '$Thumbprint',
                'isExportable': true
            }
        }
    ]
}
"@

$json | out-file .\template.json
New-AzResourceGroupDeployment -Name NewCert -ResourceGroupName TestAzureAuto -TemplateFile .\template.json
```

## <a name="get-a-certificate"></a>Obter um certificado

Para recuperar um certificado, use o cmdlet `Get-AutomationCertificate` interno. Você não pode usar o cmdlet [Get-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationCertificate?view=azps-3.7.0) , pois ele retorna informações sobre o ativo de certificado, mas não o próprio certificado.

### <a name="textual-runbook-example"></a>Exemplo de runbook textual

O exemplo a seguir mostra como adicionar um certificado a um serviço de nuvem em um runbook. Nesse exemplo, a senha é recuperada de uma variável de automação criptografada.

```powershell-interactive
$serviceName = 'MyCloudService'
$cert = Get-AutomationCertificate -Name 'MyCertificate'
$certPwd = Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyCertPassword'
Add-AzureCertificate -ServiceName $serviceName -CertToDeploy $cert
```

### <a name="graphical-runbook-example"></a>Exemplo de runbook gráfico

Adicione uma atividade para o cmdlet `Get-AutomationCertificate` interno a um runbook gráfico clicando com o botão direito do mouse no certificado no painel Biblioteca e selecionando **Adicionar à tela**.

![Captura de tela da adição de um certificado à Canvas](../media/certificates/automation-certificate-add-to-canvas.png)

A imagem a seguir mostra um exemplo do uso de um certificado em um runbook gráfico. 

![Captura de tela de um exemplo de criação gráfica](../media/certificates/graphical-runbook-add-certificate.png)

### <a name="python-2-example"></a>Exemplo de Python 2

O exemplo a seguir mostra como acessar certificados em runbooks do Python 2.

```python
# get a reference to the Azure Automation certificate
cert = automationassets.get_automation_certificate("AzureRunAsCertificate")

# returns the binary cert content  
print cert
```

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre os cmdlets usados para acessar certificados, consulte [gerenciar módulos na automação do Azure](modules.md).
* Para obter informações gerais sobre runbooks, consulte [execução de runbook na automação do Azure](../automation-runbook-execution.md).
* Para obter detalhes sobre as configurações DSC, consulte [visão geral da configuração de estado](../automation-dsc-overview.md). 
