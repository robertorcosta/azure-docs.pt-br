---
title: Guia de publicação de aplicativos SaaS - Microsoft Commercial Marketplace
description: Os requisitos e recursos para ofertas de publicação de aplicativos SaaS ao Microsoft AppSource e ao Azure Marketplace.
services: Marketplace, Compute, Storage, Networking, Blockchain, Security, SaaS
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/23/2020
ms.author: mingshen
author: mingshen-ms
ms.openlocfilehash: 9f488675e4f2437982e2426da73b4db1a713905d
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88607297"
---
# <a name="saas-applications-offer-publishing-guide"></a>Guia de publicação da oferta de aplicativos SaaS

Publique aplicativos SaaS no marketplace comercial com três diferentes chamadas à ação: "Entre em contato", "Experimente agora" e "Obtenha agora". Este artigo explica essas três opções, incluindo os requisitos de cada uma. 

## <a name="offer-overview"></a>Visão geral da oferta  

Os aplicativos SaaS estão disponíveis no Microsoft AppSource e no Azure Marketplace.  Os dois repositórios online dão suporte à lista, avaliação e ofertas do Transact.

**Lista:**  a opção de publicação em lista é uma oferta do tipo "Entre em contato" usada quando a participação no nível Avaliação ou Transação não é viável. O benefício dessa abordagem é permitir que os editores com uma solução no mercado comecem imediatamente a receber clientes potenciais que possam ser convertidos em ofertas para impulsionar seu negócio.  
**Avaliação/Transação:**  o cliente tem a opção de comprar diretamente ou solicitar uma avaliação de sua solução. Fornecer uma experiência de avaliação aumenta o nível de engajamento oferecido aos clientes e permite explorar a solução antes da compra. Com uma experiência de avaliação, você terá melhores chances de promoção nas lojas online, e você deve esperar mais clientes potenciais e mais ricos dos compromissos do cliente. As Avaliações devem incluir suporte gratuito pelo menos durante o período de avaliação.  

| Oferta de aplicativos SaaS | Requisitos de negócios | Requisitos técnicos |  
| --- | --- | --- |  
| **Fale conosco** | Sim | Não |  
| **Power BI / Dynamics** | Sim | Sim (Integração com o Azure AD) |  
| **Aplicativos SaaS**| Sim | Sim (Integração com o Azure AD) |     

## <a name="saas-list"></a>Lista de SaaS

A chamada à ação para uma listagem de SaaS sem versão de avaliação e sem nenhuma funcionalidade de cobrança é "Entre em contato". 

Você não precisa configurar o Azure Active Directory para listar um aplicativo SaaS. 

|Requisitos  |Detalhes  |
|---------|---------|
|Seu aplicativo é uma oferta de SaaS  |   A solução é uma oferta de SaaS e você oferece um produto de SaaS multilocatário.      |


## <a name="saas-trial"></a>Avaliação de SaaS

Forneça uma solução ou um aplicativo usando uma avaliação baseada em SaaS (software como serviço) pronta para ser testada. Ofertas de avaliação gratuita podem ser apresentadas como uma conta de uso limitado ou de avaliação de duração limitada. 


|Requisitos  |Detalhes  |
|---------|---------|
|Seu aplicativo é uma oferta de SaaS  |   A solução é uma oferta de SaaS e você oferece um produto de SaaS multilocatário.      |
|Seu aplicativo está habilitado para AAD     |   O cliente será redirecionado para seu domínio e você negociará diretamente com o cliente       |


## <a name="saas-trial-technical-requirements"></a>Requisitos técnicos da avaliação de SaaS

Os requisitos técnicos para aplicativos SaaS são simples. Os editores apenas precisam estar integrados ao Azure Active Directory (Azure AD) para serem publicados. A integração do Microsoft Azure AD com aplicativos está bem documentada e a Microsoft fornece vários SDKs e recursos para realizar isso.  

