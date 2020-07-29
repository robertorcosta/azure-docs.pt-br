---
title: Configurar uma instância e uma autenticação (manual)
titleSuffix: Azure Digital Twins
description: Consulte como configurar uma instância do serviço gêmeos do Azure digital, incluindo a autenticação adequada. Versão manual.
author: baanders
ms.author: baanders
ms.date: 7/22/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: d2d5ce0bc988badc6f25726206a953d87de7eaa2
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87371441"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-manual"></a>Configurar uma instância e autenticação do gêmeos digital do Azure (manual)

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

Este artigo aborda as etapas para **Configurar uma nova instância de gêmeos digital do Azure**, incluindo a criação da instância e a configuração da autenticação. Depois de concluir este artigo, você terá uma instância do gêmeos digital do Azure pronta para começar a programar.

Esta versão deste artigo percorre essas etapas manualmente, uma a uma. Para executar uma configuração automatizada usando um exemplo de script de implantação, consulte a versão com script deste artigo: [*como: configurar uma instância e autenticação (script)*](how-to-set-up-instance-scripted.md).

[!INCLUDE [digital-twins-setup-starter.md](../../includes/digital-twins-setup-starter.md)]

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

### <a name="verify-success"></a>Verificar êxito

Se a instância foi criada com êxito, o resultado em Cloud Shell será semelhante a este, gerando informações sobre o recurso que você criou:

:::image type="content" source="media/how-to-set-up-instance/create-instance.png" alt-text="janela Comando com a criação bem-sucedida do grupo de recursos e a instância do gêmeos digital do Azure":::

Anote o nome de *host*da instância do Azure digital gêmeos, *Name*e *resourcegroup* da saída. Esses são todos os valores importantes que podem ser necessários à medida que você continuar trabalhando com sua instância do gêmeos digital do Azure para configurar a autenticação e os recursos do Azure relacionados.

> [!TIP]
> Você pode ver essas propriedades, juntamente com todas as propriedades de sua instância, a qualquer momento executando `az dt show --dt-name <your-Azure-Digital-Twins-instance>` .

Agora você tem uma instância de gêmeos digital do Azure pronta para uso. Em seguida, você dará as permissões apropriadas de usuário do Azure para gerenciá-lo.

## <a name="set-up-your-users-access-permissions"></a>Configurar as permissões de acesso do usuário

O Azure digital gêmeos usa o [Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) para o RBAC (controle de acesso baseado em função). Isso significa que, antes que um usuário possa fazer chamadas de plano de dados para sua instância do gêmeos digital do Azure, esse usuário deve primeiro receber uma função com permissões para fazer isso.

Para o Azure digital gêmeos, essa função é o _**proprietário do gêmeos digital do Azure (versão prévia)**_. Você pode ler mais sobre funções e segurança em [*conceitos: segurança para soluções de gêmeos digitais do Azure*](concepts-security.md).

Esta seção mostrará como criar uma atribuição de função para um usuário na instância do gêmeos digital do Azure, por meio de seu email associado ao locatário do Azure AD em sua assinatura do Azure. Dependendo de sua função e de suas permissões em sua assinatura do Azure, você configurará isso para você mesmo ou a definirá em nome de outra pessoa que gerenciará a instância de gêmeos digital do Azure.

### <a name="assign-the-role"></a>Atribuir a função

Para conceder a um usuário permissões para gerenciar uma instância do gêmeos digital do Azure, você deve atribuir a função de _**proprietário do gêmeos digital do Azure (versão prévia)**_ na instância. 

> [!NOTE]
> Observe que essa função é diferente da função de *proprietário* do Azure AD, que também pode ser atribuída no escopo da instância de gêmeos digital do Azure. Essas são duas funções de gerenciamento distintas, e o *proprietário* do Azure ad não concede acesso aos recursos do plano de dados que são concedidos com o *proprietário do Azure digital gêmeos (versão prévia)*.

Use o comando a seguir para atribuir a função (deve ser executada por um proprietário da assinatura do Azure):

```azurecli
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<Azure-AD-email-of-user-to-assign>" --role "Azure Digital Twins Owner (Preview)"
```

O resultado desse comando são informações de saída sobre a atribuição de função que foi criada.

> [!TIP]
> Se você receber um erro *400: BadRequest* , execute o seguinte comando para obter o *ObjectID* para o usuário:
> ```azurecli
> az ad user show --id <Azure-AD-email-of-user-to-assign> --query objectId
> ```
> Em seguida, repita o comando de atribuição de função usando a *ID de objeto* do usuário no lugar de seu email.

### <a name="verify-success"></a>Verificar êxito

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

Agora você tem uma instância de gêmeos digital do Azure pronta para uso e atribuiu permissões para gerenciá-la. Em seguida, você configurará permissões para um aplicativo cliente acessá-lo.

