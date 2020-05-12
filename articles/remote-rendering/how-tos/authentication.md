---
title: Autenticação
description: Explica como funciona a autenticação
author: florianborn71
ms.author: flborn
ms.date: 02/12/2019
ms.topic: how-to
ms.custom: has-adal-ref
ms.openlocfilehash: 34874e01b526a4d88ff4402edb0c3771c75a3726
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83195295"
---
# <a name="configure-authentication"></a>Configurar autenticação

A renderização remota do Azure usa o mesmo mecanismo de autenticação que o [asa (âncoras espaciais) do Azure](https://docs.microsoft.com/azure/spatial-anchors/concepts/authentication?tabs=csharp). Os clientes precisam definir *AccountKey*, *AuthenticationToken*ou *ACCESSTOKEN* para chamar as APIs REST com êxito. *AccountKey* pode ser obtido na guia "Keys" para a conta de renderização remota no portal do Azure. *AuthenticationToken* é um token do Azure AD, que pode ser obtido usando a [biblioteca Adal](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries). *AccessToken* é um token Mr, que pode ser obtido do STS (serviço de token de segurança) do Azure Mixed Reality.

## <a name="authentication-for-deployed-applications"></a>Autenticação para aplicativos implantados

 O uso de chaves de conta é recomendado para rápida integração, mas somente durante o desenvolvimento/protótipo somente. É altamente recomendável não enviar seu aplicativo para produção usando uma chave de conta incorporada e, em vez disso, usar as abordagens de autenticação do Azure AD baseadas no usuário ou no serviço.

 A autenticação do Azure AD é descrita na seção de [autenticação de usuário do Azure ad](https://docs.microsoft.com/azure/spatial-anchors/concepts/authentication?tabs=csharp#azure-ad-user-authentication) do serviço do [asa (âncora espacial) do Azure](https://docs.microsoft.com/azure/spatial-anchors/) .

## <a name="role-based-access-control"></a>Controle de acesso baseado em função

Para ajudar a controlar o nível de acesso concedido a aplicativos, serviços ou usuários do Azure AD do seu serviço, as seguintes funções foram criadas para você atribuir conforme necessário em suas contas de renderização remota do Azure:

* **Administrador de renderização remota**: fornece ao usuário recursos de conversão, gerenciamento de sessão, renderização e diagnóstico para a renderização remota do Azure.
* **Cliente de renderização remota**: fornece ao usuário recursos de gerenciamento de sessão, renderização e diagnóstico para a renderização remota do Azure.

## <a name="next-steps"></a>Próximas etapas

* [Criar uma conta](create-an-account.md)
* [Usando as APIs de front-end do Azure para autenticação](frontend-apis.md)
* [Scripts de exemplo do PowerShell](../samples/powershell-example-scripts.md)
