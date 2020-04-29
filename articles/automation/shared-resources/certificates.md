---
title: Gerenciar o certificado na automação do Azure
description: Os certificados são armazenados com segurança na automação do Azure para que os runbooks ou as configurações DSC possam acessá-los para autenticação no Azure e em recursos de terceiros. Este artigo explica os detalhes de certificados e como trabalhar com elas na criação textual e gráfica.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 04/02/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 69bc1a0333365e15452c6d3b253266d37d99b608
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81732819"
---
# <a name="manage-certificates-in-azure-automation"></a>Gerenciar certificados na automação do Azure

Os certificados são armazenados com segurança na automação do Azure para que possam ser acessados por runbooks ou configurações DSC usando a atividade [Get-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationCertificate?view=azps-3.7.0) para recursos Azure Resource Manager. O armazenamento de certificado seguro permite criar runbooks e configurações DSC que usam certificados para autenticação ou adicioná-los ao Azure ou a recursos de terceiros.

Os ativos protegidos na Automação do Azure incluem credenciais, certificados, conexões e variáveis criptografadas. Esses ativos são criptografados e armazenados na Automação do Azure usando uma chave exclusiva que é gerada para cada conta de automação. Essa chave é armazenada em um Key Vault gerenciado pelo sistema. Antes de armazenar um ativo seguro, a chave é carregada do Key Vault e usada para criptografar o ativo. Esse processo é gerenciado pela Automação do Azure.

>[!NOTE]
>Este artigo foi atualizado para usar o novo módulo Az do Azure PowerShell. Você ainda pode usar o módulo AzureRM, que continuará a receber as correções de bugs até pelo menos dezembro de 2020. Para saber mais sobre o novo módulo Az e a compatibilidade com o AzureRM, confira [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para obter instruções de instalação do módulo AZ no seu Hybrid Runbook Worker, consulte [instalar o módulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para sua conta de automação, você pode atualizar seus módulos para a versão mais recente usando [como atualizar os módulos de Azure PowerShell na automação do Azure](../automation-update-azure-modules.md).

## <a name="az-powershell-cmdlets"></a>AZ cmdlets do PowerShell

Para AZ, os cmdlets na tabela a seguir são usados para criar e gerenciar ativos de credenciais de automação com o Windows PowerShell. Eles são fornecidos como parte do [módulo AZ. Automation](/powershell/azure/overview), que está disponível para uso em Runbooks de automação e configurações DSC.

|Cmdlet |Descrição|
| --- | ---|
|[Add-AzureCertificate](/powershell/module/servicemanagement/azure/add-azurecertificate)|Carrega um certificado de serviço para o serviço de nuvem especificado.|
|[Get-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationCertificate?view=azps-3.7.0)|Obtém informações sobre um certificado a ser usado em um runbook ou configuração DSC. Você só pode recuperar o certificado em si usando `Get-AutomationCertificate` a atividade.|
|[New-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationCertificate?view=azps-3.7.0)|Cria um novo certificado na automação do Azure.|
|[Remove-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationCertificate?view=azps-3.7.0)|Remove um certificado da Automação do Azure.|
|[Set-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationCertificate?view=azps-3.7.0)|Define as propriedades de um certificado existente, incluindo o carregamento do arquivo de certificado e a definição da senha para um arquivo **. pfx** .|

## <a name="activities"></a>Atividades

As atividades na tabela a seguir são usadas para acessar certificados em um runbook e em configurações de DSC.

| Atividades | Descrição |
|:---|:---|
|`Get-AutomationCertificate`|Obtém um certificado a ser usado em um runbook ou configuração DSC. Retorna um objeto [System.Security.Cryptography.X509Certificates.X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2).|

> [!NOTE] 
> Evite usar variáveis no `Name` parâmetro de uma configuração de `Get-AutomationCertificate` runbook ou DSC. O uso de variáveis nesse parâmetro complica a descoberta de dependências entre runbooks ou configurações DSC e variáveis de automação em tempo de design.

## <a name="python-2-functions"></a>Funções do Python 2

A função na tabela a seguir é usada para acessar certificados em um runbook Python 2.

| Função | Descrição |
|:---|:---|
| `automationassets.get_automation_certificate` | Recupera informações sobre um ativo de certificado. |

> [!NOTE]
> Você deve importar o `automationassets` módulo no início do seu runbook do Python para acessar as funções de ativo.

## <a name="creating-a-new-certificate"></a>Criando um novo certificado

Ao criar um novo certificado, você carrega um arquivo .cer ou .pfx na Automação do Azure. Se marcar certificado como exportável, você poderá transferi-lo do repositório de certificados da Automação do Azure. Se não for exportável, ele só poderá ser usado para assinatura dentro do runbook ou da configuração DSC. A automação do Azure requer que o certificado tenha o provedor **Microsoft Enhanced RSA e o provedor criptográfico do AES**.

### <a name="create-a-new-certificate-with-the-azure-portal"></a>Criar um novo certificado com o portal do Azure

1. Na sua conta de automação, clique em **ativos** para abrir a página ativos.
2. Selecione **certificados** para abrir a página certificados.
3. Clique em **Adicionar um certificado** na parte superior da página.
4. Digite um nome para o certificado no campo **nome** .
5. Para procurar um arquivo **. cer** ou **. pfx** , clique em **selecionar um arquivo** em **carregar um arquivo de certificado**. Se você selecionar um arquivo **. pfx**, especifique uma senha e indique se ela pode ser exportada.
6. Clique em **Criar** para salvar o novo ativo de certificado.

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

## <a name="using-a-certificate"></a>Usando um certificado

Para usar um certificado, use a `Get-AutomationCertificate` atividade. Você não pode usar o cmdlet [Get-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationCertificate?view=azps-3.7.0) , pois ele retorna informações sobre o ativo de certificado, mas não o próprio certificado.

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

Adicione uma `Get-AutomationCertificate` atividade a um runbook gráfico clicando com o botão direito do mouse no certificado no painel Biblioteca e selecionando **Adicionar à tela**.

![Adicionar certificado à tela](../media/certificates/automation-certificate-add-to-canvas.png)

A imagem a seguir mostra um exemplo do uso de um certificado em um runbook gráfico. Isso é o mesmo que o exemplo anterior que mostra como adicionar um certificado a um serviço de nuvem de um runbook textual.

![Exemplo de criação gráfica](../media/certificates/graphical-runbook-add-certificate.png)

### <a name="python-2-example"></a>Exemplo de Python 2

O exemplo a seguir mostra como acessar certificados em runbooks Python2.

```python
# get a reference to the Azure Automation certificate
cert = automationassets.get_automation_certificate("AzureRunAsCertificate")

# returns the binary cert content  
print cert
```

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre como trabalhar com links para controlar o fluxo lógico de atividades executadas por seu runbook, consulte [links em criação gráfica](../automation-graphical-authoring-intro.md#links-and-workflow). 
