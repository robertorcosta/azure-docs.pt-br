---
title: Implementar recuperação de desastres usando backup e restauração no Gerenciamento de API
titleSuffix: Azure API Management
description: Saiba como usar o backup e restauração para executar a recuperação de desastres no Gerenciamento de API no Azure.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 02/03/2020
ms.author: apimpm
ms.openlocfilehash: f9758678708338a284a35b45f7f9dd43b9a9017c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335949"
---
# <a name="how-to-implement-disaster-recovery-using-service-backup-and-restore-in-azure-api-management"></a>Como implementar a recuperação de desastre usando o backup de serviço e restaurar no Gerenciamento de API no Azure

Ao publicar e gerenciar suas APIs através do Gerenciamento de API do Azure, você está tirando proveito de muitos recursos de tolerância e infraestrutura que você precisaria criar, implementar e gerenciar manualmente. A plataforma Azure atenua grande parte das falhas potenciais a uma fração do custo.

Para se recuperar de problemas de disponibilidade que afetam a região que hospeda seu serviço de Gerenciamento de API, esteja pronto para reconstituir seu serviço em outra região a qualquer momento. Dependendo do seu objetivo de tempo de recuperação, você pode querer manter um serviço de espera em uma ou mais regiões. Você também pode tentar manter sua configuração e conteúdo em sincronia com o serviço ativo de acordo com seu objetivo de ponto de recuperação. Os recursos de backup e restauração de serviço fornecem os blocos de construção necessários para implementar a estratégia de recuperação de desastres.

As operações de backup e restauração também podem ser usadas para replicar a configuração do serviço de gerenciamento de API entre ambientes operacionais, por exemplo, desenvolvimento e estágio. Cuidado para que os dados em tempo de execução, como usuários e assinaturas, também sejam copiados, o que pode nem sempre ser desejável.

Este guia mostra como automatizar operações de backup e restauração e como garantir a autenticação bem-sucedida das solicitações de backup e restauração pelo Azure Resource Manager.

> [!IMPORTANT]
> A operação de restauração não altera a configuração personalizada do nome de host do serviço de destino. Recomendamos usar o mesmo nome de host personalizado e certificado TLS para serviços ativos e de espera, para que, após a conclusão da operação de restauração, o tráfego possa ser redirecionado para a instância de espera por uma simples alteração CNAME DNS.
>
> A operação de backup não captura dados de log pré-agregados usados em relatórios mostrados na lâmina Analytics no portal Azure.

> [!WARNING]
> Cada backup expira após 30 dias. Se você tentar restaurar um backup após o período de expiração de 30 dias, a restauração falhará com uma mensagem `Cannot restore: backup expired`.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="authenticating-azure-resource-manager-requests"></a>Autenticação de solicitações do gerenciador de recursos do Azure

> [!IMPORTANT]
> A API REST para backup e restauração usa o Gerenciador de Recursos do Azure e tem um mecanismo de autenticação diferentes das APIs REST para gerenciar suas entidades de Gerenciamento de API. As etapas desta seção descrevem como autenticar solicitações do Gerenciador de Recursos do Azure. Para mais informações, consulte [Autenticação de solicitações do Gerenciador de Recursos do Azure](/rest/api/index).

Todas as tarefas realizadas em recursos com o Azure Resource Manager precisam ser autenticadas com o Azure Active Directory usando as seguintes etapas:

-   Adicione um aplicativo no locatário do Active Directory do Azure.
-   Defina permissões para o aplicativo que você adicionou.
-   Obtenha o token para autenticar solicitações ao Gerenciador de Recursos do Azure.

### <a name="create-an-azure-active-directory-application"></a>Criar um aplicativo do Azure Active Directory

