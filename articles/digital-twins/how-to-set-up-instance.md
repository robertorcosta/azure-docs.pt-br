---
title: Criar uma instância dos Gêmeos Digitais do Azure
titleSuffix: Azure Digital Twins
description: Consulte como configurar uma instância do serviço gêmeos do Azure digital.
author: baanders
ms.author: baanders
ms.date: 4/22/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: fecacbd2c7c6549a1321367157bb179321779ca9
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2020
ms.locfileid: "86206520"
---
# <a name="set-up-an-azure-digital-twins-instance"></a>Configurar uma instância dos Gêmeos Digitais do Azure

Este artigo explicará as etapas básicas para configurar uma nova instância de gêmeos digital do Azure. Isso inclui a criação da instância e a atribuição de permissões [Azure Active Directory (AAD)](../active-directory/fundamentals/active-directory-whatis.md) à instância para você mesmo.

Caso não tenha uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [Cloud Shell for Azure Digital Twins](../../includes/digital-twins-cloud-shell.md)]

## <a name="set-up-an-azure-digital-twins-instance"></a>Configurar uma instância dos Gêmeos Digitais do Azure

Em seguida, você criará um novo grupo de recursos do Azure para uso neste "como". Em seguida, você pode **criar uma nova instância do Azure digital gêmeos** dentro desse grupo de recursos. 

Você também precisará fornecer um nome para sua instância e escolher uma região para a implantação. Para ver quais regiões dão suporte ao Azure digital gêmeos, visite [produtos do Azure disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins).

>[!NOTE]
> O nome da nova instância deve ser exclusivo dentro da região (ou seja, se outra instância de gêmeos digital do Azure nessa região já estiver usando o nome escolhido, você precisará escolher um nome diferente).

Crie o grupo de recursos e a instância com os seguintes comandos:

```azurecli
az group create --location <region> --name <name-for-your-resource-group>
az dt create --dt-name <name-for-your-Azure-Digital-Twins-instance> -g <your-resource-group> -l <region>
```

O resultado desses comandos é semelhante a este, gerando informações sobre os recursos que você criou:

:::image type="content" source="media/how-to-set-up-instance/create-instance.png" alt-text="janela Comando com a criação bem-sucedida do grupo de recursos e a instância do gêmeos digital do Azure":::

Anote o nome de *host*da instância do Azure digital gêmeos, *Name*e *resourcegroup* da saída. Esses são todos os valores importantes que você pode precisar ao continuar trabalhando com sua instância de gêmeos digital do Azure para configurar a autenticação e os recursos do Azure relacionados.

> [!TIP]
> Você pode ver essas propriedades, juntamente com todas as propriedades de sua instância, a qualquer momento executando `az dt show --dt-name <your-Azure-Digital-Twins-instance>` .

### <a name="assign-azure-active-directory-permissions"></a>Atribuir permissões de Azure Active Directory

O Azure digital gêmeos usa [Azure Active Directory (AAD)](../active-directory/fundamentals/active-directory-whatis.md) para controle de acesso baseado em função (RBAC). Isso significa que, antes de poder fazer chamadas de plano de dados para sua instância de gêmeos digital do Azure, você deve primeiro atribuir a si mesmo uma função com essas permissões.

Para usar o gêmeos digital do Azure com um aplicativo cliente, você também precisará certificar-se de que seu aplicativo cliente possa se autenticar no Azure digital gêmeos. Isso é feito configurando um registro de aplicativo Azure Active Directory (AAD), que você pode ler sobre [como autenticar um aplicativo cliente](how-to-authenticate-client.md).

#### <a name="assign-yourself-a-role"></a>Atribuir a si mesmo uma função

Crie uma atribuição de função para você mesmo na instância do gêmeos digital do Azure, usando seu email associado ao locatário do AAD em sua assinatura do Azure. 

Para poder fazer isso, você precisa ser classificado como um proprietário em sua assinatura do Azure. Você pode verificar isso executando o `az role assignment list --assignee <your-Azure-email>` comando e verificando na saída que o valor de *RoleDefinitionName* é *proprietário*. Se você achar que o valor é *colaborador* ou algo diferente do *proprietário*, entre em contato com o administrador da assinatura com a capacidade de conceder permissões em sua assinatura. Eles podem elevar sua função em toda a assinatura para que você possa executar o comando a seguir, ou um proprietário pode executar o comando a seguir em seu nome para configurar suas permissões de gêmeos digital do Azure para você.

Para atribuir as permissões de "proprietário" do usuário em sua instância do gêmeos digital do Azure, use o seguinte comando (deve ser executado por um proprietário da assinatura do Azure):

```azurecli
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<your-AAD-email>" --role "Azure Digital Twins Owner (Preview)"
```

O resultado desse comando são informações de saída sobre a atribuição de função que foi criada.

> [!TIP]
> Se você receber um erro *400: BadRequest* , execute o seguinte comando para obter o *ObjectID* para seu usuário:
> ```azurecli
> az ad user show --id <your-AAD-email> --query objectId
> ```
> Em seguida, repita o comando de atribuição de função usando a *ID de objeto* do usuário no lugar do seu email.

Agora você tem uma instância do gêmeos digital do Azure pronta para uso e permissões para gerenciá-la.

## <a name="next-steps"></a>Próximas etapas

Veja como configurar e autenticar um aplicativo cliente para trabalhar com sua instância:
* [Como: autenticar um aplicativo cliente](how-to-authenticate-client.md)
