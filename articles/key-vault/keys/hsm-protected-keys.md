---
title: Como gerar e transferir chaves protegidas por HSM para o Azure Key Vault - Azure Key Vault | Microsoft Docs
description: Use este artigo para ajudá-lo a planejar, gerar e transferir as suas próprias chaves de HSM protegido para usar com o Cofre da Chave do Azure. Também conhecido como BYOK ou Traga sua própria chave.
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: ambapat
ms.openlocfilehash: 58cf3358a9e908070ce9003d05dd0b576b1d2d3f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429688"
---
# <a name="import-hsm-protected-keys-to-key-vault"></a>Importe chaves protegidas pelo HSM para o cofre de chaves

Para garantia extra, ao usar o Cofre da Chave do Azure, você pode importar ou gerar chaves em módulos de segurança de hardware (HSM) que nunca extrapolam o limite do HSM. Normalmente, este cenário é conhecido como *Trazer a sua própria chave*ou BYOK. O Azure Key Vault usa a família nCipher nShield de HSMs (FIPS 140-2 Nível 2 validado) para proteger suas chaves.

Esta funcionalidade não está disponível para o Azure China 21Vianet.

> [!NOTE]
> Para obter mais informações sobre o Azure Key Vault, consulte [O que é o Azure Key Vault?](../general/overview.md)  
> Para obter um tutorial de Introdução, que inclui a criação de um cofre da chaves para chaves de HSM protegido, confira [O que é o Azure Key Vault?](../general/overview.md).

## <a name="supported-hsms"></a>HSMs suportados

A transferência de chaves protegidas pelo HSM para o Key Vault é suportada por dois métodos diferentes, dependendo dos HSMs que você usa. Use a tabela abaixo para determinar qual método deve ser usado para que seus HSMs gerem e, em seguida, transfira suas próprias chaves protegidas pelo HSM para usar com o Azure Key Vault. 

|Nome do Fornecedor|Tipo de fornecedor|Modelos HSM suportados|Método de transferência com chave HSM suportado|
|---|---|---|---|
|nCipher|Fabricante|<ul><li>família nShield de HSMs</li></ul>|[Use o método BYOK legado](hsm-protected-keys-legacy.md)|
|Thales|Fabricante|<ul><li>Família SafeNet Luna HSM 7 com firmware versão 7.3 ou mais recente</li></ul>| [Use o novo método BYOK (visualização)](hsm-protected-keys-vendor-agnostic-byok.md)|
|Fortita|HSM como serviço|<ul><li>Serviço de gerenciamento de chaves auto-defendssem (SDKMS)</li></ul>|[Use o novo método BYOK (visualização)](hsm-protected-keys-vendor-agnostic-byok.md)|


## <a name="next-steps"></a>Próximas etapas

Siga [as práticas recomendadas do Key Vault](../general/best-practices.md) para garantir segurança, durabilidade e monitoramento de suas chaves.