## <a name="set-up-access-permissions-for-client-applications"></a>Configurar permissões de acesso para aplicativos cliente

Depois de configurar uma instância do gêmeos digital do Azure, é comum interagir com essa instância por meio de um aplicativo cliente que você criar. Para fazer isso, você precisará certificar-se de que o aplicativo cliente será capaz de se autenticar no gêmeos digital do Azure. Isso é feito configurando um **registro de aplicativo** [Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) para o aplicativo cliente usar.

Esse registro de aplicativo é onde você configura permissões de acesso para as [APIs do Azure digital gêmeos](how-to-use-apis-sdks.md). Posteriormente, o aplicativo cliente será autenticado no registro do aplicativo e, como resultado, receberá as permissões de acesso configuradas para as APIs.

>[!TIP]
> Como proprietário da assinatura, você pode preferir configurar um novo registro de aplicativo para cada nova instância de gêmeos digital do Azure *ou* para fazer isso apenas uma vez e estabelecer um único registro de aplicativo que será compartilhado entre todas as instâncias de gêmeos digitais do Azure na assinatura.

### <a name="create-the-registration"></a>Criar o registro

Para criar um registro de aplicativo, você precisa fornecer as IDs de recurso para as APIs do gêmeos digital do Azure e as permissões de linha de base para a API. No diretório de trabalho, abra um novo arquivo e insira o seguinte trecho de JSON para configurar esses detalhes: 

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

Salve este arquivo como *manifest.jsem*.

> [!NOTE] 
> Há alguns lugares em que uma cadeia de caracteres "amigável", legível `https://digitaltwins.azure.net` por humanos, pode ser usada para a ID do aplicativo de recurso gêmeos digital do Azure em vez do GUID `0b07f429-9f4b-4714-9392-cc5e8e80c8b0` . Por exemplo, muitos exemplos em todo esse conjunto de documentação usam a autenticação com a biblioteca MSAL, e a cadeia de caracteres amigável pode ser usada para isso. No entanto, durante essa etapa da criação do registro do aplicativo, a forma de GUID da ID é necessária, como mostrado acima. 

Na janela Cloud Shell, clique no ícone "carregar/baixar arquivos" e escolha "carregar".

:::image type="content" source="media/how-to-set-up-instance/cloud-shell-upload.png" alt-text="Janela Cloud Shell mostrando a seleção da opção carregar":::
Navegue até o *manifest.jsem* que você acabou de criar e pressione "abrir".

Em seguida, execute o seguinte comando para criar um registro de aplicativo (substituindo espaços reservados, conforme necessário):

```azurecli
az ad app create --display-name <name-for-your-app> --native-app --required-resource-accesses manifest.json --reply-url http://localhost
```

Aqui está um trecho da saída deste comando, mostrando informações sobre o registro que você criou:

:::image type="content" source="media/how-to-set-up-instance/new-app-registration.png" alt-text="Cloud Shell a saída do novo registro de aplicativo do Azure AD":::

### <a name="verify-success"></a>Verificar êxito

[!INCLUDE [digital-twins-setup-verify-app-registration-1.md](../../includes/digital-twins-setup-verify-app-registration-1.md)]

Primeiro, verifique se as configurações de seu *manifest.jscarregado em* foram definidas corretamente no registro. Para fazer isso, selecione *manifesto* na barra de menus para exibir o código do manifesto do registro do aplicativo. Role até a parte inferior da janela de código e procure os campos de sua *manifest.jsem* `requiredResourceAccess` :

[!INCLUDE [digital-twins-setup-verify-app-registration-2.md](../../includes/digital-twins-setup-verify-app-registration-2.md)]

### <a name="collect-important-values"></a>Coletar valores importantes

Em seguida, selecione *visão geral* na barra de menus para ver os detalhes do registro do aplicativo:

:::image type="content" source="media/how-to-set-up-instance/app-important-values.png" alt-text="Exibição do portal dos valores importantes para o registro do aplicativo":::

Anote a ID do *aplicativo (cliente)* e a *ID do diretório (locatário)* mostradas **na página.** Esses valores serão necessários posteriormente para [autenticar um aplicativo cliente em relação às APIs do gêmeos digital do Azure](how-to-authenticate-client.md). Se você não for a pessoa que vai escrever código para tais aplicativos, precisará compartilhar esses valores com a pessoa que será.

### <a name="other-possible-steps-for-your-organization"></a>Outras etapas possíveis para sua organização

[!INCLUDE [digital-twins-setup-additional-requirements.md](../../includes/digital-twins-setup-additional-requirements.md)]

## <a name="next-steps"></a>Próximas etapas

Veja como conectar seu aplicativo cliente à sua instância escrevendo o código de autenticação do aplicativo cliente:
* [*Como: escrever código de autenticação do aplicativo*](how-to-authenticate-client.md)
