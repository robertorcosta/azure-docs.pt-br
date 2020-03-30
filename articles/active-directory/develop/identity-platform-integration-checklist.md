---
title: Melhores práticas para a plataforma de identidade Microsoft | Azure
description: Conheça as melhores práticas, recomendações e descuidos comuns ao se integrar à plataforma de identidade da Microsoft.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/11/2019
ms.author: ryanwi
ms.reviewer: lenalepa, sureshja, jesakowi
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: 56975cebbfe4f6dd6452c850c338d431faea27bb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050488"
---
# <a name="microsoft-identity-platform-best-practices-and-recommendations"></a>Práticas e recomendações recomendadas da plataforma de identidade microsoft

Este artigo destaca as melhores práticas, recomendações e descuidos comuns ao se integrar à plataforma de identidade microsoft.  Esta lista de verificação irá guiá-lo para uma integração de alta qualidade e segura. Revise esta lista regularmente para garantir que você mantenha a qualidade e a segurança da integração do seu aplicativo com a plataforma de identidade. A lista de verificação não tem a intenção de revisar toda a sua aplicação. O conteúdo da lista de verificação está sujeito a alterações à medida que fazemos melhorias na plataforma.

Se você está apenas começando, confira a [documentação](index.yml) da plataforma de identidade da Microsoft para saber mais sobre os fundamentos de autenticação, cenários de aplicativos na plataforma de identidade da Microsoft e muito mais.

