---
title: Auteire um aplicativo para acessar os recursos do Azure Event Hubs
description: Este artigo fornece informações sobre como autenticar um aplicativo com o Azure Active Directory para acessar os recursos do Azure Event Hubs
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: spelluru
ms.openlocfilehash: a242da8cc98a21248c48a1b3981fa713706028ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064953"
---
# <a name="authenticate-an-application-with-azure-active-directory-to-access-event-hubs-resources"></a>Autuar um aplicativo com o Azure Active Directory para acessar os recursos do Event Hubs
O Microsoft Azure fornece gerenciamento de controle de acesso integrado para recursos e aplicativos com base no Azure Active Directory (Azure AD). Uma vantagem fundamental de usar o Azure AD com o Azure Event Hubs é que você não precisa mais armazenar suas credenciais no código. Em vez disso, você pode solicitar um token de acesso OAuth 2.0 da plataforma Microsoft Identity. O nome do recurso para `https://eventhubs.azure.net/` solicitar um token é (Para clientes Kafka, o recurso para solicitar um token é `https://<namespace>.servicebus.windows.net`). O Azure AD autentica o principal de segurança (um usuário, grupo ou diretor de serviço) executando o aplicativo. Se a autenticação for bem-sucedida, o Azure AD retorna um token de acesso ao aplicativo e, em seguida, o aplicativo poderá usar o token de acesso para autorizar a solicitação aos recursos do Azure Event Hubs.

Quando uma função é atribuída a um diretor de segurança do Azure AD, o Azure concede acesso a esses recursos para esse principal de segurança. O acesso pode ser escopo para o nível de assinatura, o grupo de recursos, o namespace do Event Hubs ou qualquer recurso ele. Uma segurança Azure AD pode atribuir funções a um usuário, um grupo, um diretor de serviço de aplicativo ou uma [identidade gerenciada para os recursos do Azure](../active-directory/managed-identities-azure-resources/overview.md). 

> [!NOTE]
> Uma definição de função é uma coleção de permissões. O RBAC (Role-based Access Control Control, controle baseado em função) controla como essas permissões são aplicadas por meio da atribuição de papéis. Uma atribuição de função consiste em três elementos: entidade de segurança, definição de função e escopo. Para obter mais informações, consulte [Entendendo os diferentes papéis.](../role-based-access-control/overview.md)

## <a name="built-in-roles-for-azure-event-hubs"></a>Funções incorporadas para hubs de eventos do Azure
O Azure fornece as seguintes funções de RBAC incorporadas para autorizar o acesso aos dados do Event Hubs usando o Azure AD e o OAuth:

- [Azure Event Hubs Data Owner](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-owner): Use essa função para dar acesso completo aos recursos do Event Hubs.
- [Remetente de dados do Azure Event Hubs](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-sender): Use essa função para dar acesso ao envio de recursos do Event Hubs.
- [Receptor de dados do Azure Event Hubs](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-receiver): Use essa função para dar acesso aos recursos do Event Hubs.   

> [!IMPORTANT]
> Nossa versão de pré-visualização suportava adicionar privilégios de acesso de dados do Event Hubs à função proprietário ou contribuinte. No entanto, os privilégios de acesso de dados para o papel de Proprietário e Contribuinte não são mais honrados. Se você estiver usando a função Proprietário ou Contribuinte, mude para usar a função Proprietário de dados do Azure Event Hubs.