Para começar, é recomendável que você tenha uma assinatura dedicada à publicação do Azure Marketplace, o que permite isolar o trabalho de outras iniciativas. Depois disso, você pode começar a implantar o aplicativo SaaS nessa assinatura para iniciar o trabalho de desenvolvimento.  

As melhores documentações, exemplos e diretrizes do Azure Active Directory estão nestes sites: 

* [Guia do desenvolvedor do Active Directory do Azure](../active-directory/develop/index.yml)

* [Integração com Azure Active Directory](../active-directory/develop/active-directory-how-to-integrate.md)

* [Roteiro do Azure - Segurança e Identidade](https://azure.microsoft.com/roadmap/?category=security-identity)

Para tutoriais em vídeo, examine o seguinte:

* [ Autenticação do Azure Active Directory com Vittorio Bertocci](https://channel9.msdn.com/Shows/XamarinShow/Episode-27-Azure-Active-Directory-Authentication-with-Vittorio-Bertocci?term=azure%20active%20directory%20integration)

* [Resumo técnico de Identidade do Azure Active Directory - Parte 1 de 2](https://channel9.msdn.com/Blogs/MVP-Enterprise-Mobility/Azure-Active-Directory-Identity-Technical-Briefing-Part-1-of-2?term=azure%20active%20directory%20integration)

* [Resumo técnico de Identidade do Azure Active Directory - Parte 2 de 2](https://channel9.msdn.com/Blogs/MVP-Azure/Azure-Active-Directory-Identity-Technical-Briefing-Part-2-of-2?term=azure%20active%20directory%20integration)

* [Compilar aplicativos com Microsoft Azure Active Directory](https://channel9.msdn.com/Blogs/Windows-Development-for-the-Enterprise/Building-Apps-with-Microsoft-Azure-Active-Directory?term=azure%20active%20directory%20integration)

* [Vídeos do Microsoft Azure focados no Active Directory](https://azure.microsoft.com/resources/videos/index/?services=active-directory)

Um treinamento gratuito do Azure Active Directory está disponível em  
* [Série de Conteúdo do Microsoft Azure para Profissionais de TI: Azure Active Directory](https://mva.microsoft.com/training-courses/microsoft-azure-for-it-pros-content-series-azure-active-directory-16754?l=N0e23wtxC_2106218965)

Além disso, o Azure Active Directory fornece um site para verificar se há atualizações de serviço   
* [Atualizações de serviço do Microsoft Azure AD](https://azure.microsoft.com/updates/?product=active-directory)

## <a name="using-azure-active-directory-to-enable-trials"></a>Usar o Azure Active Directory para habilitar avaliações  

A Microsoft autentica todos os usuários do Marketplace com o Azure AD. Portanto, quando um usuário autenticado clicar na sua listagem de Avaliação no Marketplace e for redirecionado para seu ambiente de Avaliação, você poderá provisionar o usuário diretamente em uma Avaliação sem exigir uma etapa adicional de entrada. O token que seu aplicativo recebe do Azure AD durante a autenticação inclui informações valiosas do usuário que podem ser usadas para criar uma conta para ele no seu aplicativo, permitindo que você automatize a experiência de provisionamento e aumente a probabilidade de conversão. Para obter mais informações sobre o token, confira [Tokens de exemplo](../active-directory/develop/active-directory-token-and-claims.md).

Usar o Azure AD para habilitar a autenticação em 1 clique no aplicativo ou avaliação:  
* Simplifica a experiência do cliente do Marketplace para Avaliação.  
* Mantém a percepção de uma "experiência no produto", mesmo quando o usuário é redirecionado do Marketplace para o seu domínio ou ambiente de Avaliação.  
* Diminui a probabilidade de abandono no redirecionamento porque não há uma etapa de entrada adicional.  
* Reduz barreiras de implantação para a grande população de usuários do Microsoft Azure AD.  

## <a name="certifying-your-azure-ad-integration-for-marketplace"></a>Certificar a integração do Azure AD para Marketplace  

Certifique a integração do Azure AD de maneiras diferentes, de acordo com o tipo do aplicativo, locatário único ou multilocatário, e se você for novato no SSO (logon único) federado do Azure AD ou já oferecer suporte para isso.  

**Para aplicativos multilocatários:**  

Se você já dá suporte ao Azure AD, faça o seguinte:
1.    Registre seu aplicativo no Portal do Azure
2.    Habilite o recurso de suporte multilocatário no Azure AD para obter uma experiência de avaliação com um clique. Encontre mais informações específicas [aqui](../active-directory/develop/active-directory-integrating-applications.md).  

Se você é novo no SSO Federado do Azure AD, faça o seguinte: 
1.  Registre seu aplicativo no Portal do Azure
2.  Desenvolva SSO com o Azure AD usando [OpenID Connect](../active-directory/develop/active-directory-protocols-openid-connect-code.md) ou [OAuth 2.0](../active-directory/develop/active-directory-protocols-oauth-code.md).
3.  Habilite o recurso de suporte multilocatário no AAD para obter uma experiência de avaliação com um clique Encontre mais informações específicas [aqui](../active-directory/develop/active-directory-devhowto-appsource-certified.md).  

**Para o aplicativo de locatário único, use qualquer uma das seguintes opções:**  
* Adicionar usuários ao seu diretório como usuários convidados usando [B2B do Azure](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md)
* Provisionar manualmente avaliações para clientes por meio do "Entre em contato comigo"
* Desenvolver um test drive por cliente
* Compile um aplicativo de demonstração de amostra multilocatário com SSO

## <a name="saas-subscriptions"></a>Assinaturas de SaaS

Use o tipo de oferta de aplicativo SaaS para permitir que o cliente compre a solução técnica baseada em SaaS como uma assinatura. Os requisitos a seguir devem ser atendidos em relação ao aplicativo SaaS:
- Precifica e cobra o serviço a uma taxa fixa (mensal ou anual) ou por usuário.
- Fornece um método para atualizar ou cancelar o serviço a qualquer momento.
A Microsoft hospeda a transação comercial. A Microsoft cobra o cliente em seu nome. Para oferecer um aplicativo SaaS como uma assinatura, integre-se às APIs de cumprimento de SaaS.  O serviço deve oferecer suporte ao provisionamento, à atualização e ao cancelamento.

| Requisito | Detalhes |  
|:--- |:--- |  
|Cobrança e medição | A oferta é cobrada com base no modelo de preços selecionado antes da publicação (taxa fixa ou por usuário).  Se estiver usando o modelo de taxa fixa, você poderá, opcionalmente, incluir mais dimensões usadas para cobrar os clientes pelo uso não incluído na taxa fixa. |  
|Cancelamento | Sua oferta é cancelável pelo cliente a qualquer momento. |  
|Página de destino da transação | Você hospeda uma página inicial da transação de associação de marcas do Azure na qual os usuários podem criar e gerenciar suas contas de serviço de SaaS. |   
| API de assinatura | Você expõe um serviço que pode interagir com a Assinatura de SaaS para criar, atualizar e excluir um plano de serviço e uma conta de usuário. As alterações críticas na API devem ter suporte em até 24 horas. As alterações não críticas de API serão lançadas periodicamente. |  

>[!Note]
>A aceitação do canal de parceiros do Cloud Solution Providers (CSP) já está disponível.  Consulte [Cloud Solution Providers](./cloud-solution-providers.md) para obter mais informações sobre como comercializar a oferta por meio dos canais de parceiro do Microsoft CSP.

## <a name="next-steps"></a>Próximas etapas
Caso ainda não tenha feito isso,

* [Saiba mais](https://azuremarketplace.microsoft.com/sell) sobre o marketplace.

Para se registrar na Central de Parceiros, comece criando uma nova oferta ou trabalhando em uma existente:

* [Entre na Central de Parceiros](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) para criar ou concluir a oferta.
* Consulte [Criar uma oferta de aplicativo SaaS](./partner-center-portal/create-new-saas-offer.md) para obter mais informações.
