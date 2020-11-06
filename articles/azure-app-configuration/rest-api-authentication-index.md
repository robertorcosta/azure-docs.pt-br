---
title: API REST de configuração de Azure App-autenticação
description: Páginas de referência para autenticação usando a API REST de configuração de Azure App
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 21a43a005b78c8916d06e97ca9d2ba21d5a585a3
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2020
ms.locfileid: "93423912"
---
# <a name="authentication"></a>Autenticação

Todas as solicitações HTTP devem ser autenticadas. Há suporte para os seguintes esquemas de autenticação.

## <a name="hmac"></a>HMAC

A [autenticação HMAC](./rest-api-authentication-hmac.md) usa um segredo gerado aleatoriamente para assinar as cargas de solicitação. Os detalhes sobre como as solicitações que usam esse método de autenticação são autorizados podem ser encontrados na seção [autorização HMAC](./rest-api-authorization-hmac.md) .

## <a name="azure-active-directory"></a>Azure Active Directory

A [autenticação do Azure Active Directory (AD do Azure)](/azure/active-directory/authentication/overview-authentication) utiliza um token de portador que é obtido do Azure Active Directory para autenticar solicitações. Os detalhes sobre como as solicitações que usam esse método de autenticação são autorizados podem ser encontrados na seção [autorização do Azure ad](./rest-api-authorization-azure-ad.md) .
