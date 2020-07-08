---
title: Integre sua oferta de marketplace comercial da Microsoft ao Azure Active Directory
description: Use o Azure Active Directory para autenticar suas ofertas do Microsoft AppSource e do Azure Marketplace.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: dsindona
ms.openlocfilehash: e2fff55fd9161aa41411dcb1abbf8ae0c1bae49c
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2020
ms.locfileid: "85957827"
---
# <a name="integrate-your-commercial-marketplace-listing-with-azure-active-directory"></a>Integre sua listagem de marketplace comercial ao Azure Active Directory

 Este artigo explica os requisitos para integrar uma listagem ou oferta de marketplace comercial ao Azure AD (Azure Active Directory). O Azure AD é um serviço de identidade de nuvem que usa estruturas padrão do setor para permitir a autenticação com uma conta da Microsoft. [Saiba mais sobre o Azure Active Directory](https://azure.microsoft.com/services/active-directory).

## <a name="azure-ad-benefits"></a>Benefícios do AD do Azure

Os clientes do Microsoft AppSource e do Azure Marketplace usam experiências no produto para pesquisar catálogos de listagem da vitrine. Essas ações exigem que os clientes assinem o produto. A integração do Azure AD fornece os seguintes benefícios:

- Engajamento mais rápido e uma experiência otimizada para o cliente
- Logon único (SSO) para milhões de usuários corporativos
- Experiência de login consistente em todos os aplicativos publicados por diferentes parceiros
- Autenticação escalonável entre plataformas para dispositivos móveis e aplicativos de nuvem

## <a name="offers-that-require-azure-ad"></a>Ofertas que exigem o Azure AD

As várias [opções de listagem e tipos de ofertas](determine-your-listing-type.md) do marketplace comercial têm requisitos diferentes para a implementação do Azure AD. Confira a tabela a seguir para obter detalhes.

| Tipo de oferta    | SSO do Azure AD necessário para entrar em contato comigo?  | SSO do Azure AD necessário para a avaliação? | SSO do Azure AD necessário para o Test Drive?  | SSO do Azure AD necessário para Transact |
| :------------------- | :-------------------|:-------------------|:-------------------|:-------------------|
| Máquina Virtual | N/D | Não | Não | Não |
| Aplicativos Azure (modelo de solução)  | N/D | N/D | N/D | N/D |
| Aplicativos gerenciados  | N/D | N/D | N/D | Não |
| SaaS  | Não | Sim | Sim | Sim |
| Contêineres  | N/D | N/D | N/D | Não |
| Serviços de Consultoria  | Não | N/D | N/D | N/D |

Para obter mais informações sobre os requisitos técnicos de SaaS, consulte [Guia de publicação de oferta de aplicativo SaaS](marketplace-saas-applications-technical-publishing-guide.md).

## <a name="azure-ad-integration"></a>Integração com o Azure AD

- Para obter informações sobre como habilitar o logon único integrando o Azure AD à sua listagem, consulte [Azure Active Directory para desenvolvedores](../active-directory/develop/index.yml).
- Para obter detalhes sobre o logon único do Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory?](../active-directory/manage-apps/what-is-single-sign-on.md).

## <a name="enable-a-trial-listing"></a>Habilitar uma listagem de avaliação

A configuração automatizada do cliente pode aumentar a probabilidade de conversão. Quando seu cliente seleciona sua listagem de avaliação e é redirecionado para seu ambiente de avaliação, você pode configurar o cliente diretamente, sem exigir etapas adicionais de login.

Durante a autenticação, o Azure AD envia um token para seu aplicativo ou oferta. As informações do usuário fornecidas pelo token permitem a criação de uma conta de usuário no seu aplicativo ou oferta. Para obter mais informações, consulte [tokens de exemplo](../active-directory/develop/id-tokens.md).

Quando você usa o Azure AD para habilitar a autenticação de um clique no seu aplicativo ou a listagem de avaliação, você:

- Simplificar a experiência do cliente do Marketplace para sua listagem de avaliação.
- Manter a percepção de uma experiência no produto mesmo quando o usuário é redirecionado do Marketplace para o seu domínio ou ambiente de avaliação.
- Reduza a probabilidade de abandono quando os usuários são redirecionados porque não há etapas adicionais de login.
- Reduza as barreiras de implantação para a grande população de usuários do Azure AD.

## <a name="verify-azure-ad-integration"></a>Verifique se a integração do Azure AD

### <a name="multitenant-solutions"></a>Soluções multi locatárias

Use o Azure AD para dar suporte as seguintes ações:

- Registre seu aplicativo em uma das vitrines do Marketplace. Modo de exibição [registro de aplicativo](../active-directory/develop/quickstart-register-app.md) ou [certificação do AppSource](../active-directory/azuread-dev/howto-get-appsource-certified.md) para obter mais informações.
- Habilite o recurso de suporte multilocatário no Microsoft Azure Active Directory para obter uma experiência de avaliação com um clique.

Se você for novo no usando o logon único federado do Azure AD, siga estas etapas:

1. Registre seu aplicativo no Marketplace.
1. Desenvolver SSO com o Azure AD por meio [OAuth 2.0](../active-directory/azuread-dev/v1-protocols-oauth-code.md) ou [OpenID Connect](../active-directory/azuread-dev/v1-protocols-openid-connect-code.md).
1. Habilite o recurso de suporte multilocação no Azure AD para fornecer uma experiência de avaliação com um clique.

### <a name="single-tenant-solutions"></a>Soluções de inquilino único

Use o Azure AD para oferecer suporte a uma das seguintes ações:

- Adicionar usuários convidados ao seu diretório por meio do [Azure AD B2B](../active-directory/b2b/what-is-b2b.md).
- Configurar manualmente avaliações para clientes usando o **entre em contato comigo** opção de publicação.
- Desenvolva um test drive por cliente.
- Crie um aplicativo de demonstração de exemplo multilocatário que usa SSO.

## <a name="next-steps"></a>Próximas etapas

Caso ainda não tenha feito isso, 

- [Saiba mais](https://azuremarketplace.microsoft.com/sell) sobre o marketplace.

Para se registrar na Central de Parceiros, comece criando uma nova oferta ou trabalhando em uma existente:

- [Entre na Central de Parceiros](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) para criar ou concluir sua oferta.
