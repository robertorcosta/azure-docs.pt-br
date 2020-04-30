---
title: Gerenciar conexões na automação do Azure
description: Os ativos de conexão na Automação do Azure contêm as informações necessárias para se conectar a um aplicativo ou serviço externo de um runbook ou uma configuração DSC. Este artigo explica os detalhes de conexões e como trabalhar com elas na criação textual e gráfica.
services: automation
ms.subservice: shared-capabilities
ms.date: 01/13/2020
ms.topic: conceptual
ms.openlocfilehash: 39a41a60f4cabe995ebd458c4b906438d1e31bde
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82097108"
---
# <a name="manage-connections-in-azure-automation"></a>Gerenciar conexões na automação do Azure

Um ativo de conexão da Automação contém as informações necessárias para se conectar a um aplicativo ou serviço externo de um runbook ou da configuração DSC. Isso pode incluir as informações necessárias para autenticação, como um nome de usuário e uma senha, além das informações de conexão, como uma URL ou uma porta. O valor de uma conexão é manter todas as propriedades para se conectar a um aplicativo específico em um ativo, em vez de criar diversas variáveis. O usuário pode editar os valores para uma conexão em um único local, e você pode transmitir o nome de uma conexão para um runbook ou uma configuração DSC em um único parâmetro. As propriedades de uma conexão podem ser acessadas no runbook ou na configuração DSC `Get-AutomationConnection` com a atividade.

Ao criar uma conexão, você deve especificar um tipo de conexão. O tipo de conexão é um modelo que define um conjunto de propriedades. A conexão define valores para cada propriedade definida em seu tipo de conexão. Tipos de conexão são adicionados à automação do Azure nos módulos de integração ou criados com a [API de automação do Azure](/previous-versions/azure/reference/mt163818(v=azure.100)) se o módulo de integração inclui um tipo de conexão e é importado para sua conta de automação. Caso contrário, você deve criar um arquivo de metadados para especificar um tipo de conexão de automação. Para obter mais informações sobre isso, consulte [módulos de integração](automation-integration-modules.md).

>[!NOTE]
>Os ativos protegidos na Automação do Azure incluem credenciais, certificados, conexões e variáveis criptografadas. Esses ativos são criptografados e armazenados na automação do Azure usando uma chave exclusiva que é gerada para cada conta de automação. Essa chave é armazenada em um Key Vault gerenciado pelo sistema. Antes de armazenar um ativo seguro, a chave é carregada do Key Vault e usada para criptografar o ativo. Esse processo é gerenciado pela Automação do Azure.

