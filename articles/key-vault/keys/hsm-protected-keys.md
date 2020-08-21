---
title: Como gerar e transferir as chaves protegidas por HSM – Azure Key Vault
description: Use este artigo para ajudá-lo a planejar, gerar e transferir as suas próprias chaves de HSM protegido para usar com o Cofre da Chave do Azure. Também conhecido como BYOK ou Traga sua própria chave.
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: tutorial
ms.date: 05/29/2020
ms.author: ambapat
ms.openlocfilehash: 76b10dbd9b6d801d93cd5d9704531eb1a6de36a5
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88585484"
---
# <a name="import-hsm-protected-keys-to-key-vault"></a>Importar chaves protegidas por HSM para o Key Vault

Para garantia extra, ao usar o Cofre da Chave do Azure, você pode importar ou gerar chaves em módulos de segurança de hardware (HSM) que nunca extrapolam o limite do HSM. Normalmente, este cenário é conhecido como *BYOK* ou Bring Your Own Key. O Azure Key Vault usa a família nCipher nShield de HSMs (com validação FIPS 140-2 Nível 2) para proteger suas chaves.

Essa funcionalidade não está disponível para a Azure China 21Vianet.

> [!NOTE]
> Para obter mais informações sobre o Cofre da Chave do Azure, consulte [O que é o Cofre da Chave do Azure?](../general/overview.md)  
> Para obter um tutorial de Introdução, que inclui a criação de um cofre da chaves para chaves de HSM protegido, confira [O que é o Azure Key Vault?](../general/overview.md).

## <a name="supported-hsms"></a>HSMs compatíveis

Há suporte para a transferência de chaves protegidas por HSM para o Key Vault por meio de dois métodos diferentes, dependendo dos HSMs utilizados. Use a tabela abaixo para determinar qual método deve ser usado para os HSMs a serem gerados e transfira as próprias chaves protegidas por HSM a serem usadas com o Azure Key Vault. 

|Nome do Fornecedor|Tipo de fornecedor|Modelos de HSM compatíveis|Método de transferência de chave HSM compatível|
|---|---|---|---|
|[nCipher](https://www.ncipher.com/products/key-management/cloud-microsoft-azure)|Fabricante,<br/>HSM como serviço|<ul><li>Família de HSMs nShield</li><li>nShield como serviço</ul>|**Método 1:** [nCipher BYOK](hsm-protected-keys-ncipher.md) (com atestado forte para importação de chave e validação de HSM)<br/>**Método 2:** [usar o novo método BYOK](hsm-protected-keys-byok.md) |
|Thales|Fabricante|<ul><li>Família HSM 7 da Luna com versão de firmware 7.3 ou mais recente</li></ul>| [usar o novo método BYOK](hsm-protected-keys-byok.md)|
|Fortanix|Fabricante,<br/>HSM como serviço|<ul><li>SDKMS (Serviço de Gerenciamento de Chaves de Proteção Automática)</li><li>Equinix SmartKey</li></ul>|[usar o novo método BYOK](hsm-protected-keys-byok.md)|
|Marvell|Fabricante|Todos os HSMs LiquidSecurity com<ul><li>Firmware versão 2.0.4 ou posterior</li><li>Firmware versão 3.2 ou mais recente</li></ul>|[usar o novo método BYOK](hsm-protected-keys-byok.md)|
|Cryptomathic|ISV (Sistema de Gerenciamento de Chaves Empresariais)|Várias marcas e modelos de HSM, incluindo<ul><li>nCipher</li><li>Thales</li><li>Utimaco</li></ul>Acesse o [site da Cryptomathic para obter detalhes](https://www.cryptomathic.com/azurebyok)|[usar o novo método BYOK](hsm-protected-keys-byok.md)|


## <a name="next-steps"></a>Próximas etapas

* Siga [Melhores práticas do Key Vault](../general/best-practices.md) para garantir a segurança, a durabilidade e o monitoramento das suas chaves.
* Veja [Especificação de BYOK](https://docs.microsoft.com/azure/key-vault/keys/byok-specification) para obter uma descrição completa do novo método BYOK
