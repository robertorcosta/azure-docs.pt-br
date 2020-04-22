---
title: Gerenciar certificado no Azure Automation
description: Os certificados são armazenados com segurança no Azure Automation para que os runbooks ou configurações de DSC possam acessá-los para autenticar contra recursos do Azure e de terceiros. Este artigo explica os detalhes de certificados e como trabalhar com elas na criação textual e gráfica.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 04/02/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 69bc1a0333365e15452c6d3b253266d37d99b608
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732819"
---
# <a name="manage-certificates-in-azure-automation"></a>Gerenciar certificados no Azure Automation

Os certificados são armazenados com segurança no Azure Automation para que possam ser acessados por runbooks ou configurações de DSC usando a atividade [Get-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationCertificate?view=azps-3.7.0) para recursos do Azure Resource Manager. O armazenamento seguro de certificados permite criar runbooks e configurações de DSC que usam certificados para autenticação ou adicioná-los aos recursos do Azure ou de terceiros.

Os ativos protegidos na Automação do Azure incluem credenciais, certificados, conexões e variáveis criptografadas. Esses ativos são criptografados e armazenados na Automação do Azure usando uma chave exclusiva que é gerada para cada conta de automação. Esta chave é armazenada em um cofre de chaves gerenciado pelo sistema. Antes de armazenar um ativo seguro, a chave é carregada do Key Vault e usada para criptografar o ativo. Esse processo é gerenciado pela Automação do Azure.