>[!NOTE]
>Este artigo foi atualizado para usar o novo módulo Az do Azure PowerShell. Você ainda pode usar o módulo AzureRM, que continuará a receber as correções de bugs até pelo menos dezembro de 2020. Para saber mais sobre o novo módulo Az e a compatibilidade com o AzureRM, confira [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para obter instruções de instalação do módulo AZ no seu Hybrid Runbook Worker, consulte [instalar o módulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para sua conta de automação, você pode atualizar seus módulos para a versão mais recente usando [como atualizar os módulos de Azure PowerShell na automação do Azure](automation-update-azure-modules.md).

## <a name="connection-types"></a>Tipos de conexão

Há três tipos de conexões internas disponíveis na automação do Azure:

* **Azure** – essa conexão pode ser usada para gerenciar os recursos clássicos.
* **AzureClassicCertificate** – essa conexão é usada pela conta **AzureClassicRunAs**.
* **AzureServicePrincipal** – essa conexão é usada pela conta **AzureRunAs**.

Na maioria dos casos, você não precisa criar um recurso de conexão porque ele é criado quando você cria uma [conta Executar como](manage-runas-account.md).

## <a name="windows-powershell-cmdlets"></a>Cmdlets do Windows PowerShell

Os cmdlets na tabela a seguir são usados para criar e gerenciar conexões de Automação com o Windows PowerShell. Eles são fornecidos como parte do [módulo Azure PowerShell](/powershell/azure/overview), que está disponível para uso em Runbooks de automação e configurações DSC.

|Cmdlet|Descrição|
|---|---|
|[Get-AzAutomationConnection](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationconnection?view=azps-3.7.0)|Recupera uma conexão. Inclui uma tabela de hash com os valores dos campos de conexão.|
|[New-AzAutomationConnection](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationconnection?view=azps-3.7.0)|Cria uma nova conexão.|
|[Remove-AzAutomationConnection](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationConnection?view=azps-3.7.0)|Remove uma conexão existente.|
|[Set-AzAutomationConnectionFieldValue](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationConnectionFieldValue?view=azps-3.7.0)|Define o valor de determinado campo para uma conexão existente.|

## <a name="activities"></a>Atividades

As atividades na tabela a seguir são usadas para acessar conexões em um runbook ou em uma configuração DSC.

|Atividades|Descrição|
|---|---|
|`Get-AutomationConnection` | Obtém uma conexão a ser usada. Retorna uma tabela de hash com as propriedades da conexão.|

>[!NOTE]
>Evite usar variáveis com o `Name` parâmetro de `Get-AutomationConnection`. O uso desse parâmetro pode complicar a descoberta de dependências entre runbooks ou configurações DSC e ativos de conexão em tempo de design.

## <a name="python-2-functions"></a>Funções do Python 2

A função na tabela a seguir é usada para acessar conexões em um runbook Python 2.

| Função | Descrição |
|:---|:---|
| `automationassets.get_automation_connection` | Recupera uma conexão. Retorna um dicionário com as propriedades da conexão. |

> [!NOTE]
> Você deve importar o `automationassets` módulo na parte superior do seu runbook do Python para acessar as funções de ativo.

## <a name="creating-a-new-connection"></a>Criando uma nova conexão

### <a name="create-a-new-connection-with-the-azure-portal"></a>Criar uma nova conexão com o portal do Azure

1. Na sua conta de automação, clique na parte **ativos** para abrir a folha **ativos** .
2. Clique na parte **Conexões** para abrir a folha **onexões**.
3. Clique em **Adicionar uma conexão** na parte superior da folha.
4. No menu suspenso **Tipo** , selecione o tipo de conexão que você deseja criar. O formulário apresentará as propriedades para esse tipo específico.
5. Preencha o formulário e clique em **Criar** para salvar a nova conexão.

### <a name="create-a-new-connection-with-windows-powershell"></a>Criar uma nova conexão com o Windows PowerShell

Crie uma nova conexão com o Windows PowerShell usando `New-AzAutomationConnection` o cmdlet. Esse cmdlet tem um parâmetro chamado `ConnectionFieldValues` que espera uma [tabela de hash](https://technet.microsoft.com/library/hh847780.aspx) definindo valores para cada uma das propriedades definidas pelo tipo de conexão.

Você pode usar os seguintes comandos de exemplo como uma alternativa para criar a conta Executar como do portal para criar um novo ativo de conexão.

```powershell
$ConnectionAssetName = "AzureRunAsConnection"
$ConnectionFieldValues = @{"ApplicationId" = $Application.ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Cert.Thumbprint; "SubscriptionId" = $SubscriptionId}
New-AzAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -ConnectionTypeName AzureServicePrincipal -ConnectionFieldValues $ConnectionFieldValues
```

Quando você cria sua conta de automação, ela inclui vários módulos globais por padrão, juntamente com o tipo `AzureServicePrincipal` de conexão para `AzureRunAsConnection` criar o ativo de conexão. Se você tentar criar um novo ativo de conexão para se conectar a um serviço ou aplicativo com um método de autenticação diferente, a operação falhará porque o tipo de conexão ainda não está definido em sua conta de automação. Para obter mais informações sobre como criar seu próprio tipo de conexão para um módulo de [Galeria do PowerShell](https://www.powershellgallery.com) personalizado, consulte [módulos de integração](automation-integration-modules.md).

## <a name="using-a-connection-in-a-runbook-or-dsc-configuration"></a>Usando uma conexão em um runbook ou configuração DSC

Recupere uma conexão em um runbook ou configuração DSC com o `Get-AutomationConnection` cmdlet. Você não pode usar `Get-AzAutomationConnection` a atividade. Essa atividade recupera os valores dos diferentes campos na conexão e os retorna como uma tabela de [hash](https://go.microsoft.com/fwlink/?LinkID=324844). Essa tabela de hash pode ser usada com os comandos apropriados no runbook ou na configuração DSC.

### <a name="textual-runbook-sample"></a>Exemplo de runbook textual

Os comandos de exemplo a seguir mostram como usar a conta Executar como mencionada anteriormente para autenticar com os recursos do Azure Resource Manager em seu runbook. Eles usam o ativo de conexão que representa a conta Executar como, que faz referência à entidade de serviço baseada em certificado, não às credenciais.

```powershell
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
```

> [!NOTE]
> Para runbooks não gráficos do PowerShell, `Add-AzAccount` e `Add-AzureRMAccount` são aliases para [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0). Você pode usar esses cmdlets ou pode [atualizar seus módulos](automation-update-azure-modules.md) em sua conta de automação para as versões mais recentes. Talvez seja necessário atualizar seus módulos mesmo se você acabou de criar uma nova conta de automação.

### <a name="graphical-runbook-samples"></a>Exemplos de runbook gráfico

Você adiciona uma `Get-AutomationConnection` atividade a um runbook gráfico clicando com o botão direito do mouse na conexão no painel Biblioteca do editor gráfico e selecionando **Adicionar à tela**.

![adicionar à tela](media/automation-connections/connection-add-canvas.png)

A imagem a seguir mostra um exemplo do uso de uma conexão em um runbook gráfico. Este é o mesmo exemplo mostrado acima para autenticação usando a conta Executar como com um runbook textual. Este exemplo usa o `Constant value` conjunto de dados para `Get RunAs Connection` a atividade que usa um objeto de conexão para autenticação. Um [link de pipeline](automation-graphical-authoring-intro.md#links-and-workflow) é usado aqui, `ServicePrincipalCertificate` pois o conjunto de parâmetros está esperando um único objeto.

![obter conexões](media/automation-connections/automation-get-connection-object.png)

### <a name="python-2-runbook-sample"></a>Exemplo de runbook do Python 2

O exemplo a seguir mostra como autenticar usando a conexão executar como em um runbook do Python 2.

```python
""" Tutorial to show how to authenticate against Azure resource manager resources """
import azure.mgmt.resource
import automationassets

def get_automation_runas_credential(runas_connection):
    """ Returns credentials to authenticate against Azure resoruce manager """
    from OpenSSL import crypto
    from msrestazure import azure_active_directory
    import adal

    # Get the Azure Automation Run As service principal certificate
    cert = automationassets.get_automation_certificate("AzureRunAsCertificate")
    pks12_cert = crypto.load_pkcs12(cert)
    pem_pkey = crypto.dump_privatekey(
        crypto.FILETYPE_PEM, pks12_cert.get_privatekey())

    # Get Run As connection information for the Azure Automation service principal
    application_id = runas_connection["ApplicationId"]
    thumbprint = runas_connection["CertificateThumbprint"]
    tenant_id = runas_connection["TenantId"]

    # Authenticate with service principal certificate
    resource = "https://management.core.windows.net/"
    authority_url = ("https://login.microsoftonline.com/" + tenant_id)
    context = adal.AuthenticationContext(authority_url)
    return azure_active_directory.AdalAuthentication(
        lambda: context.acquire_token_with_client_certificate(
            resource,
            application_id,
            pem_pkey,
            thumbprint)
    )


# Authenticate to Azure using the Azure Automation Run As service principal
runas_connection = automationassets.get_automation_connection(
    "AzureRunAsConnection")
azure_credential = get_automation_runas_credential(runas_connection)
```

## <a name="next-steps"></a>Próximas etapas

- Revise [Links na criação gráfica](automation-graphical-authoring-intro.md#links-and-workflow) para entender como direcionar e controlar o fluxo de lógica em seus runbooks.
* Para obter uma referência de cmdlet do PowerShell, consulte [AZ. Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).
- Para saber mais sobre o uso de módulos do PowerShell pela automação do Azure e práticas recomendadas para a criação de seus próprios módulos do PowerShell, para que funcionem como Módulos de integração na automação do Azure, veja [Módulos de integração](automation-integration-modules.md).