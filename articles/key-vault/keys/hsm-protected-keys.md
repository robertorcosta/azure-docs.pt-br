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
ms.date: 05/29/2020
ms.author: ambapat
ms.openlocfilehash: 5433d9746cd64d0e942e056cfcd1940eba35c77d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84417915"
---
# <a name="import-hsm-protected-keys-to-key-vault"></a>Importar chaves protegidas por HSM para o Key Vault

Para garantia extra, ao usar o Cofre da Chave do Azure, você pode importar ou gerar chaves em módulos de segurança de hardware (HSM) que nunca extrapolam o limite do HSM. Normalmente, este cenário é conhecido como *Trazer a sua própria chave*ou BYOK. Azure Key Vault usa a família nCipher nShield de HSMs (FIPS 140-2 nível 2 validado) para proteger suas chaves.

Essa funcionalidade não está disponível para o Azure China 21Vianet.

> [!NOTE]
> Para obter mais informações sobre o Cofre da Chave do Azure, consulte [O que é o Cofre da Chave do Azure?](../general/overview.md)  
> Para obter um tutorial de Introdução, que inclui a criação de um cofre da chaves para chaves de HSM protegido, confira [O que é o Azure Key Vault?](../general/overview.md).

## <a name="supported-hsms"></a>HSMs com suporte

Há suporte para a transferência de chaves protegidas por HSM para Key Vault por meio de dois métodos diferentes, dependendo dos HSMs que você usa. Use a tabela abaixo para determinar qual método deve ser usado para os HSMs a serem gerados e, em seguida, transfira suas próprias chaves protegidas por HSM para usar com Azure Key Vault. 

|Nome do Fornecedor|Tipo de fornecedor|Modelos HSM com suporte|Método de transferência de chave HSM com suporte|
|---|---|---|---|
|[nCipher](https://www.ncipher.com/products/key-management/cloud-microsoft-azure)|Manufacturer<br/>HSM como um serviço|<ul><li>família de HSMs nShield</li><li>nShield como um serviço</ul>|**Método 1:** [nCipher BYOK](hsm-protected-keys-ncipher.md) (com atestado forte para importação de chave e validação HSM)<br/>**Método 2:** [usar o novo método BYOK](hsm-protected-keys-byok.md) |
|Thales|Fabricante|<ul><li>Família de HSM 7 da Luna com firmware versão 7,3 ou mais recente</li></ul>| [Usar o novo método BYOK](hsm-protected-keys-byok.md)|
|Fortanix|Manufacturer<br/>HSM como um serviço|<ul><li>Serviço de gerenciamento de chaves de autodefesa (SDKMS)</li><li>Equinix SmartKey</li></ul>|[Usar o novo método BYOK](hsm-protected-keys-byok.md)|
|Marvell|Fabricante|Todos os HSMs LiquidSecurity com<ul><li>Firmware versão 2.0.4 ou posterior</li><li>Firmware versão 3,2 ou mais recente</li></ul>|[Usar o novo método BYOK](hsm-protected-keys-byok.md)|
|Cryptomathic|ISV (sistema de gerenciamento de chaves empresariais)|Várias marcas e modelos HSM, incluindo<ul><li>nCipher</li><li>Thales</li><li>Utimaco</li></ul>Consulte o [site do Cryptomathic para obter detalhes](https://www.cryptomathic.com/azurebyok)|[Usar o novo método BYOK](hsm-protected-keys-byok.md)|


## <a name="next-steps"></a>Próximas etapas

* Siga [Key Vault práticas recomendadas](../general/best-practices.md) para garantir a segurança, durabilidade e monitoramento para suas chaves.
* Consulte a [especificação BYOK](https://docs.microsoft.com/azure/key-vault/keys/byok-specification) para obter uma descrição completa do novo método BYOK
