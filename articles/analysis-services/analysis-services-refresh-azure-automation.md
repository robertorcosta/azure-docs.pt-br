---
title: Atualize os modelos de serviços de análise do Azure com a Automação Azure | Microsoft Docs
description: Este artigo descreve como codificar atualizações de modelos para os Serviços de Análise do Azure usando o Azure Automation.
author: chrislound
ms.service: analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: chlound
ms.openlocfilehash: a79123d57f80474e1871ef68f9a92ea9417089ac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73572362"
---
# <a name="refresh-with-azure-automation"></a>Atualizar com Automação do Azure

Usando o Azure Automation e o PowerShell Runbooks, você pode executar operações automatizadas de atualização de dados em seus modelos tabulares de Análise do Azure.  

O exemplo neste artigo usa os [módulos PowerShell SqlServer](https://docs.microsoft.com/powershell/module/sqlserver/?view=sqlserver-ps).

Uma amostra do PowerShell Runbook, que demonstra a atualização de um modelo, é fornecida mais tarde neste artigo.  

## <a name="authentication"></a>Autenticação

Todas as chamadas devem ser autenticadas com um token válido do Azure Active Directory (OAuth 2).  O exemplo neste artigo usará um SPN (Service Principal) para autenticar aos Serviços de Análise do Azure.

Para saber mais sobre a criação de um Service Principal, consulte [Criar um diretor de serviço usando o portal Azure](../active-directory/develop/howto-create-service-principal-portal.md).

## <a name="prerequisites"></a>Pré-requisitos

> [!IMPORTANT]
> O exemplo a seguir assume que o firewall dos Serviços de Análise do Azure está desativado. Se o firewall estiver ativado, o endereço IP público do iniciador de solicitação precisará ser listado no firewall.

### <a name="install-sqlserver-modules-from-powershell-gallery"></a>Instale módulos SqlServer na galeria PowerShell.

1. Em sua conta de automação do Azure, clique em **módulos**e, em seguida, **navegue na galeria**.

2. Na barra de pesquisa, procure por **SqlServer**.

    ![Módulos de pesquisa](./media/analysis-services-refresh-azure-automation/1.png)

3. Selecione SqlServer e clique **em Importar**.
 
    ![Módulo de Importação](./media/analysis-services-refresh-azure-automation/2.png)

4. Clique em **OK**.
 
### <a name="create-a-service-principal-spn"></a>Criar um SpN (Service Principal)

Para saber mais sobre a criação de um Service Principal, consulte [Criar um diretor de serviço usando o portal Azure](../active-directory/develop/howto-create-service-principal-portal.md).

### <a name="configure-permissions-in-azure-analysis-services"></a>Configure permissões nos Serviços de Análise do Azure
 
O Princípio do Serviço criado deve ter permissões de administrador de servidor no servidor. Para saber mais, consulte [Adicionar uma entidade de serviço à função de administrador do servidor](analysis-services-addservprinc-admins.md).

## <a name="design-the-azure-automation-runbook"></a>Projete o Runbook de Automação do Azure

1. Na Conta de Automação, crie um recurso **de Credenciais** que será usado para armazenar com segurança o Service Principal.

    ![Criar credencial](./media/analysis-services-refresh-azure-automation/6.png)

2. Digite os detalhes da credencial.  Para o **nome de usuário,** digite o **SPN ClientId**, for the **Password**, digite o **SPN Secret**.

    ![Criar credencial](./media/analysis-services-refresh-azure-automation/7.png)

3. Importar o Runbook de Automação

    ![Importar Runbook](./media/analysis-services-refresh-azure-automation/8.png)

4. Procure o arquivo **Refresh-Model.ps1,** forneça **um nome** e **uma descrição**e clique em **Criar**.

    ![Importar Runbook](./media/analysis-services-refresh-azure-automation/9.png)

5. Quando o Runbook for criado, ele entrará automaticamente no modo de edição.  Selecione **Publicar**.

    ![Publicar runbook](./media/analysis-services-refresh-azure-automation/10.png)

    > [!NOTE]
    > O recurso de credencial criado anteriormente é recuperado pelo manual usando o comando **Get-AutomationPSCredential.**  Esse comando é então passado para o comando **Invoke-ProcessASADatabase** PowerShell para executar a autenticação nos Serviços de Análise do Azure.

6. Teste o manual clicando em **Iniciar**.

    ![Inicie o Runbook](./media/analysis-services-refresh-azure-automation/11.png)

7. Preencha os parâmetros **DATABASENAME,** **ANALYSISSERVER**e **REFRESHTYPE** e clique em **OK**. O parâmetro **WEBHOOKDATA** não é necessário quando o Runbook é executado manualmente.

    ![Inicie o Runbook](./media/analysis-services-refresh-azure-automation/12.png)

Se o Runbook for executado com sucesso, você receberá uma saída como a seguinte:

![Corrida bem sucedida](./media/analysis-services-refresh-azure-automation/13.png)

## <a name="use-a-self-contained-azure-automation-runbook"></a>Use um runbook de automação azure independente

O Runbook pode ser configurado para ativar a atualização do modelo Azure Analysis Services em uma base programada.

Isso pode ser configurado da seguinte forma:

1. No Manual de Automação, clique em **Agendar**e, em seguida, **adicione um cronograma**.
 
    ![Criar cronograma](./media/analysis-services-refresh-azure-automation/14.png)

2. Clique **em Agendamento** > **Crie um novo cronograma**e preencha os detalhes.

    ![Configurar cronograma](./media/analysis-services-refresh-azure-automation/15.png)

3. Clique em **Criar**.

4. Preencha os parâmetros do cronograma. Estes serão usados cada vez que o Runbook for acionado. O parâmetro **WEBHOOKDATA** deve ser deixado em branco ao ser executado por um cronograma.

    ![Configurar parâmetros](./media/analysis-services-refresh-azure-automation/16.png)

5. Clique em **OK**.

## <a name="consume-with-data-factory"></a>Consumir com fábrica de dados

Para consumir o runbook usando o Azure Data Factory, primeiro crie um **Webhook** para o runbook. O **Webhook** fornecerá uma URL que pode ser chamada através de uma atividade web da Fábrica de Dados Do Azure.

> [!IMPORTANT]
> Para criar um **Webhook,** o status do Runbook deve ser **publicado**.

1. No manual de automação, clique **em Webhooks**e clique em **Adicionar webhook**.

   ![Adicionar webhook](./media/analysis-services-refresh-azure-automation/17.png)

2. Dê ao Webhook um nome e uma expiração.  O nome só identifica o Webhook dentro do Manual de Automação, ele não faz parte da URL.

   >[!CAUTION]
   >Certifique-se de copiar a URL antes de fechar o assistente, pois não pode recuperá-la uma vez fechada.
    
   ![Configurar webhook](./media/analysis-services-refresh-azure-automation/18.png)

    Os parâmetros para o webhook podem permanecer em branco.  Ao configurar a atividade web da Fábrica de Dados do Azure, os parâmetros podem ser passados para o corpo da chamada web.

3. Na Fábrica de Dados, configure uma **atividade web**

### <a name="example"></a>Exemplo

   ![Exemplo de atividade web](./media/analysis-services-refresh-azure-automation/19.png)

A **URL** é a URL criada a partir do Webhook.

O **corpo** é um documento JSON que deve conter as seguintes propriedades:


|Propriedade  |Valor  |
|---------|---------|
|**AnalysisServicesDatabase**     |O nome do banco de dados dos Serviços de Análise do Azure <br/> Exemplo: AdventureWorksDB         |
|**AnalysisServicesServer**     |O nome do servidor do Azure Analysis Services. <br/> Exemplo: https:\//westus.asazure.windows.net/servers/myserver/models/AdventureWorks/         |
|**DatabaseRefreshType**     |O tipo de atualização para executar. <br/> Exemplo: Completo         |

Exemplo de corpo JSON:

```json
{
    "AnalysisServicesDatabaseName": "AdventureWorksDB",
    "AnalysisServicesServer": "asazure://westeurope.asazure.windows.net/MyAnalysisServer",
    "DatabaseRefreshType": "Full"
}
```

Esses parâmetros são definidos no script PowerShell do runbook.  Quando a atividade da Web é executada, a carga útil JSON passada é WEBHOOKDATA.

Isso é desserializado e armazenado como parâmetros do PowerShell, que são então usados pelo comando Invoke-ProcesASDatabase PowerShell.

![Webhook desserializado](./media/analysis-services-refresh-azure-automation/20.png)

## <a name="use-a-hybrid-worker-with-azure-analysis-services"></a>Use um trabalhador híbrido com serviços de análise do Azure

Uma máquina virtual do Azure com um endereço IP público estático pode ser usada como um Trabalhador Híbrido de Automação Azure.  Este endereço IP público pode então ser adicionado ao firewall dos Serviços de Análise do Azure.

> [!IMPORTANT]
> Certifique-se de que o endereço IP público da Máquina Virtual esteja configurado como estático.
>
>Para saber mais sobre a configuração do Azure Automation Hybrid Workers, consulte [Automatizar recursos em seu data center ou nuvem usando o Hybrid Runbook Worker](../automation/automation-hybrid-runbook-worker.md#install-a-hybrid-runbook-worker).

Uma vez configurado um Trabalhador Híbrido, crie um Webhook conforme descrito na seção [Consumir com Fábrica de Dados](#consume-with-data-factory).  A única diferença aqui é selecionar a opção **Executar no** > **Hybrid Worker** ao configurar o Webhook.

Exemplo webhook usando Hybrid Worker:

![Exemplo de Webhook de trabalhador híbrido](./media/analysis-services-refresh-azure-automation/21.png)

## <a name="sample-powershell-runbook"></a>Runbook de amostras powershell

O seguinte trecho de código é um exemplo de como executar a atualização do modelo Azure Analysis Services usando um PowerShell Runbook.

```powershell
param
(
    [Parameter (Mandatory = $false)]
    [object] $WebhookData,

    [Parameter (Mandatory = $false)]
    [String] $DatabaseName,
    [Parameter (Mandatory = $false)]
    [String] $AnalysisServer,
    [Parameter (Mandatory = $false)]
    [String] $RefreshType
)

$_Credential = Get-AutomationPSCredential -Name "ServicePrincipal"

# If runbook was called from Webhook, WebhookData will not be null.
if ($WebhookData)
{ 
    # Retrieve AAS details from Webhook request body
    $atmParameters = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)
    Write-Output "CredentialName: $($atmParameters.CredentialName)"
    Write-Output "AnalysisServicesDatabaseName: $($atmParameters.AnalysisServicesDatabaseName)"
    Write-Output "AnalysisServicesServer: $($atmParameters.AnalysisServicesServer)"
    Write-Output "DatabaseRefreshType: $($atmParameters.DatabaseRefreshType)"
    
    $_databaseName = $atmParameters.AnalysisServicesDatabaseName
    $_analysisServer = $atmParameters.AnalysisServicesServer
    $_refreshType = $atmParameters.DatabaseRefreshType
 
    Invoke-ProcessASDatabase -DatabaseName $_databaseName -RefreshType $_refreshType -Server $_analysisServer -ServicePrincipal -Credential $_credential
}
else 
{
    Invoke-ProcessASDatabase -DatabaseName $DatabaseName -RefreshType $RefreshType -Server $AnalysisServer -ServicePrincipal -Credential $_Credential
}
```


## <a name="next-steps"></a>Próximas etapas

[Amostras](analysis-services-samples.md)  
[REST API](https://docs.microsoft.com/rest/api/analysisservices/servers)
