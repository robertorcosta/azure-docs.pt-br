---
title: Campos de registro do portal Azure para aplicativos desenvolvidos sob medida
description: Orientação para o registro de um aplicativo desenvolvido sob medida com o Azure AD
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: ryanwi
ms.openlocfilehash: c44575ca43063388d5c65855542cf15700d2cb5a
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80883161"
---
# <a name="azure-portal-registration-fields-for-custom-developed-apps"></a>Campos de registro do portal Azure para aplicativos desenvolvidos sob medida

Este artigo oferece uma breve descrição de todos os campos disponíveis no formulário de registro do aplicativo no [portal do Azure](https://portal.azure.com).

## <a name="register-a-new-application"></a>Registrar um novo aplicativo

-   Para registrar um novo aplicativo, navegue até o [portal do Azure](https://portal.azure.com).

-   Do painel de navegação esquerdo, clique **em Azure Active Directory.**

-   Escolha **Registros do aplicativo** e clique em **Adicionar**.

-   Isso abre o formulário de registro do aplicativo.

## <a name="fields-in-the-application-registration-form"></a>Campos do formulário de registro do aplicativo

| Campo            | Descrição                                                                              |
|------------------|------------------------------------------------------------------------------------------|
| Nome             | O nome do aplicativo. Ele deve ter um mínimo de quatro caracteres.                |
| Tipos de conta com suporte| Selecione quais contas você gostaria que seu aplicativo suportasse: contas somente neste diretório organizacional, contas em qualquer diretório organizacional ou contas em qualquer diretório organizacional e contas pessoais da Microsoft.  |
| Redirecionar uri (opcional) | Selecione o tipo de aplicativo que você está construindo, **cliente Web** ou **Público (mobile & desktop)** e, em seguida, digite o URI (ou URL de resposta) redirecionamento para o seu aplicativo. Para aplicativos Web, informe a URL base do aplicativo. Por exemplo, http://localhost:31544 pode ser uma URL para um aplicativo Web em execução no seu computador local. Os usuários usariam essa URL para entrar em um aplicativo cliente Web. Para aplicativos cliente públicos, informe o URI usado pelo Azure AD para retornar respostas de token. Digite um valor específico para sua aplicação, como myapp://auth. Para ver exemplos específicos para aplicativos web ou aplicativos nativos, confira nossas [partidas rápidas](https://docs.microsoft.com/azure/active-directory/develop).|

Depois de preencher os campos acima, o aplicativo é registrado no portal Azure e você é redirecionado para a página de visão geral do aplicativo. As páginas de configurações no painel esquerdo em **Gerenciar** têm mais campos para você personalizar sua aplicação. As tabelas abaixo descrevem todos os campos. Você só veria um subconjunto desses campos, dependendo se você criou um aplicativo web ou um aplicativo de cliente público.

### <a name="overview"></a>Visão geral

| Campo           | Descrição        |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ID do aplicativo  | Quando você registra um aplicativo, o Azure AD atribui a seu aplicativo uma ID de aplicativo. A ID do aplicativo pode ser usada para identificar exclusivamente o aplicativo nas solicitações de autenticação no Azure AD, além de acessar recursos, como a API do Graph.                                                          |
| URI de ID do aplicativo      | Este deve ser um URI exclusivo, geralmente do formulário **https://&lt;nome\_&gt;/&lt;\_&gt;** do nome do inquilino. Isso é usado durante o fluxo de concessão de autorização, como um identificador exclusivo para especificar o recurso para o qual o token deve ser emitido. Ele também se torna a declaração "aud" no token de acesso emitido. |

### <a name="branding"></a>Identidade visual

| Campo           | Descrição        |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Carregar novo logotipo | Pode ser usado para carregar um logotipo para seu aplicativo. O logotipo deve estar no formato .bmp, .jpg ou .png, e o tamanho do arquivo deve ser inferior a 100 KB. As dimensões da imagem devem ser 215x215 pixels, com as dimensões da imagem central de 94x94 pixels.|
| URL da home page   | É a URL de logon especificada durante o registro do aplicativo.|

### <a name="authentication"></a>Autenticação

| Campo           | Descrição        |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| URL de logoff      | Esta é a url de logout de saída única. O Azure AD envia uma solicitação de logoff para essa URL quando o usuário limpa a sessão com o Azure AD usando qualquer outro aplicativo registrado.|
| Tipos de conta com suporte  | Esta opção especifica se o aplicativo pode ser usado por vários locatários. Normalmente, isso significa que organizações externas podem usar seu aplicativo registrando-o em seu locatário e concedendo acesso aos dados da organização.|
| URLs de redirecionamento      | Os URLs redirecionados ou de resposta são os pontos finais em que o Azure AD retorna quaisquer tokens que seu aplicativo solicita. Para aplicativos nativos, é para onde o usuário é enviado após uma autorização bem-sucedida. O Azure AD verifica se o URI redirecionado que seu aplicativo fornece na solicitação OAuth 2.0 corresponde a um dos valores registrados no portal.|

### <a name="certificates-and-secrets"></a>Certificados e segredos

| Campo           | Descrição        |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Segredos do cliente            | Você pode criar segredos ou chaves do cliente para acessar programáticamente APIs da Web protegidas pelo Azure AD sem qualquer interação do usuário. A partir da página **secreta do novo cliente,** digite uma descrição da chave e a data de validade e salve para gerar a chave. Certifique-se de salvá-la em algum lugar seguro, pois você não poderá acessá-la depois.             |

## <a name="next-steps"></a>Próximas etapas

[Gerenciamento de aplicativos com o Active Directory do Azure](../manage-apps/what-is-application-management.md)