Use a lista de verificação a seguir para garantir que seu aplicativo esteja efetivamente integrado à [plataforma de identidade Microsoft](https://docs.microsoft.com/azure/active-directory/develop/).

## <a name="basics"></a>Noções básicas

|   |   |
|---|---|
| ![caixa de seleção](./media/active-directory-integration-checklist/checkbox-two.svg) | Leia e entenda as [Políticas da Plataforma Microsoft.](https://go.microsoft.com/fwlink/?linkid=2090497&clcid=0x409) Certifique-se de que seu aplicativo adere aos termos descritos à medida que eles são projetados para proteger os usuários e a plataforma. |

## <a name="ownership"></a>Propriedade

|   |   |
|---|---|
| ![caixa de seleção](./media/active-directory-integration-checklist/checkbox-two.svg) | Certifique-se de que as informações associadas à conta que você usou para registrar e gerenciar aplicativos estão atualizadas. |

## <a name="branding"></a>Identidade visual

|   |   |
|---|---|
| ![caixa de seleção](./media/active-directory-integration-checklist/checkbox-two.svg) | Adere às [diretrizes de branding para aplicações](howto-add-branding-in-azure-ad-apps.md). |
| ![caixa de seleção](./media/active-directory-integration-checklist/checkbox-two.svg) | Forneça um nome e logotipo significativos para sua aplicação. Essas informações aparecem no [pedido de consentimento do](application-consent-experience.md)aplicativo . Certifique-se de que seu nome e logotipo sejam representativos da sua empresa/produto para que os usuários possam tomar decisões informadas. Certifique-se de que você não está violando nenhuma marca registrada. |

## <a name="privacy"></a>Privacidade

|   |   |
|---|---|
| ![caixa de seleção](./media/active-directory-integration-checklist/checkbox-two.svg) | Forneça links para os termos de serviço e declaração de privacidade do seu aplicativo. |

## <a name="security"></a>Segurança

|   |   |
|---|---|
| ![caixa de seleção](./media/active-directory-integration-checklist/checkbox-two.svg) | Gerencie suas URIs de redirecionamento: <ul><li>Mantenha a propriedade de todas as SUAS URIs redirecionadas e mantenha os registros de DNS para eles atualizados.</li><li>Não use curingas (*) em suas URIs.</li><li>Para aplicativos web, certifique-se de que todas as URIs estão seguras e criptografadas (por exemplo, usando esquemas https).</li><li>Para clientes públicos, use URIs de redirecionamento específicos da plataforma, se aplicável (principalmente para iOS e Android). Caso contrário, use URIs redirecionamento com uma alta quantidade de aleatoriedade para evitar colisões ao ligar de volta para o seu aplicativo.</li><li>Se o seu aplicativo estiver sendo usado a `https://login.microsoftonline.com/common/oauth2/nativeclient`partir de um agente web isolado, você pode usar .</li><li>Revise e corte todas as URIs não utilizadas ou desnecessárias de forma regular.</li></ul> |
| ![caixa de seleção](./media/active-directory-integration-checklist/checkbox-two.svg) | Se o seu aplicativo estiver registrado em um diretório, minimize e monitore manualmente a lista de proprietários de registros de aplicativos. |
| ![caixa de seleção](./media/active-directory-integration-checklist/checkbox-two.svg) | Não habilite o suporte para o [fluxo de subvenção implícita OAuth2,](v2-oauth2-implicit-grant-flow.md) a menos que explicitamente necessário. Conheça o cenário válido [aqui.](v2-oauth2-implicit-grant-flow.md#suitable-scenarios-for-the-oauth2-implicit-grant) |
| ![caixa de seleção](./media/active-directory-integration-checklist/checkbox-two.svg) | Mova-se para além do nome de usuário/senha. Não use o [fluxo de credencial de senha do proprietário de recursos (ROPC),](v2-oauth-ropc.md)que lida diretamente com as senhas dos usuários. Esse fluxo requer um alto grau de confiança e exposição do usuário e só deve ser usado quando outros fluxos mais seguros não puderem ser usados. Esse fluxo ainda é necessário em alguns cenários (como DevOps), mas lembre-se de que seu uso imporá restrições ao aplicativo.  Para abordagens mais modernas, leia [fluxos de autenticação e cenários de aplicativos.](authentication-flows-app-scenarios.md)|
| ![caixa de seleção](./media/active-directory-integration-checklist/checkbox-two.svg) | Proteja e gerencie suas credenciais confidenciais de aplicativos para aplicativos web, APIs da Web e aplicativos daemon. Use [credenciais de certificado,](active-directory-certificate-credentials.md)não credenciais de senha (segredos do cliente). Se você deve usar uma credencial de senha, não configure-a manualmente. Não armazene credenciais em código ou config, e nunca permita que sejam manuseadas por humanos. Se possível, use [identidades gerenciadas para recursos do Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) ou [do Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) para armazenar e girar regularmente suas credenciais. |
| ![caixa de seleção](./media/active-directory-integration-checklist/checkbox-two.svg) | Certifique-se de que seu aplicativo solicite as permissões de menor privilégio. Apenas peça permissões que seu aplicativo absolutamente precisa, e somente quando você precisar delas. Entenda os [diferentes tipos de permissões.](v2-permissions-and-consent.md#permission-types) Use apenas permissões de aplicativo, se necessário; usar permissões delegadas sempre que possível. Para obter uma lista completa de permissões do Microsoft Graph, consulte esta [referência de permissões](https://docs.microsoft.com/graph/permissions-reference). |
| ![caixa de seleção](./media/active-directory-integration-checklist/checkbox-two.svg) | Se você está protegendo uma API usando a plataforma de identidade microsoft, pense cuidadosamente através das permissões que ela deve expor. Considere qual é a granularidade certa para sua solução e quais permissões requerem consentimento do admin. Verifique se há permissões esperadas nos tokens de entrada antes de tomar quaisquer decisões de autorização. |

## <a name="implementation"></a>Implementação

|   |   |
|---|---|
| ![caixa de seleção](./media/active-directory-integration-checklist/checkbox-two.svg) | Use soluções modernas de autenticação (OAuth 2.0, [OpenID Connect](v2-protocols-oidc.md)) para fazer login com segurança nos usuários. |
| ![caixa de seleção](./media/active-directory-integration-checklist/checkbox-two.svg) |  Não programe diretamente contra protocolos como OAuth 2.0 e Open ID. Em vez disso, aproveite a [Biblioteca de Autenticação microsoft (MSAL)](msal-overview.md). As bibliotecas MSAL envolvem com segurança protocolos de segurança em uma biblioteca fácil de usar e você recebe suporte interno para cenários de [Acesso Condicional,](/azure/active-directory/conditional-access/overview) [soo de logon único em](/azure/active-directory/manage-apps/what-is-single-sign-on)todo o dispositivo e suporte de cache de token embutido. Para obter mais informações, consulte a lista de bibliotecas de [clientes](reference-v2-libraries.md#microsoft-supported-client-libraries) e [bibliotecas de middleware](reference-v2-libraries.md#microsoft-supported-server-middleware-libraries) suportadas pela Microsoft e a lista de [bibliotecas de clientes de terceiros compatíveis](reference-v2-libraries.md#compatible-client-libraries).<br/><br/>Se você deve entregar código para os protocolos de autenticação, você deve seguir uma metodologia como [o Microsoft SDL](https://www.microsoft.com/sdl/default.aspx). Preste muita atenção às considerações de segurança nas especificações das normas para cada protocolo.|
| ![caixa de seleção](./media/active-directory-integration-checklist/checkbox-two.svg) |  Migre os aplicativos existentes da [Azure Active Directory Authentication Library (ADAL)](../azuread-dev/active-directory-authentication-libraries.md) para a [Microsoft Authentication Library](msal-overview.md). A MSAL é a mais recente solução de plataforma de identidade da Microsoft e é preferida da ADAL. Está disponível em .NET, JavaScript, Android, iOS, macOS e também está em pré-visualização pública para Python e Java. Leia mais sobre [migração de ADAL.NET,](msal-net-migration.md) [ADAL.js](msal-compare-msal-js-and-adal-js.md)e aplicativos [de ADAL.NET e corretores iOS.](msal-net-migration-ios-broker.md)|
| ![caixa de seleção](./media/active-directory-integration-checklist/checkbox-two.svg) |  Para aplicativos móveis, configure cada plataforma usando a experiência de registro do aplicativo. Para que seu aplicativo aproveite o Microsoft Authenticator ou o Microsoft Company Portal para login único, seu aplicativo precisa de um "URI de redirecionamento de corretor" configurado. Isso permite que a Microsoft retorne o controle ao seu aplicativo após a autenticação. Ao configurar cada plataforma, a experiência de registro do aplicativo irá guiá-lo durante o processo. Use o quickstart para baixar um exemplo de trabalho. No iOS, use corretores e webview do sistema sempre que possível.|
| ![caixa de seleção](./media/active-directory-integration-checklist/checkbox-two.svg) |  Em aplicativos web ou APIs da Web, mantenha um cache de token por conta.  Para aplicativos web, o cache de token deve ser fornecido pelo ID da conta.  Para APIs da Web, a conta deve ser chaveada pelo hash do token usado para chamar a API. MSAL.NET fornece serialização de cache de token personalizada nas subplataformas .NET Framework e .NET Core. Por razões de segurança e desempenho, nossa recomendação é serializar um cache por usuário. Para obter mais informações, leia sobre [a serialização do cache de tokens](msal-net-token-cache-serialization.md#token-cache-for-a-web-app-confidential-client-application).|
| ![caixa de seleção](./media/active-directory-integration-checklist/checkbox-two.svg) | Se os dados necessários pelo aplicativo forem disponibilizados através [do Microsoft Graph,](https://developer.microsoft.com/graph)solicite permissões para esses dados usando o ponto final do Microsoft Graph em vez da API individual. |
| ![caixa de seleção](./media/active-directory-integration-checklist/checkbox-two.svg) |Não olhe para o valor do token de acesso, ou tente analisá-lo como um cliente.  Eles podem alterar valores, formatos ou até mesmo ficar criptografados sem aviso - use sempre o id_token se seu cliente precisar aprender algo sobre o usuário ou ligar para o Microsoft Graph.  Apenas as APIs da Web devem analisar os tokens de acesso (já que são eles que definem o formato e a definição das chaves de criptografia). |

## <a name="end-user-experience"></a>Experiência do usuário final

|   |   |
|---|---|
| ![caixa de seleção](./media/active-directory-integration-checklist/checkbox-two.svg) | [Entenda a experiência](application-consent-experience.md) de consentimento e configure as peças do prompt de consentimento do seu aplicativo para que os usuários finais e administradores tenham informações suficientes para determinar se confiam no seu aplicativo. |
| ![caixa de seleção](./media/active-directory-integration-checklist/checkbox-two.svg) | Minimize o número de vezes que um usuário precisa inserir credenciais de login ao usar seu aplicativo, tentando autenticação silenciosa (aquisição silenciosa de tokens) antes de fluxos interativos. |
| ![caixa de seleção](./media/active-directory-integration-checklist/checkbox-two.svg) | Não use "prompt=consent" para cada login. Use apenas prompt=consent se você tiver determinado que você precisa pedir consentimento para permissões adicionais (por exemplo, se você mudou as permissões necessárias do seu aplicativo). |
| ![caixa de seleção](./media/active-directory-integration-checklist/checkbox-two.svg) | Quando aplicável, enriqueça seu aplicativo com dados do usuário. Usar a [API do Microsoft Graph](https://developer.microsoft.com/graph) é uma maneira fácil de fazer isso. A ferramenta [Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) que pode ajudá-lo a começar. |
| ![caixa de seleção](./media/active-directory-integration-checklist/checkbox-two.svg) | Registre o conjunto completo de permissões que seu aplicativo requer para que os admins possam conceder consentimento facilmente ao seu inquilino. Use [o consentimento incremental](../azuread-dev/azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent) em tempo de execução para ajudar os usuários a entender por que seu aplicativo está solicitando permissões que podem preocupar ou confundir os usuários quando solicitado na primeira partida. |
| ![caixa de seleção](./media/active-directory-integration-checklist/checkbox-two.svg) | Implemente uma [experiência de saída única limpa.](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-6-SignOut) É um requisito de privacidade e segurança, e torna uma boa experiência de usuário. |

## <a name="testing"></a>Testes

|   |   |
|---|---|
| ![caixa de seleção](./media/active-directory-integration-checklist/checkbox-two.svg) | Teste para [políticas de acesso condicional](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-6-SignOut) que podem afetar a capacidade de seus usuários de usar seu aplicativo. |
| ![caixa de seleção](./media/active-directory-integration-checklist/checkbox-two.svg) | Teste seu aplicativo com todas as contas possíveis que você planeja apoiar (por exemplo, contas de trabalho ou escola, contas pessoais da Microsoft, contas de filho e contas soberanas). |

## <a name="additional-resources"></a>Recursos adicionais

Explore informações detalhadas sobre a v2.0:

* [Plataforma de identidade Microsoft (visão geral v2.0)](v2-overview.md)
* [Referência de protocolos de plataforma de identidade da Microsoft](active-directory-v2-protocols.md)
* [Referência de tokens de acesso](access-tokens.md)
* [Referência de tokens de ID](id-tokens.md)
* [Referência de bibliotecas de autenticação](reference-v2-libraries.md)
* [Permissões e consentimento na plataforma de identidade da Microsoft](v2-permissions-and-consent.md)
* [API do Microsoft Graph](https://developer.microsoft.com/graph)
