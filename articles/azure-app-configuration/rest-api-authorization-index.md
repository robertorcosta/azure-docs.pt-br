---
title: API REST de configuração de Azure App-autorização
description: Páginas de referência para autorização usando a API REST de configuração de Azure App
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 54f9cfab1f49837a3765c978de6deeb9e5e7d644
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2020
ms.locfileid: "93423871"
---
# <a name="authorization"></a>Autorização

A autorização refere-se ao procedimento usado para determinar as permissões que um chamador tem ao fazer uma solicitação. Há vários modelos de autorização. O modelo de autorização usado para uma solicitação depende do método de [autenticação](./rest-api-authentication-index.md) usado. Os modelos de autorização estão listados abaixo.

## <a name="hmac"></a>HMAC

O modelo de [modelo de autorização](./rest-api-authorization-hmac.md) associado à autenticação HMAC divide as permissões em somente leitura ou leitura/gravação. Consulte a página [autorização HMAC](./rest-api-authorization-hmac.md) para obter detalhes.

## <a name="azure-active-directory"></a>Azure Active Directory

O [modelo de autorização](./rest-api-authorization-azure-ad.md) associado à autenticação do Azure Active Directory (AD do Azure) usa o RBAC do Azure para controlar permissões. Consulte a página de [autorização do Azure ad](./rest-api-authorization-azure-ad.md) para obter detalhes.
