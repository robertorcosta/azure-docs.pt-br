---
title: API REST de configuração de Azure App-autenticação
description: Páginas de referência para autenticação usando a API REST de configuração de Azure App
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 56416009395ebf8270ad0fa8d141277424dd6d9a
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/26/2020
ms.locfileid: "96183457"
---
# <a name="authentication"></a>Autenticação

Todas as solicitações HTTP devem ser autenticadas. Há suporte para os seguintes esquemas de autenticação.

## <a name="hmac"></a>HMAC

A [autenticação HMAC](./rest-api-authentication-hmac.md) usa um segredo gerado aleatoriamente para assinar as cargas de solicitação. Os detalhes sobre como as solicitações que usam esse método de autenticação são autorizados podem ser encontrados na seção [autorização HMAC](./rest-api-authorization-hmac.md) .

## <a name="azure-active-directory"></a>Azure Active Directory

A [autenticação do Azure Active Directory (AD do Azure)](../active-directory/authentication/overview-authentication.md) utiliza um token de portador que é obtido do Azure Active Directory para autenticar solicitações. Os detalhes sobre como as solicitações que usam esse método de autenticação são autorizados podem ser encontrados na seção [autorização do Azure ad](./rest-api-authorization-azure-ad.md) .