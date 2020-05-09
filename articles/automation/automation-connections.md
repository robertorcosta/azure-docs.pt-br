---
title: Gerenciar conexões na automação do Azure
description: Os ativos de conexão na Automação do Azure contêm as informações necessárias para se conectar a um aplicativo ou serviço externo de um runbook ou uma configuração DSC. Este artigo explica os detalhes de conexões e como trabalhar com elas na criação textual e gráfica.
services: automation
ms.subservice: shared-capabilities
ms.date: 01/13/2020
ms.topic: conceptual
ms.custom: has-adal-ref
ms.openlocfilehash: b00dd226306ed639757666cc4f826b0d7a0e5711
ms.sourcegitcommit: d662eda7c8eec2a5e131935d16c80f1cf298cb6b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82651818"
---
# <a name="manage-connections-in-azure-automation"></a>Gerenciar conexões na automação do Azure

Um ativo de conexão de automação do Azure contém as informações listadas abaixo. Essas informações são necessárias para a conexão com um serviço ou aplicativo externo de um runbook ou configuração DSC. 

* Informações necessárias para autenticação, como nome de usuário e senha
* Informações de conexão, como URL ou porta

O ativo de conexão mantém todas as propriedades para se conectar a um aplicativo específico, tornando-o desnecessário para criar várias variáveis. Você pode editar os valores para uma conexão em um único local e pode passar o nome de uma conexão para um runbook ou uma configuração DSC em um só parâmetro. O runbook ou a configuração acessa as propriedades de uma conexão usando o cmdlet `Get-AutomationConnection` interno.

Ao criar uma conexão, você deve especificar um tipo de conexão. O tipo de conexão é um modelo que define um conjunto de propriedades. Você pode adicionar um tipo de conexão à automação do Azure usando um módulo de integração com um arquivo de metadados. Também é possível criar um tipo de conexão usando a [API de automação do Azure](/previous-versions/azure/reference/mt163818(v=azure.100)) se o módulo de integração inclui um tipo de conexão e é importado para sua conta de automação. 

>[!NOTE]
>Os ativos protegidos na Automação do Azure incluem credenciais, certificados, conexões e variáveis criptografadas. Esses ativos são criptografados e armazenados na automação do Azure usando uma chave exclusiva que é gerada para cada conta de automação. A automação do Azure armazena a chave no Key Vault gerenciado pelo sistema. Antes de armazenar um ativo seguro, a automação carrega a chave de Key Vault e a usa para criptografar o ativo. 