## <a name="assign-rbac-roles-using-the-azure-portal"></a>Atribuir funções RBAC usando o portal Azure  
Para saber mais sobre como gerenciar o acesso aos recursos do Azure usando o RBAC e o portal Azure, consulte [este artigo](..//role-based-access-control/role-assignments-portal.md). 

Depois de determinar o escopo apropriado para uma atribuição de função, navegue até esse recurso no portal Azure. Exiba as configurações de controle de acesso (IAM) para o recurso e siga estas instruções para gerenciar as atribuições da função:

> [!NOTE]
> As etapas descritas abaixo atribuem uma função ao seu hub de eventos os namespaces do Event Hubs, mas você pode seguir as mesmas etapas para atribuir uma função escopo a qualquer recurso do Event Hubs.

1. No [portal Azure,](https://portal.azure.com/)navegue até o namespace do Event Hubs.
2. Na **página Visão geral,** selecione o centro de eventos para o qual você deseja atribuir uma função.

    ![Selecione seu hub de eventos](./media/authenticate-application/select-event-hub.png)
1. Selecione **O Controle de Acesso (IAM)** para exibir as configurações de controle de acesso para o hub de eventos. 
1. Selecione a guia **Atribuições de função** para ver as atribuições de função atuais. Selecione o botão **Adicionar** na barra de ferramentas e, em seguida, **selecione Adicionar atribuição de função**. 

    ![Adicionar botão na barra de ferramentas](./media/authenticate-application/role-assignments-add-button.png)
1. Na página **Adicionar atribuição de função,** faça as seguintes etapas:
    1. Selecione a **função Event Hubs** que você deseja atribuir. 
    1. Pesquise para localizar o **principal de segurança** (usuário, grupo, principal de serviço) ao qual você deseja atribuir a função.
    1. Selecione **Salvar** para salvar a atribuição de função. 

        ![Atribuir função a um usuário](./media/authenticate-application/assign-role-to-user.png)
    4. A identidade à qual você atribuiu a função aparece listada sob essa função. Por exemplo, a imagem a seguir mostra que os usuários do Azure estão na função Proprietário de dados do Azure Event Hubs. 
        
        ![Usuário na lista](./media/authenticate-application/user-in-list.png)

Você pode seguir etapas semelhantes para atribuir uma função escopo ao namespace, grupo de recursos ou assinatura do Event Hubs. Uma vez que você define a função e seu escopo, você pode testar esse comportamento com amostras [neste local do GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac).


## <a name="authenticate-from-an-application"></a>Autenticar a partir de um aplicativo
Uma vantagem fundamental de usar o Azure AD com o Event Hubs é que suas credenciais não precisam mais ser armazenadas em seu código. Em vez disso, você pode solicitar um token de acesso OAuth 2.0 da plataforma de identidade Microsoft. O Azure AD autentica o principal de segurança (um usuário, um grupo ou principal de serviço) executando o aplicativo. Se a autenticação for bem-sucedida, o Azure AD retorna o token de acesso ao aplicativo e, em seguida, o aplicativo poderá usar o token de acesso para autorizar solicitações ao Azure Event Hubs.

As seguintes seções mostram como configurar seu aplicativo nativo ou aplicativo web para autenticação com a plataforma de identidade 2.0 da Microsoft. Para obter mais informações sobre a plataforma de identidade Microsoft 2.0, consulte [a visão geral da plataforma de identidade da Microsoft (v2.0).](../active-directory/develop/v2-overview.md)

Para obter uma visão geral do fluxo de concessão do código do OAuth 2.0, consulte [Autorizar acesso a aplicativos Web do Azure Active Directory usando o fluxo de concessão do código do OAuth 2.0](../active-directory/develop/v2-oauth2-auth-code-flow.md).

### <a name="register-your-application-with-an-azure-ad-tenant"></a>Registre o aplicativo com um locatário do Azure AD
O primeiro passo para usar o Azure AD para autorizar os recursos do Event Hubs é registrar seu aplicativo cliente com um inquilino Azure AD do [portal Azure](https://portal.azure.com/). Quando você registra seu aplicativo cliente, você fornece informações sobre o aplicativo para a AD. O Azure AD fornece então um ID do cliente (também chamado de ID de aplicativo) que você pode usar para associar seu aplicativo ao tempo de execução do Azure AD. Para saber mais sobre a ID do cliente, consulte [Objetos de entidade de serviço e aplicativo no Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md). 

As seguintes imagens mostram etapas para registrar um aplicativo web:

![Registrar um aplicativo](./media/authenticate-application/app-registrations-register.png)

> [!Note]
> Se você registrar seu aplicativo como um aplicativo nativo, você pode especificar qualquer URI válido para o URI redirecionar. Para aplicações nativas, esse valor não precisa ser uma URL real. Para aplicações web, o URI de redirecionamento deve ser um URI válido, porque especifica a URL para a qual os tokens são fornecidos.

Depois de registrar seu aplicativo, você verá o ID do **aplicativo (cliente)** em **Configurações:**

![ID de inscrição do aplicativo registrado](./media/authenticate-application/application-id.png)

Para obter mais informações sobre como registrar um aplicativo no Azure AD, consulte [Integrando aplicativos com o Azure Active Directory](../active-directory/develop/quickstart-v2-register-an-app.md).


### <a name="create-a-client-secret"></a>Criar um segredo do cliente   
O aplicativo precisa de um segredo do cliente para provar sua identidade ao solicitar um token. Para adicionar o segredo do cliente, siga estes passos.

1. Navegue até o seu registro de aplicativo no portal Azure.
1. Selecione a configuração **de segredos & Certificados.**
1. Em **segredos do Cliente,** selecione **novo segredo do cliente** para criar um novo segredo.
1. Forneça uma descrição do segredo e escolha o intervalo de expiração desejado.
1. Copie imediatamente o valor do novo segredo para um local seguro. O valor de preenchimento é exibido para você apenas uma vez.

    ![Segredo do cliente](./media/authenticate-application/client-secret.png)


### <a name="client-libraries-for-token-acquisition"></a>Bibliotecas de clientes para aquisição de tokens  
Depois de registrar seu aplicativo e conceder permissões para enviar/receber dados no Azure Event Hubs, você pode adicionar código ao seu aplicativo para autenticar um principal de segurança e adquirir o token OAuth 2.0. Para autenticar e adquirir o token, você pode usar uma das bibliotecas de [autenticação](../active-directory/develop/reference-v2-libraries.md) da plataforma de identidade da Microsoft ou outra biblioteca de código aberto que suporta OpenID ou Connect 1.0. Seu aplicativo pode então usar o token de acesso para autorizar uma solicitação contra o Azure Event Hubs.

Para obter uma lista de cenários para os quais a aquisição de tokens é suportada, consulte a seção [Cenários](https://aka.ms/msal-net-scenarios) da Biblioteca de [Autenticação microsoft (MSAL) para](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) o repositório .NET GitHub.

## <a name="samples"></a>Exemplos
- [Amostras do Microsoft.Azure.EventHubs](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac). 
    
    Essas amostras usam a antiga biblioteca **Microsoft.Azure.EventHubs,** mas você pode atualizá-la facilmente para usar a biblioteca mais recente **do Azure.Messaging.EventHubs.** Para mover a amostra do uso da biblioteca antiga para uma nova, consulte o [Guia para migrar do Microsoft.Azure.EventHubs para o Azure.Messaging.EventHubs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/migration-guide-from-v4.md).
- [Azure.Messaging.EventHubs amostras](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp)

    Esta amostra foi atualizada para usar a biblioteca Mais recente **do Azure.Messaging.EventHubs.**

## <a name="next-steps"></a>Próximas etapas
- Para saber mais sobre o RBAC, consulte [O que é o controle de acesso baseado em função (RBAC)](../role-based-access-control/overview.md)?
- Para saber como atribuir e gerenciar atribuições de função do RBAC com Azure PowerShell, CLI do Azure ou API REST, consulte estes artigos:
    - [Gerenciar o controle de acesso baseado em função (RBAC) com o Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)  
    - [Gerenciar o controle de acesso baseado em função (RBAC) com a CLI do Azure](../role-based-access-control/role-assignments-cli.md)
    - [Gerenciar o controle de acesso baseado em função (RBAC) com a API REST](../role-based-access-control/role-assignments-rest.md)
    - [Gerenciar o RBAC (Role-Based Access Control, controle de acesso baseado em função) com os modelos do Gerenciador de recursos do Azure](../role-based-access-control/role-assignments-template.md)

Veja os seguintes artigos relacionados:
- [Autenticar uma identidade gerenciada com o Azure Active Directory para acessar recursos do Event Hubs](authenticate-managed-identity.md)
- [Autenticar solicitações ao Azure Event Hubs usando assinaturas de acesso compartilhadas](authenticate-shared-access-signature.md)
- [Autorize o acesso aos recursos do Event Hubs usando o Azure Active Directory](authorize-access-azure-active-directory.md)
- [Autorize o acesso aos recursos do Event Hubs usando assinaturas de acesso compartilhado](authorize-access-shared-access-signature.md)

