---
title: Usar o PowerShell para registrar e verificar Power BI (visualização)
description: Saiba como usar o PowerShell para registrar e verificar um locatário Power BI no Azure alcance.
author: darrenparker
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 10/12/2020
ms.openlocfilehash: f0b541baf49307006c18f07d92bd409763a29e3a
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/03/2020
ms.locfileid: "96551372"
---
# <a name="use-powershell-to-register-and-scan-power-bi-in-azure-purview-preview"></a>Usar o PowerShell para registrar e verificar Power BI no Azure alcance (versão prévia) 

Este artigo mostra como usar o PowerShell para configurar uma verificação de um locatário Power BI em um catálogo alcance do Azure.

## <a name="power-bi-authentication-background"></a>Plano de fundo de autenticação Power BI

O catálogo alcance deve se conectar à API de administração do Power BI para verificar artefatos em um locatário Power BI. O Power BI API de administração atualmente dá suporte a dois tipos de autenticação:

- Identidade gerenciada (MSI).
- Autenticação de usuário delegada.

> [!Note]
> O MSI é recomendado, a menos que a autenticação de usuário delegada seja necessária.

## <a name="create-a-security-group"></a>Adicionar um grupo de segurança

Cada catálogo alcance tem sua própria identidade gerenciada atribuída pelo sistema que deve receber acesso ao locatário Power BI para habilitar a verificação. O nome do catálogo pode ser usado para localizar sua identidade no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com), procure Azure Active Directory.
1. Crie um novo grupo de segurança em seu Azure Active Directory, seguindo [criar um grupo básico e adicionar membros usando Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal).

    > [!Tip]
    > Você pode ignorar esta etapa se já tiver um grupo de segurança a ser usado.

1. Certifique-se de selecionar segurança como o **tipo de grupo**.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/security-group.png" alt-text="Tipo de grupo de segurança":::

1. Adicione a identidade gerenciada do catálogo a esse grupo de segurança selecionando Membros e **+ adicionar membros**.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/add-group-member.png" alt-text="Adicionar a instância gerenciada do catálogo ao grupo":::

1. Pesquise seu catálogo e selecione-o.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/add-catalog-to-group-by-search.png" alt-text="Adicionar Catálogo pesquisando-o":::

1. Você deverá ver uma notificação de êxito mostrando que ela foi adicionada.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/success-add-catalog-msi.png" alt-text="Êxito ao adicionar catálogo MSI":::

## <a name="associate-the-security-group-with-power-bi"></a>Associar o grupo de segurança a Power BI

