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
ms.openlocfilehash: a36e15a56a5a4c8a637120ca730ae1da764d376d
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2020
ms.locfileid: "93422887"
---
# <a name="common-error-codes-for-azure-key-vault"></a>Códigos de erro comuns para Azure Key Vault

Os códigos de erro listados na tabela a seguir podem ser retornados por uma operação no cofre de chaves do Azure

| Código do erro | Mensagem do usuário |
|--|--|
| VaultAlreadyExists |  Falha na tentativa de criar um novo cofre de chaves com o nome especificado, pois o nome já está em uso. Se você excluiu recentemente um cofre de chaves com esse nome, ele ainda pode estar no estado de exclusão reversível. Você pode verificar se ele existe em estado de exclusão reversível [aqui](https://docs.microsoft.com/azure/key-vault/general/key-vault-recovery?tabs=azure-portal#list-recover-or-purge-a-soft-deleted-key-vault) |
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
