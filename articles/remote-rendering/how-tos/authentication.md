---
title: Autenticação
description: Explica como funciona a autenticação
author: florianborn71
ms.author: flborn
ms.date: 02/12/2019
ms.topic: how-to
ms.openlocfilehash: fbc27f2fa3b7517151c4bcdbec5b146e83dd868e
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681630"
---
# <a name="configure-authentication"></a>Configurar autenticação

A renderização remota do Azure usa o mesmo mecanismo de autenticação [que as Âncoras Espaciais Do Zure (ASA).](https://docs.microsoft.com/azure/spatial-anchors/concepts/authentication?tabs=csharp) Os clientes precisam definir *AccountKey,* *AuthenticationToken*ou *AccessToken* para chamar as APIs REST com sucesso. *AccountKey* pode ser obtido na guia "Chaves" para a conta de renderização remota no portal Azure. *AutenticaçãoToken* é um token Azure AD, que pode ser obtido usando a [biblioteca ADAL](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries). *AccessToken* é um token MR, que pode ser obtido do Azure Mixed Reality Security Token Service (STS).

## <a name="authentication-for-deployed-applications"></a>Autenticação para aplicativos implantados

 O uso de chaves de conta é recomendado para um embarque rápido, mas durante o desenvolvimento/prototipagem apenas. É fortemente recomendável não enviar seu aplicativo para a produção usando uma chave de conta incorporada nele e, em vez disso, usar as abordagens de autenticação Azure AD baseadas no usuário ou no serviço.

 A autenticação Azure AD é descrita na seção de autenticação do [usuário Azure AD](https://docs.microsoft.com/azure/spatial-anchors/concepts/authentication?tabs=csharp#azure-ad-user-authentication) do serviço [ASA (Azure Spatial Anchors).](https://docs.microsoft.com/azure/spatial-anchors/)

## <a name="role-based-access-control"></a>Controle de acesso baseado em função

Para ajudar a controlar o nível de acesso concedido aos aplicativos, serviços ou usuários ad do seu serviço, foram criadas as seguintes funções para que você atribua conforme necessário contra suas contas de renderização remota do Azure:

* **Administrador de renderização remota**: fornece ao usuário recursos de conversão, sessão de gerenciamento, renderização e diagnóstico para renderização remota do Azure.
* **Cliente de renderização remota**: fornece ao usuário recursos de sessão de gerenciamento, renderização e diagnóstico para renderização remota do Azure.

## <a name="next-steps"></a>Próximas etapas

* [Criar uma conta](create-an-account.md)
* [Usando as APIs do Frontend do Azure para autenticação](frontend-apis.md)
* [Exemplo de scripts PowerShell](../samples/powershell-example-scripts.md)
