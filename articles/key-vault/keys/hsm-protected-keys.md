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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81429688"
---
# <a name="import-hsm-protected-keys-to-key-vault"></a>Importar chaves protegidas por HSM para o Key Vault

Para garantia extra, ao usar o Cofre da Chave do Azure, você pode importar ou gerar chaves em módulos de segurança de hardware (HSM) que nunca extrapolam o limite do HSM. Normalmente, este cenário é conhecido como *Trazer a sua própria chave*ou BYOK. Azure Key Vault usa a família nCipher nShield de HSMs (FIPS 140-2 nível 2 validado) para proteger suas chaves.

Essa funcionalidade não está disponível para o Azure China 21Vianet.

> [!NOTE]
> Para obter mais informações sobre Azure Key Vault, consulte [o que é Azure Key Vault?](../general/overview.md)  
> Para obter um tutorial de Introdução, que inclui a criação de um cofre da chaves para chaves de HSM protegido, confira [O que é o Azure Key Vault?](../general/overview.md).

## <a name="supported-hsms"></a>HSMs com suporte

Há suporte para a transferência de chaves protegidas por HSM para Key Vault por meio de dois métodos diferentes, dependendo dos HSMs que você usa. Use a tabela abaixo para determinar qual método deve ser usado para os HSMs a serem gerados e, em seguida, transfira suas próprias chaves protegidas por HSM para usar com Azure Key Vault. 

|Nome do Fornecedor|Tipo de fornecedor|Modelos HSM com suporte|Método de transferência de chave HSM com suporte|
|---|---|---|---|
|nCipher|Fabricante|<ul><li>família de HSMs nShield</li></ul>|[Usar método BYOK herdado](hsm-protected-keys-legacy.md)|
|Thales|Fabricante|<ul><li>Família SafeNet Luna HSM 7 com firmware versão 7,3 ou mais recente</li></ul>| [Usar o novo método BYOK (visualização)](hsm-protected-keys-vendor-agnostic-byok.md)|
|Fortanix|HSM como um serviço|<ul><li>Serviço de gerenciamento de chaves de autodefesa (SDKMS)</li></ul>|[Usar o novo método BYOK (visualização)](hsm-protected-keys-vendor-agnostic-byok.md)|


## <a name="next-steps"></a>Próximas etapas

Siga [Key Vault práticas recomendadas](../general/best-practices.md) para garantir a segurança, durabilidade e monitoramento para suas chaves.
