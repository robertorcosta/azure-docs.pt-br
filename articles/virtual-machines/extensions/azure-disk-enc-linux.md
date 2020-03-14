---
title: Azure Disk Encryption para Linux
description: Implante o Azure Disk Encryption na máquina virtual do Linux usando uma extensão da máquina virtual.
services: virtual-machines-linux
documentationcenter: ''
author: ejarvi
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/10/2019
ms.author: ejarvi
ms.openlocfilehash: 4fa7f7d1419a8cd1006a632ba67587ab3434bf5a
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79254021"
---
# <a name="azure-disk-encryption-for-linux-microsoftazuresecurityazurediskencryptionforlinux"></a>Azure Disk Encryption para Linux (Microsoft.Azure.Security.AzureDiskEncryptionForLinux)

## <a name="overview"></a>Visão geral

Azure Disk Encryption aproveita o subsistema de dm-crypt no Linux para fornecer criptografia de disco completo em [selecionar distribuições de Linux do Azure](https://aka.ms/adelinux).  A solução é integrada ao Azure Key Vault para gerenciar as chaves de criptografia de disco e segredos.

## <a name="prerequisites"></a>Prerequisites

Para obter uma lista completa de pré-requisitos, consulte [Azure Disk Encryption para VMs do Linux](../linux/disk-encryption-overview.md), especificamente as seguintes seções:

- [Azure Disk Encryption para VMs Linux](../linux/disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Requisitos adicionais de VM](../linux/disk-encryption-overview.md#additional-vm-requirements)
- [Requisitos de rede](../linux/disk-encryption-overview.md#networking-requirements)

## <a name="extension-schemata"></a>Schemata de extensão

Há duas Schemata para Azure Disk Encryption: v 1.1, um esquema mais novo e recomendado que não usa as propriedades do AAD (Azure Active Directory) e v 0.1, um esquema mais antigo que requer Propriedades do AAD. Você deve usar a versão do esquema correspondente à extensão que está usando: esquema v 1.1 para a extensão AzureDiskEncryptionForLinux versão 1,1, esquema v 0.1 para a extensão AzureDiskEncryptionForLinux versão 0,1.
### <a name="schema-v11-no-aad-recommended"></a>Esquema v 1.1: sem AAD (recomendado)

O esquema v 1.1 é recomendado e não requer Azure Active Directory Propriedades.

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2015-06-15",
  "location": "[location]",
  "properties": {
        "publisher": "Microsoft.Azure.Security",
        "settings": {
          "DiskFormatQuery": "[diskFormatQuery]",
          "EncryptionOperation": "[encryptionOperation]",
          "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
          "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
          "KeyVaultURL": "[keyVaultURL]",
          "SequenceVersion": "sequenceVersion]",
          "VolumeType": "[volumeType]"
        },
        "type": "AzureDiskEncryptionForLinux",
        "typeHandlerVersion": "[extensionVersion]"
  }
}
```


### <a name="schema-v01-with-aad"></a>Esquema v 0,1: com o AAD 

O esquema 0,1 requer `aadClientID` e `aadClientSecret` ou `AADClientCertificate`.

Usando `aadClientSecret`:

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2015-06-15",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientSecret": "[aadClientSecret]",
      "Passphrase": "[passphrase]"
    },
    "publisher": "Microsoft.Azure.Security",
    "settings": {
      "AADClientID": "[aadClientID]",
      "DiskFormatQuery": "[diskFormatQuery]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KeyVaultURL": "[keyVaultURL]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    },
    "type": "AzureDiskEncryptionForLinux",
    "typeHandlerVersion": "[extensionVersion]"
  }
}
```

Usando `AADClientCertificate`:

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2015-06-15",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientCertificate": "[aadClientCertificate]",
      "Passphrase": "[passphrase]"
    },
    "publisher": "Microsoft.Azure.Security",
    "settings": {
      "AADClientID": "[aadClientID]",
      "DiskFormatQuery": "[diskFormatQuery]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KeyVaultURL": "[keyVaultURL]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    },
    "type": "AzureDiskEncryptionForLinux",
    "typeHandlerVersion": "[extensionVersion]"
  }
}
```


### <a name="property-values"></a>Valores de propriedade

| Nome | Valor/Exemplo | Tipo de Dados |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| publicador | Microsoft.Azure.Security | string |
| type | AzureDiskEncryptionForLinux | string |
| typeHandlerVersion | 0.1, 1.1 | INT |
| (esquema 0.1) AADClientID | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | guid | 
| (esquema 0.1) AADClientSecret | password | string |
| (esquema 0.1) AADClientCertificate | thumbprint | string |
| DiskFormatQuery | {"dev_path":"","name":"","file_system":""} | Dicionário JSON |
| EncryptionOperation | EnableEncryption, EnableEncryptionFormatAll | string | 
| KeyEncryptionAlgorithm | 'RSA-OAEP', 'RSA-OAEP-256', 'RSA1_5' | string |
| KeyEncryptionKeyURL | url | string |
| adicional KeyVaultURL | url | string |
| Senha | password | string | 
| SequenceVersion | UNIQUEIDENTIFIER | string |
| VolumeType | Sistema operacional, Dados, Tudo | string |

## <a name="template-deployment"></a>Implantação de modelo

Para obter um exemplo de implantação de modelo, consulte [Habilitar a criptografia em uma VM do Linux em execução](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm).

## <a name="azure-cli-deployment"></a>Implantação da CLI do Azure

As instruções podem ser encontradas na última [documentação da CLI do Azure](/cli/azure/vm/encryption?view=azure-cli-latest). 

## <a name="troubleshoot-and-support"></a>Solução de problemas e suporte

### <a name="troubleshoot"></a>Solucionar problemas

Para solução de problemas, consulte o [Guia de solução de problemas do Azure Disk Encryption](../../security/azure-security-disk-encryption-tsg.md).

### <a name="support"></a>Suporte

Caso precise de mais ajuda em qualquer ponto deste artigo, entre em contato com os especialistas do Azure nos [fóruns do Azure e do Stack Overflow no MSDN](https://azure.microsoft.com/support/community/). Como alternativa, você pode registrar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione Obter suporte. Para saber mais sobre como usar o suporte do Azure, leia as [Perguntas frequentes sobre o suporte do Microsoft Azure](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre extensões de VM, consulte [Recursos e extensões da máquina virtual para Linux](features-linux.md).