>[!NOTE]
>Este artigo foi atualizado para usar o novo módulo Az do Azure PowerShell. Você ainda pode usar o módulo AzureRM, que continuará a receber as correções de bugs até pelo menos dezembro de 2020. Para saber mais sobre o novo módulo Az e a compatibilidade com o AzureRM, confira [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para obter instruções de instalação do módulo AZ no trabalhador do runbook híbrido, consulte [Instalar o Módulo PowerShell do Azure](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para sua conta de Automação, você pode atualizar seus módulos para a versão mais recente usando [Como atualizar módulos Azure PowerShell no Azure Automation](../automation-update-azure-modules.md).

## <a name="az-powershell-cmdlets"></a>Cmdlets Az PowerShell

Para Az, os cmdlets na tabela a seguir são usados para criar e gerenciar ativos de credencial de automação com o Windows PowerShell. Eles são enviados como parte do [módulo Az.Automation,](/powershell/azure/overview)que está disponível para uso em runbooks de automação e configurações de DSC.

|Cmdlet |Descrição|
| --- | ---|
|[Add-AzureCertificate](/powershell/module/servicemanagement/azure/add-azurecertificate)|Carrega um certificado de serviço para o serviço de nuvem especificado.|
|[Get-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationCertificate?view=azps-3.7.0)|Obtém informações sobre um certificado a ser usado em um runbook ou configuração DSC. Você só pode recuperar o `Get-AutomationCertificate` certificado em si usando a atividade.|
|[Certificado de automação da New-Az](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationCertificate?view=azps-3.7.0)|Cria um novo certificado no Azure Automation.|
|[Certificado de automação remove-Az](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationCertificate?view=azps-3.7.0)|Remove um certificado da Automação do Azure.|
|[Certificado de automação set-Az](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationCertificate?view=azps-3.7.0)|Define as propriedades de um certificado existente, incluindo o upload do arquivo do certificado e a definição da senha de um arquivo **.pfx.**|

## <a name="activities"></a>Atividades

As atividades na tabela a seguir são usadas para acessar certificados em um runbook e em configurações de DSC.

| Atividades | Descrição |
|:---|:---|
|`Get-AutomationCertificate`|Obtém um certificado a ser usado em um runbook ou configuração DSC. Retorna um objeto [System.Security.Cryptography.X509Certificates.X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2).|

> [!NOTE] 
> Você deve evitar usar `Name` variáveis no `Get-AutomationCertificate` parâmetro de uma configuração de runbook ou DSC. O uso de variáveis neste parâmetro complica a descoberta de dependências entre runbooks ou configurações de DSC e variáveis de Automação na hora do design.

## <a name="python-2-functions"></a>Funções python 2

A função na tabela a seguir é usada para acessar certificados em um runbook Python 2.

| Função | Descrição |
|:---|:---|
| `automationassets.get_automation_certificate` | Recupera informações sobre um ativo de certificado. |

> [!NOTE]
> Você deve `automationassets` importar o módulo no início do seu runbook Python para acessar as funções de ativos.

## <a name="creating-a-new-certificate"></a>Criando um novo certificado

Ao criar um novo certificado, você carrega um arquivo .cer ou .pfx na Automação do Azure. Se marcar certificado como exportável, você poderá transferi-lo do repositório de certificados da Automação do Azure. Se não for exportável, então ele só poderá ser usado para assinar dentro do manual ou da configuração do DSC. A Azure Automation requer o certificado para que o provedor **Microsoft Enhanced RSA e AES Cryptographic Provider**.

### <a name="create-a-new-certificate-with-the-azure-portal"></a>Crie um novo certificado com o portal Azure

1. Em sua conta de Automação, clique **em Ativos** para abrir a página Ativos.
2. Selecione **Certificados** para abrir a página Certificados.
3. Clique em **Adicionar um certificado** no topo da página.
4. Digite um nome para o certificado no campo **Nome.**
5. Para procurar um arquivo **.cer** ou **.pfx,** clique **em Selecionar um arquivo** em Upload de um arquivo de **certificado**. Se você selecionar um arquivo **.pfx,** especifique uma senha e indique se ela pode ser exportada.
6. Clique em **Criar** para salvar o novo ativo de certificado.

### <a name="create-a-new-certificate-with-powershell"></a>Crie um novo certificado com o PowerShell

O exemplo a seguir mostra como criar um novo Certificado de automação e marcá-lo como exportável. Este exemplo importa um arquivo **.pfx** existente.

```powershell-interactive
$certificateName = 'MyCertificate'
$PfxCertPath = '.\MyCert.pfx'
$CertificatePassword = ConvertTo-SecureString -String 'P@$$w0rd' -AsPlainText -Force
$ResourceGroup = "ResourceGroup01"

New-AzAutomationCertificate -AutomationAccountName "MyAutomationAccount" -Name $certificateName -Path $PfxCertPath –Password $CertificatePassword -Exportable -ResourceGroupName $ResourceGroup
```

### <a name="create-a-new-certificate-with-a-resource-manager-template"></a>Crie um novo certificado com um modelo de Gerenciador de Recursos

O exemplo a seguir demonstra como implantar um certificado em sua conta de Automação usando um modelo de Gerenciador de Recursos através do PowerShell:

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

## <a name="using-a-certificate"></a>Usando um certificado

Para usar um certificado, use a `Get-AutomationCertificate` atividade. Você não pode usar o [cmdlet Get-AzAutomationCertificate,](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationCertificate?view=azps-3.7.0) uma vez que ele retorna informações sobre o ativo do certificado, mas não o certificado em si.

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

Adicione `Get-AutomationCertificate` uma atividade a um runbook gráfico clicando com o botão direito do mouse no certificado no painel biblioteca e selecionando **Adicionar à tela**.

![Adicionar certificado à tela](../media/certificates/automation-certificate-add-to-canvas.png)

A imagem a seguir mostra um exemplo do uso de um certificado em um runbook gráfico. Este é o mesmo exemplo anterior que mostra como adicionar um certificado a um serviço de nuvem a partir de um runbook textual.

![Exemplo de criação gráfica](../media/certificates/graphical-runbook-add-certificate.png)

### <a name="python-2-example"></a>Exemplo python 2

O exemplo a seguir mostra como acessar certificados em runbooks Python2.

```python
# get a reference to the Azure Automation certificate
cert = automationassets.get_automation_certificate("AzureRunAsCertificate")

# returns the binary cert content  
print cert
```

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre como trabalhar com links para controlar o fluxo lógico de atividades realizadas pelo seu runbook, consulte [Links na autoria gráfica](../automation-graphical-authoring-intro.md#links-and-workflow). 
