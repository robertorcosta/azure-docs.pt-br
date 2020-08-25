---
title: Tutorial para configurar o Azure Active Directory B2C com o whoIam
titleSuffix: Azure AD B2C
description: Saiba como integrar a autenticação de Azure AD B2C com o whoIam para verificação de usuário
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/20/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 6545c5f40004dc50904618a8ea734eb73eeee933
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88817298"
---
# <a name="tutorial-for-configuring-whoiam-with-azure-active-directory-b2c"></a>Tutorial para configurar o WhoIAM com o Azure Active Directory B2C

Neste tutorial de exemplo, fornecemos orientações sobre como configurar o BRIMS (sistema de gerenciamento de identidades com marca [WhoIAM](https://www.whoiam.ai/brims/) ) no seu ambiente e integrá-lo com Azure ad B2C.

O BRIMS da WhoIAM é um conjunto de aplicativos e serviços que é implantado em seu ambiente. Ele fornece a verificação de voz, SMS e email da sua base de usuários. O BRIMS trabalha em conjunto com sua solução de gerenciamento de identidade e acesso existente e é independente da plataforma.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará de:

- Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).

- [Um locatário Azure ad B2C](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant) que está vinculado à sua assinatura do Azure.

- Uma [conta de avaliação](https://www.whoiam.ai/contact-us/) do WhoIAM

## <a name="scenario-description"></a>Descrição do cenário

A integração do WhoIAM inclui os seguintes componentes:

- Azure AD B2C locatário – o servidor de autorização, responsável por verificar as credenciais do usuário com base nas políticas personalizadas definidas no locatário. Ele também é conhecido como provedor de identidade.

- Um portal de administração para gerenciar clientes e suas configurações

- Um serviço de API que expõe vários recursos por meio de pontos de extremidade  

- Azure Cosmos DB que é usado como o back-end para o portal de administração do BRIMS e o serviço de API

O diagrama de arquitetura a seguir mostra a implementação.

![captura de tela para whoiam-arquitetura-diagrama](media/partner-whoiam/whoiam-architecture-diagram.png)

|Etapa | Descrição |
|:-----| :-----------|
| 1. | O usuário chega em uma página de logon. O usuário inicia uma solicitação de inscrição ou entrada para um aplicativo que usa Azure AD B2C como seu provedor de identidade.
| 2. | Como parte da autenticação, o usuário solicita a verificação da propriedade de seu email ou telefone ou usa sua voz como um fator de verificação biométrica.  
| 3. | Azure AD B2C faz uma chamada para o serviço de API BRIMS passando o endereço de email do usuário, o número de telefone e sua gravação de voz
| 4. | O BRIMS usa configurações predefinidas, como email totalmente personalizável e modelos de SMS para interagir com o usuário em sua respectiva linguagem, que é consistente com a aparência do aplicativo.
| 5. | Depois que a verificação de identidade de um usuário for concluída, BRIMS retornará um token para Azure AD B2C indicando o resultado da verificação. O Azure AD B2C concede ao usuário acesso ao aplicativo ou falha na tentativa de autenticação.  

## <a name="onboard-with-whoiam"></a>Integração com o WhoIAM

1. Entre em contato com o [WhoIAM](https://www.whoiam.ai/contact-us/) e crie uma conta do BRIMS.

2. Depois que uma conta for criada, use as diretrizes de integração disponibilizadas para você e configure os seguintes serviços do Azure:

    - [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) -usado para armazenamento seguro de senha, como senhas do serviço de email.

    - [Azure app serviço](https://azure.microsoft.com/services/app-service/) -usado para hospedar a API do BRIMS e os serviços do portal de administração

    - [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) -usado para autenticar usuários administrativos para o portal de administração

    - [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) -usado para armazenar e recuperar as configurações

    - [Application insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview#:~:text=Application%20Insights%2C%20a%20feature%20of%20Azure%20Monitor%2C%20is,professionals.%20Use%20it%20to%20monitor%20your%20live%20applications.) (opcional) – usado para fazer logon no portal de administração e API

3. Implante a API do BRIMS e o portal de administração do BRIMS em seu ambiente do Azure.

4. Azure AD B2C exemplos de política personalizada estão disponíveis na documentação de integração do BRIMS. Siga o documento para configurar seu aplicativo e use a plataforma BRIMS para verificação de identidade do usuário.  

Para obter mais informações sobre o BRIMS da WhoIAM, consulte a [documentação do produto](https://www.whoiam.ai/brims/)

## <a name="test-the-user-flow"></a>Testar o fluxo de usuário

1. Abra o locatário Azure AD B2C e, em políticas, selecione **estrutura de experiência de identidade**.

2. Selecione o **SignUpSignIn**criado anteriormente.

3. Selecione **executar fluxo de usuário** e selecione as configurações:

   a. **Aplicativo**: selecione o aplicativo registrado (exemplo é JWT)

   b. **URL de resposta**: selecione a **URL de redirecionamento**

   c. Selecione **Executar fluxo de usuário**.

4. Percorrer o fluxo de inscrição e criar uma conta

5. O serviço BRIMS será chamado durante o fluxo, depois que o atributo de usuário for criado. Se o fluxo estiver incompleto, verifique se o usuário não está salvo no diretório.

## <a name="next-steps"></a>Próximas etapas

Para obter informações adicionais, examine os seguintes artigos:

- [Políticas personalizadas no Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-overview)

- [Introdução às políticas personalizadas no Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications)
