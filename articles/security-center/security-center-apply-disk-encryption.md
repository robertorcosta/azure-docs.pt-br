---
title: Aplicar a criptografia de disco na Central de Segurança do Azure | Microsoft Docs
description: Este documento mostra como implementar a recomendação do Azure Security Center **Aplicar criptografia de disco**.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 6cc7824a-8d6b-4a5f-ab40-e3bbaebc4a91
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: memildin
ms.openlocfilehash: e0677ecc63573d04e384a0104ee0e4e28b4d5e3b
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80473281"
---
# <a name="apply-disk-encryption-in-azure-security-center"></a>Aplicar a criptografia de disco na Central de Segurança do Azure

O Azure Security Center recomenda que você use o Azure Disk Encryption em discos não criptografados no Windows e linux VM. A Criptografia de Disco permite que você criptografe os discos de VM IaaS do Windows e do Linux.  A criptografia é recomendada para volumes de dados e do sistema operacional em sua VM.

O Disk Encryption usa o [BitLocker](https://technet.microsoft.com/library/cc732774.aspx), um recurso do Windows padrão da indústria, e o recurso [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) do Linux. Esses recursos fornecem SO e criptografia de dados para ajudar a proteger seus dados e atender a compromissos de segurança organizacional e de conformidade. A solução é integrada ao [Cofre de Chaves do Azure](https://azure.microsoft.com/documentation/services/key-vault/) para ajudá-lo a controlar e a gerenciar as chaves de criptografia de disco e segredos em sua assinatura de cofre de chaves, garantindo ao mesmo tempo que todos os dados nos discos de máquina virtual sejam criptografados em repouso no [Armazenamento do Azure](https://azure.microsoft.com/documentation/services/storage/).

Para obter a lista de versões suportadas do Windows e Linux, consulte [VMs e sistemas operacionais suportados](../virtual-machines/windows/disk-encryption-overview.md#supported-vms-and-operating-systems) na documentação de criptografia de disco do Azure.

## <a name="implement-the-recommendation"></a>Implementar a recomendação
1. Na página **Recomendações,** selecione **Criptografia de disco deve ser aplicada em máquinas virtuais**.
2. A partir dos **recursos insalubres,** selecione uma VM para a qual a criptografia de disco é recomendada.
3. Siga as instruções para aplicar a criptografia a essas VMs.

![Aplicando criptografia de disco](./media/security-center-apply-disk-encryption/apply-disk-encryption.png)

Para criptografar as Máquinas Virtuais do Azure identificadas pela Central de Segurança como precisando de criptografia, recomendamos as seguintes etapas:

* Instalar e configurar o PowerShell do Azure. Isso permitirá que você execute os comandos do PowerShell necessários para a configurar os pré-requisitos exigidos para criptografar as Máquinas Virtuais do Azure.
* Obter e executar o script Pré-requisitos de Azure Disk Encryption do Azure PowerShell.
* Criptografar suas máquinas virtuais.

[Criptografe uma VM Windows IaaS com o Azure PowerShell](../virtual-machines/windows/disk-encryption-powershell-quickstart.md) - Você orienta essas etapas e assume que você está usando uma máquina cliente do Windows a partir da qual você pode configurar criptografia de disco.

Há várias abordagens que podem ser usadas para as Máquinas Virtuais do Azure. Se você já é bem versado no Azure PowerShell ou no Azure CLI, então você pode preferir usar abordagens alternativas. Para saber mais sobre essas outras abordagens, confira [Azure Disk Encryption](../security/fundamentals/encryption-overview.md).

## <a name="see-also"></a>Confira também
Este documento mostrou como implementar a recomendação da Central de Segurança "Aplicar criptografia de disco". Para saber mais sobre criptografia de disco, consulte:

* [Criptografia e gerenciamento de chaves com o Azure Key Vault](https://azure.microsoft.com/documentation/videos/azurecon-2015-encryption-and-key-management-with-azure-key-vault/) (vídeo, 36 min 39 seg)-- Aprenda a usar o gerenciamento de criptografia de disco para VMs IaaS e Azure Key Vault para ajudar a proteger e proteger seus dados.
* Criptografia de [disco Azure](../security/fundamentals/encryption-overview.md) (documento)-- Saiba como ativar a criptografia de disco para VMs Windows e Linux.