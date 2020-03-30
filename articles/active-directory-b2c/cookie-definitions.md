---
title: Definições de cookie
titleSuffix: Azure AD B2C
description: Fornece definições para os cookies usados no Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b984b75b3a12606aa0d82c7e7b399d5dce59df33
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78189507"
---
# <a name="cookies-definitions-for-azure-ad-b2c"></a>Definições de cookies para Azure AD B2C

As seções a seguir fornecem informações sobre os cookies usados no Azure Active Directory B2C (Azure AD B2C).

## <a name="samesite"></a>MesmoSite

O serviço Microsoft Azure AD B2C é compatível com `SameSite=None` as `Secure` configurações do navegador SameSite, incluindo suporte para com o atributo.

Para proteger o acesso aos sites, os navegadores da Web introduzirão um novo modelo seguro por padrão que pressupõe que todos os cookies devem ser protegidos contra acesso externo, a menos que especificado de outra forma. O navegador Chrome é o primeiro a implementar essa mudança, começando pelo [Chrome 80 em fevereiro de 2020](https://www.chromium.org/updates/same-site). Para obter mais informações sobre como se preparar para a mudança no Chrome, consulte [Desenvolvedores: Prepare-se para o Novo Site=Nenhum; Proteja as configurações de cookies](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html) no Blog do Cromomium.

Os desenvolvedores devem usar `SameSite=None`a nova configuração de cookies, para designar cookies para acesso entre sites. Quando `SameSite=None` o atributo estiver `Secure` presente, um atributo adicional deve ser usado para que cookies entre sites só possam ser acessados em conexões HTTPS. Valide e teste todos os seus aplicativos, incluindo os aplicativos que usam o Azure AD B2C.

Para obter mais informações, consulte:

* [Manipular alterações de cookie SameSite no navegador Chrome](../active-directory/develop/howto-handle-samesite-cookie-changes-chrome-browser.md)
* [Efeito nos sites dos clientes e serviços e produtos da Microsoft na versão 80 ou posterior do Chrome](https://support.microsoft.com/help/4522904/potential-disruption-to-customer-websites-in-latest-chrome)

## <a name="cookies"></a>Cookies

A tabela a seguir lista os cookies usados no Azure AD B2C.

| Nome | Domínio | Expiração | Finalidade |
| ----------- | ------ | -------------------------- | --------- |
| `x-ms-cpim-admin` | main.b2cadmin.ext.azure.com | Fim da sessão do [navegador](session-behavior.md) | Mantém os dados de membros do usuário entre os inquilinos. Os inquilinos de um usuário são um membro e nível de adesão (Admin ou Usuário). |
| `x-ms-cpim-slice` | domínio b2clogin.com, login.microsoftonline.com e marcado | Fim da sessão do [navegador](session-behavior.md) | Usado para encaminhar solicitações para a instância de produção apropriada. |
| `x-ms-cpim-trans` | domínio b2clogin.com, login.microsoftonline.com e marcado | Fim da sessão do [navegador](session-behavior.md) | Usado para rastrear as transações (número de solicitações de autenticação ao Azure AD B2C) e a transação atual. |
| `x-ms-cpim-sso:{Id}` | domínio b2clogin.com, login.microsoftonline.com e marcado | Fim da sessão do [navegador](session-behavior.md) | Usado para manter a sessão SSO. |
| `x-ms-cpim-cache:{id}_n` | domínio b2clogin.com, login.microsoftonline.com e marcado | Fim da sessão do [navegador,](session-behavior.md)autenticação bem sucedida | Usado para manter o estado de solicitação. |
| `x-ms-cpim-csrf` | domínio b2clogin.com, login.microsoftonline.com e marcado | Fim da sessão do [navegador](session-behavior.md) | Token de falsificação de solicitação entre sites usado para proteção CRSF. |
| `x-ms-cpim-dc` | domínio b2clogin.com, login.microsoftonline.com e marcado | Fim da sessão do [navegador](session-behavior.md) | Usado para roteamento de rede Azure AD B2C. |
| `x-ms-cpim-ctx` | domínio b2clogin.com, login.microsoftonline.com e marcado | Fim da sessão do [navegador](session-behavior.md) | Contexto |
| `x-ms-cpim-rp` | domínio b2clogin.com, login.microsoftonline.com e marcado | Fim da sessão do [navegador](session-behavior.md) | Usado para armazenar dados de associação para o inquilino do provedor de recursos. |
| `x-ms-cpim-rc` | domínio b2clogin.com, login.microsoftonline.com e marcado | Fim da sessão do [navegador](session-behavior.md) | Usado para armazenar o biscoito de relé. |