>[!NOTE]
>Este artigo foi atualizado para usar o novo módulo Az do Azure PowerShell. Você ainda pode usar o módulo AzureRM, que continuará a receber as correções de bugs até pelo menos dezembro de 2020. Para saber mais sobre o novo módulo Az e a compatibilidade com o AzureRM, confira [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para obter instruções de instalação do módulo Az no seu Hybrid Runbook Worker, confira [Instalar o módulo do Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para sua conta de Automação, você pode atualizar seus módulos para a versão mais recente usando [Como atualizar os módulos do Azure PowerShell na Automação do Azure](automation-update-azure-modules.md).

## <a name="connection-types"></a>Tipos de conexão

A automação do Azure torna os seguintes tipos de conexão internos disponíveis:

* `Azure`-Representa uma conexão usada para gerenciar recursos clássicos.
* `AzureServicePrincipal`-Representa uma conexão usada pela conta Executar como do Azure.
* `AzureClassicCertificate`-Representa uma conexão usada pela conta Executar como clássica do Azure.

Na maioria dos casos, você não precisa criar um recurso de conexão porque ele é criado quando você cria uma [conta Executar como](manage-runas-account.md).

## <a name="powershell-cmdlets-to-access-connections"></a>Cmdlets do PowerShell para acessar conexões

Os cmdlets na tabela a seguir criam e gerenciam conexões de automação com o PowerShell. Eles são fornecidos como parte dos [módulos AZ](shared-resources/modules.md#az-modules).

|Cmdlet|Descrição|
|---|---|
|[Get-AzAutomationConnection](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationconnection?view=azps-3.7.0)|Recupera informações sobre uma conexão.|
|[New-AzAutomationConnection](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationconnection?view=azps-3.7.0)|Cria uma nova conexão.|
|[Remove-AzAutomationConnection](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationConnection?view=azps-3.7.0)|Remove uma conexão existente.|
|[Set-AzAutomationConnectionFieldValue](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationConnectionFieldValue?view=azps-3.7.0)|Define o valor de determinado campo para uma conexão existente.|

## <a name="internal-cmdlets-to-access-connections"></a>Cmdlets internos para acessar conexões

O cmdlet interno na tabela a seguir é usado para acessar conexões em seus runbooks e configurações DSC. Esse cmdlet vem com o módulo `Orchestrator.AssetManagement.Cmdlets`global. Para obter mais informações, consulte [cmdlets internos](shared-resources/modules.md#internal-cmdlets).

|Cmdlet interno|Descrição|
|---|---|
|`Get-AutomationConnection` | Recupera os valores dos diferentes campos na conexão e os retorna como uma tabela de [hash](https://go.microsoft.com/fwlink/?LinkID=324844). Você pode usar essa tabela de hash com os comandos apropriados no runbook ou na configuração DSC.|

>[!NOTE]
>Evite usar variáveis com o `Name` parâmetro de `Get-AutomationConnection`. O uso de variáveis nesse caso pode complicar a descoberta de dependências entre runbooks ou configurações DSC e ativos de conexão em tempo de design.

## <a name="python-2-functions-to-access-connections"></a>Funções do Python 2 para acessar conexões

A função na tabela a seguir é usada para acessar conexões em um runbook Python 2.

| Função | Descrição |
|:---|:---|
| `automationassets.get_automation_connection` | Recupera uma conexão. Retorna um dicionário com as propriedades da conexão. |

> [!NOTE]
> Você deve importar o `automationassets` módulo na parte superior do seu runbook do Python para acessar as funções de ativo.

## <a name="create-a-new-connection"></a>Crie uma nova conexão

### <a name="create-a-new-connection-with-the-azure-portal"></a>Criar uma nova conexão com o portal do Azure

Para criar uma nova conexão no portal do Azure:

1. Em sua conta de automação, clique em **conexões** em **recursos compartilhados**.
2. Clique em **+ Adicionar uma conexão** na página conexões.
4. No campo **tipo** , no painel nova conexão, selecione o tipo de conexão a ser criado. Suas opções são `Azure`, `AzureServicePrincipal`e `AzureClassicCertificate`. 
5. O formulário apresenta propriedades para o tipo de conexão que você escolheu. Preencha o formulário e clique em **Criar** para salvar a nova conexão.

### <a name="create-a-new-connection-with-windows-powershell"></a>Criar uma nova conexão com o Windows PowerShell

Crie uma nova conexão com o Windows PowerShell usando `New-AzAutomationConnection` o cmdlet. Esse cmdlet tem um `ConnectionFieldValues` parâmetro que espera uma tabela de hash definindo valores para cada uma das propriedades definidas pelo tipo de conexão.

Você pode usar os seguintes comandos de exemplo como uma alternativa para criar a conta Executar como do portal para criar um novo ativo de conexão.

```powershell
$ConnectionAssetName = "AzureRunAsConnection"
$ConnectionFieldValues = @{"ApplicationId" = $Application.ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Cert.Thumbprint; "SubscriptionId" = $SubscriptionId}
New-AzAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -ConnectionTypeName AzureServicePrincipal -ConnectionFieldValues $ConnectionFieldValues
```

Quando você cria sua conta de automação, ela inclui vários módulos globais por padrão, juntamente com o tipo `AzureServicePrincipal` de conexão para `AzureRunAsConnection` criar o ativo de conexão. Se você tentar criar um novo ativo de conexão para se conectar a um serviço ou aplicativo com um método de autenticação diferente, a operação falhará porque o tipo de conexão ainda não está definido em sua conta de automação. Para obter mais informações sobre como criar seu próprio tipo de conexão para um módulo personalizado, consulte [Adicionar um tipo de conexão](#add-a-connection-type).

## <a name="add-a-connection-type"></a>Adicionar um tipo de conexão

Se o runbook ou a configuração DSC se conectar a um serviço externo, você deverá definir um tipo de conexão em um [módulo personalizado](shared-resources/modules.md#custom-modules) chamado módulo de integração. Esse módulo inclui um arquivo de metadados que especifica as propriedades do tipo de conexão e é denominado ** &lt;ModuleName&gt;-Automation. JSON**, localizado na pasta do módulo do arquivo **. zip** compactado. Esse arquivo contém os campos de uma conexão que são necessários para se conectar ao sistema ou serviço que o módulo representa. Usando esse arquivo, você pode definir os nomes de campo, os tipos de dados, o status de criptografia e o status opcional para o tipo de conexão. 

O exemplo a seguir é um modelo no formato de arquivo **. JSON** que define as propriedades de nome de usuário e senha para um `MyModuleConnection`tipo de conexão personalizado chamado:

```json
{
   "ConnectionFields": [
   {
      "IsEncrypted":  false,
      "IsOptional":  true,
      "Name":  "Username",
      "TypeName":  "System.String"
   },
   {
      "IsEncrypted":  true,
      "IsOptional":  false,
      "Name":  "Password",
      "TypeName":  "System.String"
   }
   ],
   "ConnectionTypeName":  "MyModuleConnection",
   "IntegrationModuleName":  "MyModule"
}
```

## <a name="get-a-connection-in-a-runbook-or-dsc-configuration"></a>Obter uma conexão em um runbook ou configuração DSC

Recupere uma conexão em um runbook ou configuração DSC com o cmdlet `Get-AutomationConnection` interno. Esse cmdlet é preferencial sobre o `Get-AzAutomationConnection` cmdlet, pois ele recupera os valores de conexão em vez de informações sobre a conexão. 

### <a name="textual-runbook-example"></a>Exemplo de runbook textual

O exemplo a seguir mostra como usar a conta Executar como para autenticar com Azure Resource Manager recursos em seu runbook. Ele usa um ativo de conexão que representa a conta Executar como, que faz referência à entidade de serviço baseada em certificado.

```powershell
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
```

### <a name="graphical-runbook-examples"></a>Exemplos de runbook gráfico

Você pode adicionar uma atividade para o cmdlet `Get-AutomationConnection` interno a um runbook gráfico. Clique com o botão direito do mouse na conexão no painel Biblioteca do editor gráfico e selecione **Adicionar à tela**.

![adicionar à tela](media/automation-connections/connection-add-canvas.png)

A imagem a seguir mostra um exemplo de como usar um objeto de conexão em um runbook gráfico. Este exemplo usa o `Constant value` conjunto de dados para `Get RunAs Connection` a atividade, que usa um objeto de conexão para autenticação. Um [link de pipeline](automation-graphical-authoring-intro.md#links-and-workflow) é usado aqui, `ServicePrincipalCertificate` pois o conjunto de parâmetros está esperando um único objeto.

![obter conexões](media/automation-connections/automation-get-connection-object.png)

### <a name="python-2-runbook-example"></a>Exemplo de runbook do Python 2

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

* Para saber mais sobre os cmdlets usados para acessar conexões, consulte [gerenciar módulos na automação do Azure](shared-resources/modules.md).
* Para obter informações gerais sobre runbooks, consulte [execução de runbook na automação do Azure](automation-runbook-execution.md).
* Para obter detalhes sobre as configurações DSC, consulte [visão geral da configuração de estado](automation-dsc-overview.md).