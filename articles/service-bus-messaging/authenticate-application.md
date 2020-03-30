---
title: Autuar um aplicativo para acessar as entidades de Ônibus de Serviço do Azure
description: Este artigo fornece informações sobre a autenticação de um aplicativo com o Azure Active Directory para acessar entidades de Ônibus de Serviço do Azure (filas, tópicos, etc.)
services: service-bus-messaging
ms.service: event-hubs
documentationcenter: ''
author: axisc
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: aschhab
ms.openlocfilehash: 6a78e4d81921fae8dcb325e9d72df1eee7b99a3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259286"
---
# <a name="authenticate-and-authorize-an-application-with-azure-active-directory-to-access-azure-service-bus-entities"></a>Autuar e autorizar um aplicativo com o Azure Active Directory para acessar as entidades de Ônibus de Serviço do Azure
O Azure Service Bus suporta o uso do Azure Active Directory (Azure AD) para autorizar solicitações a entidades de Ônibus de Serviço (filas, tópicos, assinaturas ou filtros). Com o Azure AD, você pode usar o RBAC (Role-Based Access Control, controle de acesso baseado em função) para conceder permissões a um diretor de segurança, que pode ser um usuário, grupo ou diretor de serviço de aplicativo. Para saber mais sobre papéis e atribuições de papéis, consulte [Entendendo os diferentes papéis.](../role-based-access-control/overview.md)

## <a name="overview"></a>Visão geral
Quando um princípio de segurança (um usuário, grupo ou aplicativo) tenta acessar uma entidade de Ônibus de Serviço, a solicitação deve ser autorizada. Com o Azure AD, o acesso a um recurso é um processo de duas etapas. 

 1. Primeiro, a identidade do diretor de segurança é autenticada, e um token OAuth 2.0 é devolvido. O nome do recurso para `https://servicebus.azure.net`solicitar um token é .
 1. Em seguida, o token é passado como parte de uma solicitação ao serviço de Ônibus de Serviço para autorizar o acesso ao recurso especificado.

A etapa de autenticação requer que uma solicitação de aplicativo contenha um token de acesso OAuth 2.0 em tempo de execução. Se um aplicativo estiver sendo executado dentro de uma entidade Azure, como uma VM do Azure, um conjunto de escala de máquina virtual ou um aplicativo Azure Function, ele poderá usar uma identidade gerenciada para acessar os recursos. Para saber como autenticar solicitações feitas por uma identidade gerenciada para o serviço de ônibus de serviço, consulte [Authenticate acesso aos recursos do Azure Service Bus com o Azure Active Directory e identidades gerenciadas para recursos do Azure](service-bus-managed-service-identity.md). 

