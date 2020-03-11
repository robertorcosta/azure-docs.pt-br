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
ms.openlocfilehash: 048e5072c592cf2de32e533014c99034572a1c47
ms.sourcegitcommit: 72c2da0def8aa7ebe0691612a89bb70cd0c5a436
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2020
ms.locfileid: "79082890"
---
# <a name="import-hsm-protected-keys-to-key-vault"></a>Importar chaves protegidas por HSM para Key Vault

Para garantia extra, ao usar o Cofre da Chave do Azure, você pode importar ou gerar chaves em módulos de segurança de hardware (HSM) que nunca extrapolam o limite do HSM. Normalmente, este cenário é conhecido como *Trazer a sua própria chave*ou BYOK. Azure Key Vault usa a família nCipher nShield de HSMs (FIPS 140-2 nível 2 validado) para proteger suas chaves.

Essa funcionalidade não está disponível para o Azure China 21Vianet.

> [!NOTE]
> Para obter mais informações sobre o Cofre da Chave do Azure, consulte [O que é o Cofre da Chave do Azure?](key-vault-overview.md)  
> Para obter um tutorial de Introdução, que inclui a criação de um cofre da chaves para chaves de HSM protegido, confira [O que é o Azure Key Vault?](key-vault-overview.md).

## <a name="supported-hsms"></a>HSMs com suporte

Há suporte para a transferência de chaves protegidas por HSM para Key Vault por meio de dois métodos diferentes, dependendo dos HSMs que você usa. Use a tabela abaixo para determinar qual método deve ser usado para os HSMs a serem gerados e, em seguida, transfira suas próprias chaves protegidas por HSM para usar com Azure Key Vault. 

|Nome do Fornecedor|Tipo de fornecedor|Modelos HSM com suporte|Método de transferência de chave HSM com suporte|
|---|---|---|---|
|nCipher|Fabricante|<ul><li>família de HSMs nShield</li></ul>|[Usar método BYOK herdado](hsm-protected-keys-legacy.md)|
|Thales|Fabricante|<ul><li>Família SafeNet Luna HSM 7 com firmware versão 7,3 ou mais recente</li></ul>| [Usar o novo método BYOK (visualização)](hsm-protected-keys-vendor-agnostic-byok.md)|
|Fortanix|HSM como um serviço|<ul><li>Serviço de gerenciamento de chaves de autodefesa (SDKMS)</li></ul>|[Usar o novo método BYOK (visualização)](hsm-protected-keys-vendor-agnostic-byok.md)|










## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

Siga [Key Vault práticas recomendadas](key-vault-best-practices.md) para garantir a segurança, durabilidade e monitoramento para suas chaves.
