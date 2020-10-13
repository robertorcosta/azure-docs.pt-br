---
title: Códigos de erro comuns para Azure Key Vault | Microsoft Docs
description: Códigos de erro comuns para Azure Key Vault
services: key-vault
author: sebansal
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: reference
ms.date: 09/29/2020
ms.author: mbaldwin
ms.openlocfilehash: 25c79229a09db912903fba825f0d48f571880745
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91876872"
---
# <a name="common-error-codes-for-azure-key-vault"></a>Códigos de erro comuns para Azure Key Vault

Os códigos de erro listados na tabela a seguir podem ser retornados por uma operação no cofre de chaves do Azure

| Código do erro | Mensagem do usuário |
|--|--|
| VaultAlreadyExists |  O cofre de chaves especificado já existe (no estado excluído de maneira reversível ou em outra assinatura). |
| VaultNameNotValid |  O nome do cofre deve ter 24 caracteres, alfanuméricos e começar com um alfabeto |
| AccessDenied |  Talvez você não tenha permissões na política de acesso para executar essa operação. |
| ForbiddenByFirewall |  O endereço do cliente não é autorizado e o chamador não é um serviço confiável. |
| ConflictError |  Você está solicitando várias operações no mesmo item.  |
| RegionNotSupported |  A região do Azure especificada não tem suporte para este recurso. |
| SkuNotSupported |  O tipo de SKU especificado não tem suporte para este recurso. |
| ResourceNotFound |  O recurso do Azure especificado não foi encontrado. |
| CertificateExpired |  Verifique a data de validade e o período de validade do certificado. |


## <a name="next-steps"></a>Próximas etapas

- Confira o [Guia do desenvolvedor do Azure Key Vault](developers-guide.md)
- Leia mais sobre a [Autenticação no Key Vault](authentication.md)