A etapa de autorização exige que uma ou mais funções RBAC sejam atribuídas ao diretor de segurança. O Azure Service Bus fornece funções RBAC que englobam conjuntos de permissões para recursos de Service Bus. As funções atribuídas a um diretor de segurança determinam as permissões que o diretor terá. Para saber mais sobre como atribuir funções RBAC ao Azure Service Bus, consulte [Funções RBAC incorporadas para ônibus de serviço Azure](#built-in-rbac-roles-for-azure-service-bus). 

Aplicativos nativos e aplicativos web que fazem solicitações ao Service Bus também podem autorizar com o Azure AD. Este artigo mostra como solicitar um token de acesso e usá-lo para autorizar solicitações de recursos de Ônibus de Serviço. 


## <a name="assigning-rbac-roles-for-access-rights"></a>Atribuindo funções RBAC para direitos de acesso
Azure Active Directory (Azure AD) autoriza os direitos de acesso aos recursos protegidos por meio do [controle de acesso baseado em função (RBAC)](../role-based-access-control/overview.md). O Azure Service Bus define um conjunto de funções RBAC incorporadas que abrangem conjuntos comuns de permissões usadas para acessar entidades de Ônibus de Serviço e você também pode definir funções personalizadas para acessar os dados.

Quando uma função RBAC é atribuída a um diretor de segurança do Azure AD, o Azure concede acesso a esses recursos para esse principal de segurança. O acesso pode ser escopo para o nível de assinatura, o grupo de recursos ou o espaço de nome do Ônibus de Serviço. Um diretor de segurança do Azure AD pode ser um usuário, um grupo, um diretor de serviço de aplicativo ou uma [identidade gerenciada para os recursos do Azure](../active-directory/managed-identities-azure-resources/overview.md).

## <a name="built-in-rbac-roles-for-azure-service-bus"></a>Funções RBAC incorporadas para ônibus de serviço azure
Para o Barramento de Serviço do Azure, o gerenciamento de namespaces e de todos os recursos relacionados por meio do portal do Azure e da API de gerenciamento de recursos do Azure já está protegido pelo modelo *RBAC (controle de acesso baseado em função)*. O Azure fornece as funções RBAC incorporadas abaixo para autorizar o acesso a um namespace de Ônibus de Serviço:

- [Proprietário de dados de barramento de serviço do Azure](../role-based-access-control/built-in-roles.md#azure-service-bus-data-owner): Permite o acesso de dados ao namespace do Service Bus e suas entidades (filas, tópicos, assinaturas e filtros)
- [Remetente de dados do ônibus de serviço do Azure](../role-based-access-control/built-in-roles.md#azure-service-bus-data-sender): Use essa função para dar acesso ao namespace do Service Bus e suas entidades.
- [Receptor de dados do ônibus de serviço do Azure](../role-based-access-control/built-in-roles.md#azure-service-bus-data-receiver): Use essa função para dar acesso ao namespace do Service Bus e suas entidades. 

## <a name="resource-scope"></a>Escopo de recursos 
Antes de atribuir uma função RBAC a um diretor de segurança, determine o escopo de acesso que o diretor de segurança deve ter. As melhores práticas ditam que é sempre melhor conceder apenas o escopo mais estreito possível.

A lista a seguir descreve os níveis nos quais você pode escopo de acesso aos recursos do Service Bus, começando pelo escopo mais estreito:

- **Fila,** **tópico**ou **assinatura**: A atribuição da função se aplica à entidade específica do Service Bus. Atualmente, o portal Azure não suporta atribuir usuários/grupos/identidades gerenciadas às funções rbac de ônibus de serviço no nível de assinatura. 
- **Espaço de nome do ônibus de serviço**: A atribuição de função abrange toda a topologia do Service Bus o namespace e para o grupo de consumidores associado a ele.
- **Grupo de recursos**: A atribuição de função se aplica a todos os recursos do Service Bus o grupo de recursos.
- **Assinatura**: A atribuição de função se aplica a todos os recursos do Service Bus em todos os grupos de recursos da assinatura.

> [!NOTE]
> Tenha em mente que as atribuições de função RBAC podem levar até cinco minutos para se propagar. 

Para obter mais informações sobre como os papéis incorporados são [definidos,](../role-based-access-control/role-definitions.md#management-and-data-operations)consulte Entender definições de função . Para obter informações sobre a criação de funções RBAC personalizadas, consulte [Criar funções personalizadas para o Controle de Acesso baseado em funções do Azure](../role-based-access-control/custom-roles.md).


## <a name="assign-rbac-roles-using-the-azure-portal"></a>Atribuir funções RBAC usando o portal Azure  
Para saber mais sobre como gerenciar o acesso aos recursos do Azure usando o RBAC e o portal Azure, consulte [este artigo](..//role-based-access-control/role-assignments-portal.md). 

Depois de determinar o escopo apropriado para uma atribuição de função, navegue até esse recurso no portal Azure. Exiba as configurações de controle de acesso (IAM) para o recurso e siga estas instruções para gerenciar as atribuições da função:

> [!NOTE]
> As etapas descritas abaixo atribuem uma função ao seu espaço de nome do Service Bus. Você pode seguir os mesmos passos para atribuir uma função a outros escopos suportados (grupo de recursos, assinatura, etc.).

1. No [portal Azure,](https://portal.azure.com/)navegue até o espaço de nome do seu Ônibus de Serviço. Selecione **Controle de acesso (IAM)** no menu esquerdo para exibir as configurações de controle de acesso para o namespace. Se você precisar criar um namespace do Service Bus, siga as instruções deste artigo: [Crie um espaço de nome de mensagens de ônibus de serviço](service-bus-create-namespace-portal.md).

    ![Selecione Controle de acesso no menu esquerdo](./media/authenticate-application/select-access-control-menu.png)
1. Selecione a guia **Atribuições de função** para ver as atribuições de função atuais. Selecione o botão **Adicionar** na barra de ferramentas e, em seguida, **selecione Adicionar atribuição de função**. 

    ![Adicionar botão na barra de ferramentas](./media/authenticate-application/role-assignments-add-button.png)
1. Na página **Adicionar atribuição de função,** faça as seguintes etapas:
    1. Selecione a **função Ônibus de Serviço** que deseja atribuir. 
    1. Pesquise para localizar o **principal de segurança** (usuário, grupo, principal de serviço) ao qual você deseja atribuir a função.
    1. Selecione **Salvar** para salvar a atribuição de função. 

        ![Atribuir função a um usuário](./media/authenticate-application/assign-role-to-user.png)
    4. A identidade à qual você atribuiu a função aparece listada sob essa função. Por exemplo, a imagem a seguir mostra que os usuários do Azure estão na função Proprietário de dados do barramento de ônibus do Azure Service. 
        
        ![Usuário na lista](./media/authenticate-application/user-in-list.png)

Você pode seguir etapas semelhantes para atribuir uma função escopo a um grupo de recursos ou a uma assinatura. Uma vez que você define a função e seu escopo, você pode testar esse comportamento com as [amostras no GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/RoleBasedAccessControl).


## <a name="authenticate-from-an-application"></a>Autenticar a partir de um aplicativo
Uma vantagem fundamental de usar o Azure AD com service bus é que suas credenciais não precisam mais ser armazenadas em seu código. Em vez disso, você pode solicitar um token de acesso OAuth 2.0 da plataforma de identidade Microsoft. O Azure AD autentica o principal de segurança (um usuário, um grupo ou principal de serviço) executando o aplicativo. Se a autenticação for bem-sucedida, o Azure AD retorna o token de acesso ao aplicativo e, em seguida, o aplicativo poderá usar o token de acesso para autorizar solicitações ao Azure Service Bus.

As seguintes seções mostram como configurar seu aplicativo nativo ou aplicativo web para autenticação com a plataforma de identidade 2.0 da Microsoft. Para obter mais informações sobre a plataforma de identidade Microsoft 2.0, consulte [a visão geral da plataforma de identidade da Microsoft (v2.0).](../active-directory/develop/v2-overview.md)

Para obter uma visão geral do fluxo de concessão do código do OAuth 2.0, consulte [Autorizar acesso a aplicativos Web do Azure Active Directory usando o fluxo de concessão do código do OAuth 2.0](../active-directory/develop/v2-oauth2-auth-code-flow.md).

### <a name="register-your-application-with-an-azure-ad-tenant"></a>Registre o aplicativo com um locatário do Azure AD
O primeiro passo para usar o Azure AD para autorizar entidades de Ônibus de Serviço é cadastrar seu aplicativo cliente com um inquilino Azure AD do [portal Azure](https://portal.azure.com/). Quando você registra seu aplicativo cliente, você fornece informações sobre o aplicativo para a AD. O Azure AD fornece então um ID do cliente (também chamado de ID de aplicativo) que você pode usar para associar seu aplicativo ao tempo de execução do Azure AD. Para saber mais sobre a ID do cliente, consulte [Objetos de entidade de serviço e aplicativo no Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md). 

As seguintes imagens mostram etapas para registrar um aplicativo web:

![Registrar um aplicativo](./media/authenticate-application/app-registrations-register.png)

> [!Note]
> Se você registrar seu aplicativo como um aplicativo nativo, você pode especificar qualquer URI válido para o URI redirecionar. Para aplicações nativas, esse valor não precisa ser uma URL real. Para aplicações web, o URI de redirecionamento deve ser um URI válido, porque especifica a URL para a qual os tokens são fornecidos.

Depois de registrar seu aplicativo, você verá o ID do **aplicativo (cliente)** em **Configurações:**

![ID de inscrição do aplicativo registrado](./media/authenticate-application/application-id.png)

Para obter mais informações sobre como registrar um aplicativo no Azure AD, consulte [Integrando aplicativos com o Azure Active Directory](../active-directory/develop/quickstart-v2-register-an-app.md).

> [!IMPORTANT]
> Anote o **TenantId** e o **ApplicationId**. Você precisará desses valores para executar o aplicativo.

### <a name="create-a-client-secret"></a>Criar um segredo do cliente   
O aplicativo precisa de um segredo do cliente para provar sua identidade ao solicitar um token. Para adicionar o segredo do cliente, siga estes passos.

1. Navegue até o seu registro de aplicativo no portal Azure se você ainda não estiver na página.
1. Selecione **Certificados & segredos** no menu esquerdo.
1. Em **segredos do Cliente,** selecione **novo segredo do cliente** para criar um novo segredo.

    ![Novo segredo do cliente - botão](./media/authenticate-application/new-client-secret-button.png)
1. Forneça uma descrição do segredo e escolha o intervalo de expiração desejado e, em seguida, **selecione Adicionar**.

    ![Adicionar página secreta do cliente](./media/authenticate-application/add-client-secret-page.png)
1. Copie imediatamente o valor do novo segredo para um local seguro. O valor de preenchimento é exibido para você apenas uma vez.

    ![Segredo do cliente](./media/authenticate-application/client-secret.png)

### <a name="permissions-for-the-service-bus-api"></a>Permissões para a API de ônibus de serviço
Se o aplicativo for um aplicativo de console, você deve registrar um aplicativo nativo e adicionar permissões de API para o **Microsoft.ServiceBus** às **permissões necessárias definidas.** Os aplicativos nativos também precisam de um **URI de redirecionamento** no Azure AD, que serve como um identificador; o URI não precisa ser um destino de rede. Use `https://servicebus.microsoft.com` para este exemplo, porque o exemplo de código já usa esse URI.

### <a name="client-libraries-for-token-acquisition"></a>Bibliotecas de clientes para aquisição de tokens  
Depois de registrar seu aplicativo e conceder permissões para enviar/receber dados no Azure Service Bus, você pode adicionar código ao seu aplicativo para autenticar um principal de segurança e adquirir o token OAuth 2.0. Para autenticar e adquirir o token, você pode usar uma das bibliotecas de [autenticação](../active-directory/develop/reference-v2-libraries.md) da plataforma de identidade da Microsoft ou outra biblioteca de código aberto que suporta OpenID ou Connect 1.0. Seu aplicativo pode então usar o token de acesso para autorizar uma solicitação contra o Azure Service Bus.

Para obter uma lista de cenários para os quais a aquisição de tokens é suportada, consulte a seção [Cenários](https://aka.ms/msal-net-scenarios) da Biblioteca de [Autenticação microsoft (MSAL) para](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) o repositório .NET GitHub.

## <a name="sample-on-github"></a>Exemplo no GitHub
Veja a seguinte amostra no GitHub: [Controle de acesso de base de função para ônibus de serviço](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/RoleBasedAccessControl). 

Use a opção **Login Secreto do Cliente,** não a opção **de login do usuário interativo.** Quando você usa a opção secreta do cliente, você não vê uma janela pop-up. O aplicativo utiliza o ID do inquilino e o ID do aplicativo para autenticação. 

### <a name="run-the-sample"></a>Execute o exemplo

Antes de executar a amostra, edite o arquivo **app.config** e, dependendo do seu cenário, defina os seguintes valores:

- `tenantId`: defina com o valor **TenantId**.
- `clientId`: defina com o valor **ApplicationId**.
- `clientSecret`: se você quiser entrar usando o segredo do cliente, crie-o no Azure AD. Além disso, use um aplicativo Web ou uma API em vez de um aplicativo nativo. E também adicione o aplicativo no **Controle de Acesso (IAM)** no namespace que você criou anteriormente.
- `serviceBusNamespaceFQDN`: defina o nome DNS completo do namespace do Barramento de Serviço recém-criado, por exemplo, `example.servicebus.windows.net`.
- `queueName`: defina como o nome da fila criada.
- O URI de redirecionamento que você especificou no aplicativo nas etapas anteriores.

Quando você executa o aplicativo do console, você é solicitado a selecionar um cenário. Selecione **Login interativo do usuário** digitando seu número e pressionando ENTER. O aplicativo exibe uma janela de logon, solicita seu consentimento para acessar o Barramento de Serviço e, em seguida, usa o serviço para executar o cenário de envio/recebimento usando a identidade de logon.


## <a name="next-steps"></a>Próximas etapas
- Para saber mais sobre o RBAC, consulte [O que é o controle de acesso baseado em função (RBAC)](../role-based-access-control/overview.md)?
- Para saber como atribuir e gerenciar atribuições de função do RBAC com Azure PowerShell, CLI do Azure ou API REST, consulte estes artigos:
    - [Gerenciar o controle de acesso baseado em função (RBAC) com o Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)  
    - [Gerenciar o controle de acesso baseado em função (RBAC) com a CLI do Azure](../role-based-access-control/role-assignments-cli.md)
    - [Gerenciar o controle de acesso baseado em função (RBAC) com a API REST](../role-based-access-control/role-assignments-rest.md)
    - [Gerenciar o RBAC (Role-Based Access Control, controle de acesso baseado em função) com os modelos do Gerenciador de recursos do Azure](../role-based-access-control/role-assignments-template.md)

Para saber mais sobre as mensagens do Barramento de Serviço, confira os tópicos a seguir.

- [Amostras de RBAC do Ônibus de Serviço](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/RoleBasedAccessControl)
- [Filas, tópicos e assinaturas do Barramento de Serviço](service-bus-queues-topics-subscriptions.md)
- [Introdução às filas do Barramento de Serviço](service-bus-dotnet-get-started-with-queues.md)
- [Como usar tópicos e assinaturas do Barramento de Serviço](service-bus-dotnet-how-to-use-topics-subscriptions.md)
