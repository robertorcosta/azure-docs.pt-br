---
title: Identidades gerenciadas no serviço de Signaler do Azure
description: Saiba como as identidades gerenciadas funcionam no serviço do Azure Signalr e como usar uma identidade gerenciada em cenários sem servidor.
author: chenyl
ms.service: signalr
ms.topic: article
ms.date: 06/8/2020
ms.author: chenyl
ms.openlocfilehash: abe7503e7eb73d533ae901af21de001960173fb0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85559411"
---
# <a name="managed-identities-for-azure-signalr-service"></a>Identidades gerenciadas para o serviço de Signaler do Azure

Este artigo mostra como criar uma identidade gerenciada para o serviço de sinalizador do Azure e como usá-lo em cenários sem servidor.

> [!Important] 
> O serviço de Signaler do Azure pode dar suporte a apenas uma identidade gerenciada. Isso significa que você pode adicionar uma identidade atribuída pelo sistema ou uma identidade atribuída pelo usuário. 

## <a name="add-a-system-assigned-identity"></a>Adicionar uma identidade atribuída pelo sistema

Para configurar uma identidade gerenciada no portal do Azure, primeiro você criará uma instância do serviço de Signaler do Azure e, em seguida, habilitará o recurso.

1. Crie uma instância do serviço de Signaler do Azure no portal como faria normalmente. Navegue até ele no Portal.

2. Selecionar **Identidade**.

4. Na guia **atribuído pelo sistema** , alterne o **status** para **ativado**. Clique em **Salvar**.

    :::image type="content" source="media/signalr-howto-use-managed-identity/system-identity-portal.png" alt-text="Adicionar uma identidade atribuída pelo sistema no portal":::

## <a name="add-a-user-assigned-identity"></a>Adicionar uma identidade atribuída pelo usuário

A criação de uma instância do serviço de Signaler do Azure com uma identidade atribuída pelo usuário requer que você crie a identidade e, em seguida, adicione seu identificador de recurso ao serviço.

1. Crie um recurso de identidade gerenciado atribuído pelo usuário de acordo com [estas instruções](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity).

2. Crie uma instância do serviço de Signaler do Azure no portal como faria normalmente. Navegue até ele no Portal.

3. Selecionar **Identidade**.

4. Na guia **atribuído pelo usuário** , selecione **Adicionar**.

5. Pesquise a identidade que você criou anteriormente e selecione-a. Selecione **Adicionar**.

    :::image type="content" source="media/signalr-howto-use-managed-identity/user-identity-portal.png" alt-text="Adicionar uma identidade atribuída pelo sistema no portal":::

## <a name="use-a-managed-identity-in-serverless-scenarios"></a>Usar uma identidade gerenciada em cenários sem servidor

O serviço de Signaler do Azure é um serviço totalmente gerenciado, portanto, você não pode usar uma identidade gerenciada para obter tokens manualmente. Em vez disso, o serviço de sinalizador do Azure usa a identidade gerenciada que você definiu para obter um token de acesso. Em seguida, o serviço define o token de acesso em um `Authorization` cabeçalho em uma solicitação upstream em cenários sem servidor.

### <a name="enable-managed-identity-authentication-in-upstream-settings"></a>Habilitar a autenticação de identidade gerenciada em configurações de upstream

1. Adicione uma identidade atribuída pelo sistema ou uma identidade atribuída pelo usuário.

2. Defina as configurações de upstream e use **ManagedIdentity** como as configurações de **autenticação** . Para saber como criar configurações de upstream com autenticação, consulte [configurações de upstream](concept-upstream.md).

3. Nas configurações de autenticação de identidade gerenciadas, para **recurso**, você pode especificar o recurso de destino. O recurso se tornará uma `aud` declaração no token de acesso obtido, que pode ser usado como parte da validação em seus pontos de extremidade upstream. O recurso pode ser um dos seguintes:
    - Vazio
    - ID do aplicativo (cliente) da entidade de serviço
    - URI da ID do aplicativo da entidade de serviço
    - [ID de recurso de um serviço do Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities#azure-services-that-support-azure-ad-authentication)

    > [!NOTE]
    > Se você validar um token de acesso por conta própria em seu serviço, poderá escolher qualquer um dos formatos de recurso. Apenas certifique-se de que o valor do **recurso** nas configurações de **autenticação** e a validação são consistentes. Se você usar o RBAC (controle de acesso baseado em função) para um plano de dados, deverá usar o recurso que o provedor de serviços solicitar.

### <a name="validate-access-tokens"></a>Validar tokens de acesso

O token no `Authorization` cabeçalho é um [token de acesso da plataforma de identidade da Microsoft](https://docs.microsoft.com/azure/active-directory/develop/access-tokens#validating-tokens).

Para validar tokens de acesso, seu aplicativo também deve validar o público e os tokens de assinatura. Estes precisam ser validados em relação aos valores no documento de descoberta OpenID. Por exemplo, consulte a [versão independente de locatário do documento](https://login.microsoftonline.com/common/.well-known/openid-configuration).

O middleware Azure Active Directory (Azure AD) tem recursos internos para validar tokens de acesso. Você pode navegar pelos nossos [exemplos](https://docs.microsoft.com/azure/active-directory/develop/sample-v2-code) para encontrar um no idioma de sua escolha.

Fornecemos bibliotecas e exemplos de código que mostram como manipular a validação de token. Há também várias bibliotecas de parceiros de software livre disponíveis para validação JWT (JSON Web token). Há pelo menos uma opção para quase todas as plataformas e idiomas. Para obter mais informações sobre as bibliotecas de autenticação do Azure AD e exemplos de código, consulte [bibliotecas de autenticação da plataforma de identidade da Microsoft](https://docs.microsoft.com/azure/active-directory/develop/reference-v2-libraries).

## <a name="next-steps"></a>Próximas etapas

- [Desenvolvimento de funções do Azure e a configuração com o serviço do Azure SignalR](signalr-concept-serverless-development-config.md)
