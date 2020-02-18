---
title: Como gerar e transferir chaves protegidas por HSM para o Azure Key Vault - Azure Key Vault | Microsoft Docs
description: Use este artigo para ajudá-lo a planejar, gerar e transferir as suas próprias chaves de HSM protegido para usar com o Cofre da Chave do Azure. Também conhecido como BYOK ou Traga sua própria chave.
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: ambapat
ms.openlocfilehash: 00d2d38801929454110b41be88d133e3af232af7
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/18/2020
ms.locfileid: "77425741"
---
# <a name="import-hsm-protected-keys-to-key-vault"></a>Importar chaves protegidas por HSM para Key Vault

Para garantia extra, ao usar o Cofre da Chave do Azure, você pode importar ou gerar chaves em módulos de segurança de hardware (HSM) que nunca extrapolam o limite do HSM. Normalmente, este cenário é conhecido como *Trazer a sua própria chave*ou BYOK. Azure Key Vault usa a família nCipher nShield de HSMs (FIPS 140-2 nível 2 validado) para proteger suas chaves.

Essa funcionalidade não está disponível para o Azure China 21Vianet.

> [!NOTE]
> Para obter mais informações sobre o Cofre da Chave do Azure, consulte [O que é o Cofre da Chave do Azure?](key-vault-overview.md)  
> Para obter um tutorial de Introdução, que inclui a criação de um cofre da chaves para chaves de HSM protegido, confira [O que é o Azure Key Vault?](key-vault-overview.md).

## <a name="supported-hsms"></a>HSMs com suporte

Há suporte para a transferência de chaves protegidas por HSM para Key Vault por meio de dois métodos diferentes, dependendo dos HSMs que você usa. Use a tabela abaixo para determinar qual método deve ser usado para os HSMs a serem gerados e, em seguida, transfira suas próprias chaves protegidas por HSM para usar com Azure Key Vault. 

|Nome do fornecedor do HSM|Modelos HSM com suporte|Método de transferência de chave HSM com suporte|
|---|---|---|
|Thales|<ul><li>Família SafeNet Luna HSM 7 com firmware versão 7,3 ou mais recente</li></ul>| [Usar o novo método BYOK (visualização)](hsm-protected-keys-vendor-agnostic-byok.md)|
|nCipher|<ul><li>família de HSMs nShield</li></ul>|[Usar método BYOK herdado](hsm-protected-keys-legacy.md)|


## <a name="next-steps"></a>Próximas etapas

Siga [Key Vault práticas recomendadas](key-vault-best-practices.md) para garantir a segurança, durabilidade e monitoramento para suas chaves.
