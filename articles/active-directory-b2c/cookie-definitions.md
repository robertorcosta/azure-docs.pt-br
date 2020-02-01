---
title: Definições de cookie
titleSuffix: Azure AD B2C
description: Fornece definições para os cookies usados no Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: c6e48a663e4d1702851e11bc5124e56c52309d08
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/31/2020
ms.locfileid: "76908863"
---
# <a name="cookies-definitions-for-azure-ad-b2c"></a>Definições de cookies para Azure AD B2C

As seções a seguir fornecem informações sobre os cookies usados no Azure Active Directory B2C (Azure AD B2C).

## <a name="samesite"></a>SameSite

O serviço B2C do Microsoft Azure AD é compatível com as configurações do navegador SameSite, incluindo suporte para `SameSite=None` com o atributo `Secure`.

Para proteger o acesso a sites, os navegadores da Web apresentarão um novo modelo seguro por padrão que assume que todos os cookies devem ser protegidos do acesso externo, a menos que especificado de outra forma. O navegador Chrome é o primeiro a implementar essa alteração, começando com o [Chrome 80 em fevereiro de 2020](https://www.chromium.org/updates/same-site). Para obter mais informações sobre como se preparar para a alteração no Chrome, consulte [desenvolvedores: Prepare-se para o New SameSite = None; Proteger as configurações de cookie](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html) no blog do Chromium.

Os desenvolvedores devem usar a nova configuração de cookie, `SameSite=None`, para designar cookies para acesso entre sites. Quando o atributo `SameSite=None` estiver presente, um atributo `Secure` adicional deverá ser usado para que os cookies entre sites só possam ser acessados por conexões HTTPS. Valide e teste todos os seus aplicativos, incluindo os aplicativos que usam Azure AD B2C.

Para obter mais informações, consulte:

* [Manipular alterações de cookie SameSite no navegador Chrome](../active-directory/develop/howto-handle-samesite-cookie-changes-chrome-browser.md)
* [Efeito em sites de clientes e serviços e produtos da Microsoft no Chrome versão 80 ou posterior](https://support.microsoft.com/help/4522904/potential-disruption-to-customer-websites-in-latest-chrome)

## <a name="cookies"></a>Cookies

A tabela a seguir lista os cookies usados no Azure AD B2C.

| Nome | Domínio | Expiração | Finalidade |
| ----------- | ------ | -------------------------- | --------- |
| `x-ms-cpim-admin` | main.b2cadmin.ext.azure.com | Fim da [sessão do navegador](session-behavior.md) | Mantém os dados de associação do usuário entre locatários. Os locatários de que um usuário é membro e nível de associação (administrador ou usuário). |
| `x-ms-cpim-slice` | b2clogin.com, login.microsoftonline.com, domínio com marca | Fim da [sessão do navegador](session-behavior.md) | Usado para rotear solicitações para a instância de produção apropriada. |
| `x-ms-cpim-trans` | b2clogin.com, login.microsoftonline.com, domínio com marca | Fim da [sessão do navegador](session-behavior.md) | Usado para acompanhar as transações (número de solicitações de autenticação para Azure AD B2C) e a transação atual. |
| `x-ms-cpim-sso:{Id}` | b2clogin.com, login.microsoftonline.com, domínio com marca | Fim da [sessão do navegador](session-behavior.md) | Usado para manter a sessão de SSO. |
| `x-ms-cpim-cache:{id}_n` | b2clogin.com, login.microsoftonline.com, domínio com marca | Fim da [sessão do navegador](session-behavior.md), autenticação bem-sucedida | Usado para manter o estado da solicitação. |
| `x-ms-cpim-csrf` | b2clogin.com, login.microsoftonline.com, domínio com marca | Fim da [sessão do navegador](session-behavior.md) | Token de solicitação entre sites forjado usado para proteção CRSF. |
| `x-ms-cpim-dc` | b2clogin.com, login.microsoftonline.com, domínio com marca | Fim da [sessão do navegador](session-behavior.md) | Usado para Azure AD B2C roteamento de rede. |
| `x-ms-cpim-ctx` | b2clogin.com, login.microsoftonline.com, domínio com marca | Fim da [sessão do navegador](session-behavior.md) | Contexto |
| `x-ms-cpim-rp` | b2clogin.com, login.microsoftonline.com, domínio com marca | Fim da [sessão do navegador](session-behavior.md) | Usado para armazenar dados de associação para o locatário do provedor de recursos. |
| `x-ms-cpim-rc` | b2clogin.com, login.microsoftonline.com, domínio com marca | Fim da [sessão do navegador](session-behavior.md) | Usado para armazenar o cookie de retransmissão. |
