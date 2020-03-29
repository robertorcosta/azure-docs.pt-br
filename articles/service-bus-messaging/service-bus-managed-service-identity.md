---
title: Identidades gerenciadas para recursos do Azure com Barramento de Serviço
description: Este artigo descreve como usar identidades gerenciadas para acessar com as entidades de Ônibus de Serviço do Azure (filas, tópicos e assinaturas).
services: service-bus-messaging
documentationcenter: na
author: axisc
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: 89de6bf80d14ec77fe6b1f98b6e1d15c6e573fbe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76756276"
---
# <a name="authenticate-a-managed-identity-with-azure-active-directory-to-access-azure-service-bus-resources"></a>Autenticar uma identidade gerenciada com o Azure Active Directory para acessar os recursos do Azure Service Bus
[Identidades gerenciadas para recursos do Azure](../active-directory/managed-identities-azure-resources/overview.md) é um recurso do Azure que permite criar uma identidade segura associada à implantação na qual o código do aplicativo é executado. Em seguida, você pode associar essa identidade a funções de controle de acesso que concedem permissões personalizadas para acessar os recursos do Azure específicos que seu aplicativo precisa.

Com identidades gerenciadas, a plataforma do Azure gerencia essa identidade de runtime. Você não precisa armazenar e proteger as chaves de acesso no código ou na configuração do aplicativo, seja para a identidade ou para os recursos que você precisa acessar. Um aplicativo cliente do Barramento de Serviço em execução dentro de um aplicativo de Serviço de Aplicativo do Azure ou em uma máquina virtual com entidades gerenciadas habilitadas para suporte de recursos do Azure não precisa lidar com regras e chaves de SAS nem com nenhum outro token de acesso. O aplicativo cliente precisa apenas do endereço do ponto de extremidade do namespace das Mensagens do Barramento de Serviço. Quando o aplicativo se conecta, o Barramento de Serviço associa o contexto de entidade gerenciada ao cliente em uma operação que será mostrada em um exemplo mais adiante neste artigo. Depois que está associado com uma identidade gerenciada, o cliente do Barramento de Serviço pode realizar todas as operações autorizadas. A autorização é concedida ao associar uma entidade gerenciada com as funções do Barramento de Serviço. 

## <a name="overview"></a>Visão geral
Quando um princípio de segurança (um usuário, grupo ou aplicativo) tenta acessar uma entidade de Ônibus de Serviço, a solicitação deve ser autorizada. Com o Azure AD, o acesso a um recurso é um processo de duas etapas. 

 1. Primeiro, a identidade do diretor de segurança é autenticada, e um token OAuth 2.0 é devolvido. O nome do recurso para `https://servicebus.azure.net`solicitar um token é .
 1. Em seguida, o token é passado como parte de uma solicitação ao serviço de Ônibus de Serviço para autorizar o acesso ao recurso especificado.

A etapa de autenticação requer que uma solicitação de aplicativo contenha um token de acesso OAuth 2.0 em tempo de execução. Se um aplicativo estiver sendo executado dentro de uma entidade Azure, como uma VM do Azure, um conjunto de escala de máquina virtual ou um aplicativo Azure Function, ele poderá usar uma identidade gerenciada para acessar os recursos. 