1. Faça logon no [portal de administração do Power bi](https://app.powerbi.com/admin-portal/tenantSettings?allowServicePrincipalsUseReadAdminAPIsUI=1). Anexar este sinalizador de recurso:  `allowServicePrincipalsUseReadAdminAPIsUI=1` . Esse sinalizador habilita o recurso que permite associar seu grupo de segurança. Por exemplo,

    ```http
    https://app.powerbi.com/admin-portal/tenantSettings?allowServicePrincipalsUseReadAdminAPIsUI=1`
    ```

    > [!Important]
    > Você precisa ser um administrador de Power BI para ver a página de configurações de locatário.

1. Selecione **configurações**  >  **do desenvolvedor permitir que as entidades de serviço usem APIs de Power bi somente leitura (versão prévia)**.
1. Selecione **grupos de segurança específicos**.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/allow-service-principals-power-bi-admin.png" alt-text="Imagem mostrando como permitir que as entidades de serviço obtenham somente leitura Power BI permissões de API de administração ":::

    > [!Caution]
    > Quando você permite que o grupo de segurança criado (que tem sua identidade gerenciada pelo catálogo de dados como um membro) Use somente leitura Power BI APIs de administração, você também permite que ele acesse os metadados (por exemplo, nomes de painel e relatório, proprietários, descrições, etc.) para todos os seus artefatos de Power BI nesse locatário. Depois que os metadados tiverem sido extraídos para as alcance do Azure, permissões do alcance, não Power BI permissões, determine quem pode ver os metadados.

    > [!Note]
    > Você pode remover o grupo de segurança das configurações do desenvolvedor, mas os metadados extraídos anteriormente não serão removidos da conta do alcance. Você pode excluí-lo separadamente.

## <a name="register-power-bi-and-set-up-a-scan"></a>Registrar Power BI e configurar uma verificação

Agora que você recebeu as permissões de catálogo para se conectar à API de administrador do seu locatário Power BI, precisará configurar sua verificação no catálogo. Para fazer isso, você configura e executa um script do PowerShell.

1. Baixe e extraia os cmdlets do PowerShell do ADC.
1. Configure seu script fornecendo valores para as atribuições na parte superior do script.

    ```PowerShell
    #Purview Account Info
    $azuretenantId = '<Tenant Id>'
    $azuresubscriptionId = '<Tenant Id>'
    $azureResourceGroupName = '<Resource Group Name>'
    $azurePurviewAccountName = '<Catalog Name>'
    $createCatalog = $false #Change to true if you need a new catalog to be created
    $azureCatalogLocation = 'East Us' #The region of your account
    $dataSourceName = 'pbi-msi-datasource01' #provide a unique data source name
    $dataScanName = 'scan01' #provide a unique scan name

    #PowerShell Command Module Path
    $ModulePath = '<Full path to where you extracted the PS zip files>\Microsoft.DataCatalog.Management.Commands.dll'
    Import-Module $ModulePath

    If ($createCatalog -eq $true)
    {
      Connect-AzAccount -Tenant $azuretenantId -SubscriptionId $azuresubscriptionId
      Set-AzDataCatalog -ResourceGroupName $azureResourceGroupName -Name $azurePurviewAccountName -Location $azureCatalogLocation
    }

    Set-AzDataCatalogSessionSettings -DataCatalogSession -UserAuthentication -TenantId $azuretenantId  -DataCatalogAccountName $azurePurviewAccountName

    Set-AzDataCatalogDataSource -Name $dataSourceName -AccountType PowerBI  -Tenant $azuretenantId

    Set-AzDataCatalogScan -DataSourceName $dataSourceName -ScanName $dataScanName -PowerBIManagedInstanceMsi
    ```

    > [!Note]
    > Você deve ser um colaborador ou proprietário na assinatura sob a qual você executa os comandos.

1. Inicie sua verificação executando o seguinte script:

    ```PowerShell
    Start-AzDataCatalogScan -DataSourceName $dataSourceName -Name $dataScanName -AsJob
    ```

## <a name="register-and-scan-power-bi"></a>Registrar e verificar Power BI

O método de autenticação recomendado é MSI. No entanto, para verificar um locatário Power BI que está em um locatário do Azure diferente de seu catálogo, use a autenticação delegada.

Para fazer a autenticação delegada, você deve ter credenciais de usuário de administrador, bem como Power BI credenciais de administrador. Você também deve criar um aplicativo do Azure e conceder a ele Power BI permissões locatário. ReadAll.

1. Navegue até [portal do Azure](https://portal.azure.com) e procure **registros de aplicativo**.

1. Em **registros de aplicativo**, selecione **+ novo registro**.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/new-app-registration.png" alt-text="Imagem mostrando como criar um novo registro de aplicativo do Azure":::

1. Insira um nome para o seu aplicativo.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/register-new-app.png" alt-text="registrar novo aplicativo":::

1. Depois que seu aplicativo for criado, selecione **permissões de API** e **+ adicione uma permissão**.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/app-permissions.png" alt-text="Imagem mostrando como adicionar permissão ao aplicativo":::

1. Selecione **serviço de Power bi** em **permissões de API de solicitação**.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/select-power-bi-service.png" alt-text="Imagem mostrando como selecionar o serviço PBI":::

1. Selecione **permissões delegadas** e **locatário. Read. All**. Em seguida, selecione **Adicionar permissões**.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/request-api-permissions.png" alt-text="Imagem mostrando como solicitar permissões de API":::

1. Selecione **Conceder consentimento do administrador**

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/grant-admin-perms.png" alt-text="Imagem mostrando como conceder consentimento de administrador":::

1. Copie a **ID do aplicativo (cliente)** e os valores da **ID do diretório (locatário)** .  Você usará esses valores ao configurar sua verificação.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/copy-client-and-tenantid.png" alt-text="Imagem mostrando a cópia das IDs de cliente e locatário":::

1. Configure sua verificação no PowerShell. O script solicitará as credenciais. Você deve ter pelo menos a função de colaborador e a função de administrador de fonte de dados alcance na assinatura do Azure que pretende usar.

    ```PowerShell
    # Purview Account Info
    $azuretenantId = '<Tenant Id>'
    $azuresubscriptionId = '<Tenant Id>'
    $azureResourceGroupName = '<Resource Group Name>'
    $azurePurviewAccountName = '<Catalog Name>'
    $createCatalog = $false
    $azureCatalogLocation = 'East Us' #The region of your account
    $dataSourceName = 'pbi-delegated-datasource01' #provide a unique data source name
    $dataScanName = 'scan01' #provide a unique scan name

    # Power BI Tenant Info
    $powerBITenantIdToScan = '<Power BI Tenant ID copied from above steps>'
    $ServicePrincipalId = '<Client ID copied from above steps>'
    $UserName = '<Power BI Admin emil ex: admin@firsttomarket.onmicrosoft.com>'
    $Password = '<Power BI Admin Password>'

    #PowerShell Command Module Path
    $ModulePath = '<Full path to where you extracted the PS zip files>\Microsoft.DataCatalog.Management.Commands.dll'
    Import-Module $ModulePath

    #Commands To Create catalog, Create DataSource, Create Datascan, Start Scan
    If($createCatalog -eq $true)
    {
      Connect-AzAccount -Tenant $azuretenantId -SubscriptionId $azuresubscriptionId
      Set-AzDataCatalog -ResourceGroupName $azureResourceGroupName -Name $azurePurviewAccountName -Location $azureCatalogLocation
    }

    Set-AzDataCatalogSessionSettings -DataCatalogSession -UserAuthentication -TenantId $azuretenantId   -DataCatalogAccountName $azurePurviewAccountName

    Set-AzDataCatalogDataSource -Name $dataSourceName -AccountType PowerBI -Tenant $powerBITenantIdToScan

    Set-AzDataCatalogScan -DataSourceName $dataSourceName -ScanName $dataScanName -PowerBIDelegated -ServicePrincipalId $ServicePrincipalId -UserName $UserName -Password $Password
    ```

1. Execute sua verificação.

      ```PowerShell
      Start-AzDataCatalogScan -DataSourceName $dataSourceName -Name $dataScanName -AsJob
      ```

## <a name="next-steps"></a>Próximas etapas

Para começar a usar o Azure alcance, consulte [início rápido: criar uma conta do Azure alcance](create-catalog-portal.md).
