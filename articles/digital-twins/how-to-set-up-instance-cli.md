---
title: Configurar uma instância e uma autenticação (CLI)
titleSuffix: Azure Digital Twins
description: Veja como configurar uma instância do serviço gêmeos do Azure digital usando a CLI
author: baanders
ms.author: baanders
ms.date: 7/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 3c7e4887610f30113b81421396500416d04c5e5e
ms.sourcegitcommit: 2ffa5bae1545c660d6f3b62f31c4efa69c1e957f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/11/2020
ms.locfileid: "88078471"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-cli"></a>Configurar uma instância e autenticação do gêmeos digital do Azure (CLI)

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

Este artigo aborda as etapas para **Configurar uma nova instância de gêmeos digital do Azure**, incluindo a criação da instância e a configuração da autenticação. Depois de concluir este artigo, você terá uma instância do gêmeos digital do Azure pronta para começar a programar.

Esta versão deste artigo percorre essas etapas manualmente, uma a uma, usando a CLI.
* Para percorrer essas etapas manualmente usando o portal do Azure, consulte a versão do portal deste artigo: [*como configurar uma instância e autenticação (Portal)*](how-to-set-up-instance-portal.md).
* Para executar uma configuração automatizada usando um exemplo de script de implantação, consulte a versão com script deste artigo: [*como: configurar uma instância e autenticação (script)*](how-to-set-up-instance-scripted.md).