A etapa de autorização exige que uma ou mais funções RBAC sejam atribuídas ao diretor de segurança. O Azure Service Bus fornece funções RBAC que englobam conjuntos de permissões para recursos de Service Bus. As funções atribuídas a um diretor de segurança determinam as permissões que o diretor terá. Para saber mais sobre como atribuir funções RBAC ao Azure Service Bus, consulte [Funções RBAC incorporadas para ônibus de serviço Azure](#built-in-rbac-roles-for-azure-service-bus). 

Aplicativos nativos e aplicativos web que fazem solicitações ao Service Bus também podem autorizar com o Azure AD. Este artigo mostra como solicitar um token de acesso e usá-lo para autorizar solicitações de recursos de Ônibus de Serviço. 


## <a name="assigning-rbac-roles-for-access-rights"></a>Atribuindo funções RBAC para direitos de acesso
Azure Active Directory (Azure AD) autoriza os direitos de acesso aos recursos protegidos por meio do [controle de acesso baseado em função (RBAC)](../role-based-access-control/overview.md). O Azure Service Bus define um conjunto de funções RBAC incorporadas que abrangem conjuntos comuns de permissões usadas para acessar entidades de Ônibus de Serviço e você também pode definir funções personalizadas para acessar os dados.

Quando uma função RBAC é atribuída a um diretor de segurança do Azure AD, o Azure concede acesso a esses recursos para esse principal de segurança. O acesso pode ser escopo para o nível de assinatura, o grupo de recursos ou o espaço de nome do Ônibus de Serviço. Um diretor de segurança do Azure AD pode ser um usuário, um grupo, um diretor de serviço de aplicativo ou uma identidade gerenciada para os recursos do Azure.

## <a name="built-in-rbac-roles-for-azure-service-bus"></a>Funções RBAC incorporadas para ônibus de serviço azure
Para o Barramento de Serviço do Azure, o gerenciamento de namespaces e de todos os recursos relacionados por meio do portal do Azure e da API de gerenciamento de recursos do Azure já está protegido pelo modelo *RBAC (controle de acesso baseado em função)*. O Azure fornece as funções RBAC incorporadas abaixo para autorizar o acesso a um namespace de Ônibus de Serviço:

- [Proprietário de dados de barramento de serviço do Azure](../role-based-access-control/built-in-roles.md#azure-service-bus-data-owner): Permite o acesso de dados ao namespace do Service Bus e suas entidades (filas, tópicos, assinaturas e filtros)
- [Remetente de dados do ônibus de serviço do Azure](../role-based-access-control/built-in-roles.md#azure-service-bus-data-sender): Use essa função para dar acesso ao namespace do Service Bus e suas entidades.
- [Receptor de dados do ônibus de serviço do Azure](../role-based-access-control/built-in-roles.md#azure-service-bus-data-receiver): Use essa função para dar acesso ao namespace do Service Bus e suas entidades. 

## <a name="resource-scope"></a>Escopo de recursos 
Antes de atribuir uma função RBAC a um diretor de segurança, determine o escopo de acesso que o diretor de segurança deve ter. As melhores práticas ditam que é sempre melhor conceder apenas o escopo mais estreito possível.

A lista a seguir descreve os níveis nos quais você pode escopo de acesso aos recursos do Service Bus, começando pelo escopo mais estreito:

- **Fila,** **tópico**ou **assinatura**: A atribuição da função se aplica à entidade específica do Service Bus. Atualmente, o portal Azure não suporta atribuir usuários/grupos/identidades gerenciadas às funções rbac de ônibus de serviço no nível de assinatura. Aqui está um exemplo de uso do comando Azure CLI: [az-role-assignment-create](/cli/azure/role/assignment?view=azure-cli-latest#az-role-assignment-create) para atribuir uma identidade a uma função RBAC de ônibus de serviço: 

    ```azurecli
    az role assignment create \
        --role $service_bus_role \
        --assignee $assignee_id \
        --scope /subscriptions/$subscription_id/resourceGroups/$resource_group/providers/Microsoft.ServiceBus/namespaces/$service_bus_namespace/topics/$service_bus_topic/subscriptions/$service_bus_subscription
    ```
- **Espaço de nome do ônibus de serviço**: A atribuição de função abrange toda a topologia do Service Bus o namespace e para o grupo de consumidores associado a ele.
- **Grupo de recursos**: A atribuição de função se aplica a todos os recursos do Service Bus o grupo de recursos.
- **Assinatura**: A atribuição de função se aplica a todos os recursos do Service Bus em todos os grupos de recursos da assinatura.

> [!NOTE]
> Tenha em mente que as atribuições de função RBAC podem levar até cinco minutos para se propagar. 

Para obter mais informações sobre como os papéis incorporados são [definidos,](../role-based-access-control/role-definitions.md#management-and-data-operations)consulte Entender definições de função . Para obter informações sobre a criação de funções RBAC personalizadas, consulte [Criar funções personalizadas para o Controle de Acesso baseado em funções do Azure](../role-based-access-control/custom-roles.md).

## <a name="enable-managed-identities-on-a-vm"></a>Habilitar identidades gerenciadas em uma VM
Antes de usar identidades gerenciadas para recursos do Azure para autorizar os recursos do Service Bus de sua VM, você deve primeiro habilitar identidades gerenciadas para recursos do Azure na VM. Para saber como habilitar identidades gerenciadas para Recursos do Azure, confira um dos seguintes artigos:

- [Portal Azure](../active-directory/managed-service-identity/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Modelo de Gerenciador de recursos do Azure](../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Bibliotecas de clientes do Azure Resource Manager](../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="grant-permissions-to-a-managed-identity-in-azure-ad"></a>Conceder permissões a uma identidade gerenciada no Azure AD
Para autorizar uma solicitação ao serviço de Ônibus de Serviço a partir de uma identidade gerenciada em seu aplicativo, primeiro configure as configurações de Controle de acesso (RBAC) baseada em função para essa identidade gerenciada. O Azure Service Bus define funções RBAC que englobam permissões para envio e leitura do Service Bus. Quando a função RBAC é atribuída a uma identidade gerenciada, a identidade gerenciada é concedida acesso às entidades de Ônibus de Serviço no escopo apropriado.

Para obter mais informações sobre como atribuir funções RBAC, consulte [Authenticate e autorize com o Azure Active Directory para acesso aos recursos do Service Bus](authenticate-application.md#built-in-rbac-roles-for-azure-service-bus).

## <a name="use-service-bus-with-managed-identities-for-azure-resources"></a>Usar Barramento de Serviço com identidades gerenciadas para recursos do Azure
Para usar o Service Bus com identidades gerenciadas, você precisa atribuir à identidade a função e o escopo apropriado. O procedimento nesta seção usa um aplicativo simples que funciona uma identidade gerenciada e acessa os recursos do Service Bus.

Aqui estamos usando um aplicativo web de exemplo hospedado no [Azure App Service](https://azure.microsoft.com/services/app-service/). Para obter instruções passo a passo para criar um aplicativo web, consulte [Criar um aplicativo web ASP.NET Core no Azure](../app-service/app-service-web-get-started-dotnet.md)

Uma vez que o aplicativo seja criado, siga estas etapas: 

1. Vá para **Configurações** e selecione **Identidade**. 
1. Selecione o **Status** a ser **ligado**. 
1. Clique em **Salvar** para salvar a configuração. 

    ![Identidade gerenciada para um aplicativo web](./media/service-bus-managed-service-identity/identity-web-app.png)

Uma vez habilitada essa configuração, uma nova identidade de serviço é criada no Azure Active Directory (Azure AD) e configurada no host App Service.

Agora, atribua essa identidade de serviço a uma função no escopo necessário nos recursos do seu Service Bus.

### <a name="to-assign-rbac-roles-using-the-azure-portal"></a>Para atribuir funções RBAC usando o portal Azure
Para atribuir uma função a um namespace do Service Bus, navegue até o namespace no portal Azure. Exiba as configurações de Controle de acesso (IAM) para o recurso e siga estas instruções para gerenciar as atribuições da função:

> [!NOTE]
> As etapas a seguir atribuem uma função de identidade de serviço aos seus namespaces de Ônibus de Serviço. Você pode seguir os mesmos passos para atribuir uma função em outros escopos suportados (grupo de recursos e assinatura). 
> 
> [Crie um espaço de nome de mensagens de ônibus de serviço](service-bus-create-namespace-portal.md) se você não tiver um. 

1. No portal Azure, navegue até o espaço de nome do seu Ônibus de Serviço e exiba a **visão geral** do namespace. 
1. Selecione **Controle de acesso (IAM)** no menu esquerdo para exibir as configurações de controle de acesso para o espaço de nome do Ponto de Serviço.
1.  Selecione a guia **Atribuições de função** para ver as atribuições de função atuais.
3.  Selecione **Adicionar** para adicionar uma nova função.
4.  Na página **Adicionar função de função,** selecione as funções de Barramento de Serviço do Azure que você deseja atribuir. Em seguida, pesquise para localizar a identidade de serviço que você registrou para atribuir a função.
    
    ![Adicionar página de atribuição de função](./media/service-bus-managed-service-identity/add-role-assignment-page.png)
5.  Selecione **Salvar**. A identidade à qual você atribuiu a função aparece listada sob essa função. Por exemplo, a imagem a seguir mostra que a identidade do serviço tem o proprietário do Azure Service Bus Data.
    
    ![Identidade atribuída a um papel](./media/service-bus-managed-service-identity/role-assigned.png)

Depois de atribuir a função, o aplicativo web terá acesso às entidades do Ônibus de Serviço o escopo definido. 

### <a name="run-the-app"></a>Executar o aplicativo

Agora, modifique a página padrão do aplicativo ASP.NET que você criou. Também é possível usar o código do aplicativo Web [deste repositório GitHub](https://github.com/Azure-Samples/app-service-msi-servicebus-dotnet).  

A página Default.aspx é sua página de aterrissagem. O código pode ser encontrado no arquivo Default.aspx.cs. O resultado é um aplicativo Web mínimo com alguns campos de entrada e os botões **enviar** e **receber** que se conectam ao Barramento de Serviço para enviar ou receber mensagens.

Observe como o objeto [MessagingFactory](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) é inicializado. Em vez de usar o provedor de token SAS (Token de Acesso Compartilhado), o código cria um provedor de token para a identidade gerenciada com a chamada `var msiTokenProvider = TokenProvider.CreateManagedIdentityTokenProvider();`. Dessa forma, não há nenhum segredo a ser retido e uso. O fluxo do contexto de identidade gerenciada para o Barramento de Serviço e o handshake de autorização são manipulados automaticamente pelo provedor de token. É um modelo mais simples que usar SAS.

Depois de fazer essas alterações, publique e execute o aplicativo. Você pode facilmente obter os dados de publicação corretos baixando e, em seguida, importando um perfil de publicação no Visual Studio:

![Obter perfil de publicação](./media/service-bus-managed-service-identity/msi3.png)
 
Para enviar ou receber mensagens, insira o nome do namespace e o nome da entidade que você criou. Em seguida, clique em **envie** ou **receber**.


> [!NOTE]
> - A identidade gerenciada funciona somente dentro do ambiente do Azure, em serviços de aplicativos, VMs do Azure e nos conjuntos de dimensionamento. Para aplicativos .NET, a biblioteca Microsoft.Azure.Services.AppAuthentication, que é usada pelo pacote NuGet do Barramento de Serviço, fornece uma abstração sobre esse protocolo e dá suporte a uma experiência de desenvolvimento local. Essa biblioteca também permite testar o código localmente no computador de desenvolvimento, usando a conta de usuário do Visual Studio, a CLI 2.0 do Azure ou a Autenticação Integrada do Active Directory. Para obter mais informações sobre as opções de desenvolvimento local com essa biblioteca, consulte [Autenticação de serviço a serviço para o Azure Key Vault usando .NET](../key-vault/service-to-service-authentication.md).  
> 
> - Atualmente, as identidades gerenciadas não funcionam com slots de implantação do Serviço de Aplicativo.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre as mensagens do Barramento de Serviço, consulte os seguintes tópicos:

* [Filas, tópicos e assinaturas do Barramento de Serviço](service-bus-queues-topics-subscriptions.md)
* [Introdução às filas do Barramento de Serviço](service-bus-dotnet-get-started-with-queues.md)
* [Como usar tópicos e assinaturas do Barramento de Serviço](service-bus-dotnet-how-to-use-topics-subscriptions.md)