1. Faça login no [portal Azure](https://portal.azure.com).
2. Usando a assinatura que contém sua instância de serviço de gerenciamento de API, navegue até a guia **registros de Aplicativo** em **Azure Active Directory** (Azure Active Directory > registros de Aplicativo/Gerenciar).

    > [!NOTE]
    > Se o diretório padrão do Active Directory do Azure não é visível para sua conta, contate o administrador da assinatura do Azure para conceder as permissões necessárias para sua conta.

3. Clique em **Novo registro de aplicativo**.

    A janela **Criar** é exibida à direita. É nela em que você pode inserir as informações relevantes do aplicativo do AAD.

4. Insira um nome para o aplicativo.
5. Para o tipo de aplicativo, selecione **Nativo**.
6. Insira uma URL de espaço reservado como `http://resources` para o **URI de redirecionamento**, que é um campo obrigatório, mas o valor não é usado posteriormente. Clique na caixa de seleção para salvar o aplicativo.
7. Clique em **Criar**.

### <a name="add-an-application"></a>Adicionar um aplicativo

1. Uma vez que o aplicativo é criado, clique **em permissões de API**.
2. Clique em **+ Adicionar uma permissão**.
4. Pressione **Selecione as APIs da Microsoft**.
5. Escolha **o Gerenciamento de Serviços do Azure**.
6. Pressione **Selecionar**.

    ![Adicionar permissões](./media/api-management-howto-disaster-recovery-backup-restore/add-app.png)

7. Clique em **Permissões Delegadas** ao lado do aplicativo recém-adicionado e marque a caixa de **Acessar o Gerenciamento de Serviços do Azure (versão prévia)**.
8. Pressione **Selecionar**.
9. Clique em **Conceder Permissões**.

### <a name="configuring-your-app"></a>Configurando seu aplicativo

Antes de chamar as APIs que geram o backup e o restauram, é necessário obter um token. O exemplo a seguir usa o pacote NuGet [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) para recuperar o token.

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System;

namespace GetTokenResourceManagerRequests
{
    class Program
    {
        static void Main(string[] args)
        {
            var authenticationContext = new AuthenticationContext("https://login.microsoftonline.com/{tenant id}");
            var result = authenticationContext.AcquireTokenAsync("https://management.azure.com/", "{application id}", new Uri("{redirect uri}"), new PlatformParameters(PromptBehavior.Auto)).Result;

            if (result == null) {
                throw new InvalidOperationException("Failed to obtain the JWT token");
            }

            Console.WriteLine(result.AccessToken);

            Console.ReadLine();
        }
    }
}
```

Substitua `{tenant id}`, `{application id}` e `{redirect uri}` usando as seguintes instruções:

1. Substitua `{tenant id}` pela ID de locatário do aplicativo do Azure Active Directory criado. Você pode acessar o ID clicando **em registros** -> do aplicativo**Pontos finais**.

    ![Pontos de extremidade][api-management-endpoint]

2. Substitua `{application id}` pelo valor obtido navegando para a página **Configurações**.
3. Substitua a guia `{redirect uri}` com o valor das **URIs de Redirecionamento** pelo aplicativo do Azure Active Directory.

    Depois que os valores são especificados, o exemplo de código deve retornar um token semelhante ao seguinte exemplo:

    ![Token][api-management-arm-token]

    > [!NOTE]
    > O token pode expirar após um período determinado. Execute o código de exemplo novamente para gerar um novo token.

## <a name="calling-the-backup-and-restore-operations"></a>Chamando as operações de backup e restauração

As APIs REST são [Serviço de Gerenciamento de Api - Backup](/rest/api/apimanagement/2019-01-01/apimanagementservice/backup) e [Serviço de Gerenciamento de Api - Restauração](/rest/api/apimanagement/2019-01-01/apimanagementservice/restore).

Antes de chamar as operações de “backup e restauração” descritas nas seções a seguir, defina o cabeçalho de solicitação de autorização para a chamada REST.

```csharp
request.Headers.Add(HttpRequestHeader.Authorization, "Bearer " + token);
```

### <a name="back-up-an-api-management-service"></a><a name="step1"> </a>Fazer backup de um serviço de Gerenciamento de API

Para fazer backup de um serviço de Gerenciamento de API, execute a seguinte solicitação HTTP:

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/backup?api-version={api-version}
```

onde:

-   `subscriptionId` – ID da assinatura que contém o serviço de Gerenciamento de API do qual você está tentando fazer backup
-   `resourceGroupName` -nome do grupo de recursos do seu serviço de Gerenciamento de API do Azure
-   `serviceName` - o nome do serviço de Gerenciamento de API que você está fazendo um backup em específico, no momento de sua criação
-   `api-version`- substituir por`2018-06-01-preview`

No corpo da solicitação, especifique o nome da conta de armazenamento de destino do Azure, a chave de acesso, o nome do contêiner Blob e o nome de backup:

```json
{
    "storageAccount": "{storage account name for the backup}",
    "accessKey": "{access key for the account}",
    "containerName": "{backup container name}",
    "backupName": "{backup blob name}"
}
```

Defina o valor do cabeçalho de solicitação `Content-Type` para `application/json`.

O backup é uma operação longa de execução que pode levar mais de um minuto para concluir. Se a solicitação foi bem-sucedida e o processo de backup foi iniciado, você receberá uma `202 Accepted` resposta com um cabeçalho `Location`. Faça solicitações “GET” para a URL no cabeçalho do `Location` para descobrir o status da operação. Enquanto o backup está em andamento, você continua recebendo um código de status “202 Aceito”. Um Código de resposta `200 OK` indica a conclusão bem-sucedida da operação de backup.

Observe as seguintes restrições ao fazer uma solicitação de backup ou restauração:

-   O **contêiner** especificado no corpo solicitado **tem que existir**.
-   Enquanto o backup estiver em andamento, **evite alterações de gerenciamento no serviço,** como upgrade ou downgrade do SKU, alteração no nome do domínio e muito mais.
-   A restauração de um **backup é garantida somente por 30 dias** desde o momento de sua criação.
-   Os **Dados de uso** utilizados para a criação de relatórios de análise **não estão incluídos** no backup. Use o [API REST de Gerenciamento de API do Azure][azure api management rest api] para recuperar periodicamente os relatórios analíticos por questões de segurança.
-   Além disso, os seguintes itens não fazem parte dos dados de backup: certificados TLS/SSL de domínio personalizado e quaisquer certificados intermediários ou raiz carregados pelo cliente, conteúdo do portal do desenvolvedor e configurações de integração de rede virtual.
-   A frequência com que você executa backups de serviço afeta seu objetivo de ponto de recuperação. Para minimizar, aconselhamos a implementação de backups regulares e realizar backups sob demanda depois de fazer as mudanças ao seu serviço de Gerenciamento de API.
-   As **alterações** feitas na configuração do serviço (por exemplo, APIs, políticas, aparência do portal do desenvolvedor) enquanto uma operação de backup está em andamento **podem não ser incluídas no backup e, portanto, serão perdidas**.
-   **Permitir** o acesso do plano de controle à Conta de Armazenamento Do Azure, se ele tiver [o firewall][azure-storage-ip-firewall] ativado. O cliente deve abrir o conjunto de endereços IP do plano de controle de gerenciamento de API do [Azure][control-plane-ip-address] em sua conta de armazenamento para backup ou restauração de. 

> [!NOTE]
> Se você tentar fazer backup/restauração de/para um serviço de gerenciamento de API usando uma conta de armazenamento que tem [firewall][azure-storage-ip-firewall] ativado, na mesma região do Azure, isso não funcionará. Isso porque as solicitações ao Azure Storage não são snated para um IP público da Compute > (Azure Api Management control Plane). A solicitação de armazenamento cross region será snated.

### <a name="restore-an-api-management-service"></a><a name="step2"> </a>Restaurar um serviço de Gerenciamento de API

Para restaurar um serviço de Gerenciamento de API de um backup criado anteriormente, faça a seguinte solicitação HTTP:

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/restore?api-version={api-version}
```

onde:

-   `subscriptionId` - ID da assinatura que contém o serviço de Gerenciamento de API que você está restaurando um backup em
-   `resourceGroupName` – nome do grupo de recursos que contém o serviço de Gerenciamento de API do Azure em que você está restaurando um backup
-   `serviceName` - o nome do serviço de Gerenciamento de API para o qual está sendo realizada a restauração especificada no momento de sua criação
-   `api-version`- substituir por`2018-06-01-preview`

No corpo da solicitação, especifique o local do arquivo de backup. Ou seja, adicione o nome da conta de armazenamento do Azure, chave de acesso, nome do contêiner de blob e nome de backup:

```json
{
    "storageAccount": "{storage account name for the backup}",
    "accessKey": "{access key for the account}",
    "containerName": "{backup container name}",
    "backupName": "{backup blob name}"
}
```

Defina o valor do cabeçalho de solicitação `Content-Type` para `application/json`.

Restaure uma operação longa de execução que pode levar até 30 minutos ou mais para concluir. Se a solicitação foi bem-sucedida e o processo de restauração foi iniciado, você receberá uma `202 Accepted` resposta com um cabeçalho `Location`. Faça solicitações “GET” para a URL no cabeçalho do `Location` para descobrir o status da operação. Enquanto a restauração está em andamento, você continua recebendo o código de status “202 Aceito”. Um código de resposta `200 OK` indica a conclusão bem-sucedida da operação de restauração.

> [!IMPORTANT]
> **A SKU** do serviço que está sendo restaurada **precisa corresponder** à SKU do serviço copiado em backup que está sendo restaurada.
>
> As **alterações** feitas na configuração do serviço (por exemplo, APIs, políticas, aparência do portal do desenvolvedor) enquanto uma operação de restauração está em andamento **podem ser substituídas**.

<!-- Dummy comment added to suppress markdown lint warning -->

> [!NOTE]
> As operações de backup e restauração também podem ser executadas com os comandos PowerShell [_Backup-AzApiManagement_](/powershell/module/az.apimanagement/backup-azapimanagement) e [_Restore-AzApiManagement,_](/powershell/module/az.apimanagement/restore-azapimanagement) respectivamente.

## <a name="next-steps"></a>Próximas etapas

Confira os seguintes recursos para diferentes orientações do processo de backup/restauração.

-   [Replicar contas de Gerenciamento de API do Azure](https://www.returngis.net/en/2015/06/replicate-azure-api-management-accounts/)
-   [Automatizar backup e restauração do Gerenciamento de API com Aplicativos Lógicos](https://github.com/Azure/api-management-samples/tree/master/tutorials/automating-apim-backup-restore-with-logic-apps)
-   [Gerenciamento de API do Azure: Backup e Restauração de Configuração](https://blogs.msdn.com/b/stuartleeks/archive/2015/04/29/azure-api-management-backing-up-and-restoring-configuration.aspx)
    _A abordagem detalhada por Stuart não corresponde à orientação oficial, mas é interessante._

[backup an api management service]: #step1
[restore an api management service]: #step2
[azure api management rest api]: https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/api-management-rest
[api-management-add-aad-application]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-add-aad-application.png
[api-management-aad-permissions]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-permissions.png
[api-management-aad-permissions-add]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-permissions-add.png
[api-management-aad-delegated-permissions]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-delegated-permissions.png
[api-management-aad-default-directory]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-default-directory.png
[api-management-aad-resources]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-resources.png
[api-management-arm-token]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-arm-token.png
[api-management-endpoint]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-endpoint.png
[control-plane-ip-address]: api-management-using-with-vnet.md#control-plane-ips
[azure-storage-ip-firewall]: ../storage/common/storage-network-security.md#grant-access-from-an-internet-ip-range