[!INCLUDE [digital-twins-setup-steps-prereq.md](../../includes/digital-twins-setup-steps-prereq.md)]
[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="set-up-cloud-shell-session"></a>Configurar uma sessão do Cloud Shell
[!INCLUDE [Cloud Shell for Azure Digital Twins](../../includes/digital-twins-cloud-shell.md)]

## <a name="create-the-azure-digital-twins-instance"></a>Criar a instância de gêmeos digital do Azure

Nesta seção, você **criará uma nova instância do Azure digital gêmeos** usando o comando Cloud Shell. Você precisará fornecer:
* Um grupo de recursos no qual implantá-lo. Se você ainda não tiver um grupo de recursos existente em mente, poderá criar um agora com este comando:
    ```azurecli
    az group create --location <region> --name <name-for-your-resource-group>
    ```
* Uma região para a implantação. Para ver quais regiões dão suporte ao Azure digital gêmeos, visite [*produtos do Azure disponíveis por região*](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins).
* Um nome para sua instância. O nome da nova instância deve ser exclusivo na região da sua assinatura (ou seja, se sua assinatura tiver outra instância de gêmeos digital do Azure na região que já está usando o nome que você escolher, será solicitado que você escolha um nome diferente).

Use esses valores no comando a seguir para criar a instância:

```azurecli
az dt create --dt-name <name-for-your-Azure-Digital-Twins-instance> -g <your-resource-group> -l <region>
```

### <a name="verify-success-and-collect-important-values"></a>Verificar o êxito e coletar valores importantes

Se a instância foi criada com êxito, o resultado em Cloud Shell será semelhante a este, gerando informações sobre o recurso que você criou:

:::image type="content" source="media/how-to-set-up-instance/cloud-shell/create-instance.png" alt-text="janela Comando com a criação bem-sucedida do grupo de recursos e a instância do gêmeos digital do Azure":::

Anote o nome de *host*da instância do Azure digital gêmeos, *Name*e *resourcegroup* da saída. Esses são todos os valores importantes que podem ser necessários à medida que você continuar trabalhando com sua instância do gêmeos digital do Azure para configurar a autenticação e os recursos do Azure relacionados. Se outros usuários estiverem programando em relação à instância, você deverá compartilhar esses valores com eles.

> [!TIP]
> Você pode ver essas propriedades, juntamente com todas as propriedades de sua instância, a qualquer momento executando `az dt show --dt-name <your-Azure-Digital-Twins-instance>` .

Agora você tem uma instância de gêmeos digital do Azure pronta para uso. Em seguida, você dará as permissões apropriadas de usuário do Azure para gerenciá-lo.

## <a name="set-up-user-access-permissions"></a>Configurar permissões de acesso do usuário

[!INCLUDE [digital-twins-setup-role-assignment.md](../../includes/digital-twins-setup-role-assignment.md)]

Use o comando a seguir para atribuir a função (deve ser executada por um usuário com [permissões suficientes](#prerequisites-permission-requirements) na assinatura do Azure). O comando exige que você passe o *nome UPN* na conta do Azure ad para o usuário que deve receber a função. Na maioria dos casos, isso corresponderá ao email do usuário na conta do Azure AD.

```azurecli
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<Azure-AD-user-principal-name-of-user-to-assign>" --role "Azure Digital Twins Owner (Preview)"
```

O resultado desse comando são informações de saída sobre a atribuição de função que foi criada.

> [!NOTE]
> Se esse comando retornar um erro dizendo que a CLI **não pode localizar a entidade de serviço ou usuário no banco de dados de grafo**:
>
> Em vez disso, atribua a função usando a *ID de objeto* do usuário. Isso pode acontecer para usuários em contas pessoais da [Microsoft (MSAS)](https://account.microsoft.com/account). 
>
> Use a [página portal do Azure de Azure Active Directory usuários](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers) para selecionar a conta de usuário e abrir seus detalhes. Copie o *ObjectID*do usuário:
>
> :::image type="content" source="media/includes/user-id.png" alt-text="Exibição da página do usuário no portal do Azure realçando o GUID no campo ' ID do objeto '" lightbox="media/includes/user-id.png":::
>
> Em seguida, repita o comando da lista de atribuição de função usando a *ID de objeto* do usuário para o `assignee` parâmetro acima.

### <a name="verify-success"></a>Verificar êxito

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

Agora você tem uma instância de gêmeos digital do Azure pronta para uso e atribuiu permissões para gerenciá-la. Em seguida, você configurará permissões para um aplicativo cliente acessá-lo.

## <a name="set-up-access-permissions-for-client-applications"></a>Configurar permissões de acesso para aplicativos cliente

[!INCLUDE [digital-twins-setup-app-registration.md](../../includes/digital-twins-setup-app-registration.md)]

Para criar um registro de aplicativo, você precisa fornecer as IDs de recurso para as APIs do gêmeos digital do Azure e as permissões de linha de base para a API.

Em seu diretório de trabalho, crie um novo arquivo e insira o seguinte trecho de código JSON para configurar esses detalhes: 

```json
[{
    "resourceAppId": "0b07f429-9f4b-4714-9392-cc5e8e80c8b0",
    "resourceAccess": [
     {
       "id": "4589bd03-58cb-4e6c-b17f-b580e39652f8",
       "type": "Scope"
     }
    ]
}]
``` 

Salve este arquivo como _**manifest.jsem**_.

> [!NOTE] 
> Há alguns lugares em que uma cadeia de caracteres "amigável", legível `https://digitaltwins.azure.net` por humanos, pode ser usada para a ID do aplicativo de recurso gêmeos digital do Azure em vez do GUID `0b07f429-9f4b-4714-9392-cc5e8e80c8b0` . Por exemplo, muitos exemplos em todo esse conjunto de documentação usam a autenticação com a biblioteca MSAL, e a cadeia de caracteres amigável pode ser usada para isso. No entanto, durante essa etapa da criação do registro do aplicativo, a forma de GUID da ID é necessária, como mostrado acima. 

Em seguida, você carregará esse arquivo para Cloud Shell. Na janela Cloud Shell, clique no ícone "carregar/baixar arquivos" e escolha "carregar".

:::image type="content" source="media/how-to-set-up-instance/cloud-shell/cloud-shell-upload.png" alt-text="Janela Cloud Shell mostrando a seleção da opção carregar":::
Navegue até o *manifest.jsem* que você acabou de criar e pressione "abrir".

Em seguida, execute o seguinte comando para criar um registro de aplicativo (substituindo espaços reservados, conforme necessário):

```azurecli
az ad app create --display-name <name-for-your-app-registration> --native-app --required-resource-accesses manifest.json --reply-url http://localhost
```

Aqui está um trecho da saída deste comando, mostrando informações sobre o registro que você criou:

:::image type="content" source="media/how-to-set-up-instance/cloud-shell/new-app-registration.png" alt-text="Cloud Shell a saída do novo registro de aplicativo do Azure AD":::

### <a name="verify-success"></a>Verificar êxito

[!INCLUDE [digital-twins-setup-verify-app-registration-1.md](../../includes/digital-twins-setup-verify-app-registration-1.md)]

Primeiro, verifique se as configurações de seu *manifest.jscarregado em* foram definidas corretamente no registro. Para fazer isso, selecione *manifesto* na barra de menus para exibir o código do manifesto do registro do aplicativo. Role até a parte inferior da janela de código e procure os campos de sua *manifest.jsem* `requiredResourceAccess` :

[!INCLUDE [digital-twins-setup-verify-app-registration-2.md](../../includes/digital-twins-setup-verify-app-registration-2.md)]

### <a name="collect-important-values"></a>Coletar valores importantes

Em seguida, selecione *visão geral* na barra de menus para ver os detalhes do registro do aplicativo:

:::image type="content" source="media/how-to-set-up-instance/portal/app-important-values.png" alt-text="Exibição do portal dos valores importantes para o registro do aplicativo":::

Anote a ID do *aplicativo (cliente)* e a *ID do diretório (locatário)* mostradas **na página.** Esses valores serão necessários posteriormente para [autenticar um aplicativo cliente em relação às APIs do gêmeos digital do Azure](how-to-authenticate-client.md). Se você não for a pessoa que vai escrever código para tais aplicativos, precisará compartilhar esses valores com a pessoa que será.

### <a name="other-possible-steps-for-your-organization"></a>Outras etapas possíveis para sua organização

[!INCLUDE [digital-twins-setup-additional-requirements.md](../../includes/digital-twins-setup-additional-requirements.md)]

## <a name="next-steps"></a>Próximas etapas

Veja como conectar seu aplicativo cliente à sua instância escrevendo o código de autenticação do aplicativo cliente:
* [*Como: escrever código de autenticação do aplicativo*](how-to-authenticate-client.md)