---
title: Criptografia de disco azure para Linux
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
ms.date: 03/19/2020
ms.author: ejarvi
ms.openlocfilehash: 22568c7c23771f143f6cd583114949c380d15e3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066910"
---
# <a name="azure-disk-encryption-for-linux-microsoftazuresecurityazurediskencryptionforlinux"></a>Azure Disk Encryption para Linux (Microsoft.Azure.Security.AzureDiskEncryptionForLinux)

## <a name="overview"></a>Visão geral

Azure Disk Encryption aproveita o subsistema de dm-crypt no Linux para fornecer criptografia de disco completo em [selecionar distribuições de Linux do Azure](https://aka.ms/adelinux).  A solução é integrada ao Azure Key Vault para gerenciar as chaves de criptografia de disco e segredos.

## <a name="prerequisites"></a>Pré-requisitos

Para obter uma lista completa de pré-requisitos, consulte [Azure Disk Encryption for Linux VMs](../linux/disk-encryption-overview.md), especificamente as seguintes seções:

- [VMs e sistemas operacionais suportados](../linux/disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Requisitos adicionais de VM](../linux/disk-encryption-overview.md#additional-vm-requirements)
- [Requisitos de rede](../linux/disk-encryption-overview.md#networking-requirements)
- [Requisitos de armazenamento de chaves de criptografia](../linux/disk-encryption-overview.md#encryption-key-storage-requirements)

## <a name="extension-schema"></a>Esquema de Extensão

Existem duas versões do esquema de extensão para Azure Disk Encryption (ADE):
- v1.1 - Um esquema recomendado mais novo que não usa propriedades Azure Active Directory (AAD).
- v0.1 - Um esquema antigo que requer propriedades AaD (AAD) do Azure Active Directory (AAD). 

Para selecionar um esquema de `typeHandlerVersion` destino, a propriedade deve ser definida como uma versão do esquema que você deseja usar.

### <a name="schema-v11-no-aad-recommended"></a>Esquema v1.1: Sem AAD (recomendado)

O esquema v1.1 é recomendado e não requer propriedades AaD (AAD) do Azure Active Directory (AAD).

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2019-07-01",
  "location": "[location]",
  "properties": {
        "publisher": "Microsoft.Azure.Security",
        "type": "AzureDiskEncryptionForLinux",
        "typeHandlerVersion": "1.1",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "DiskFormatQuery": "[diskFormatQuery]",
          "EncryptionOperation": "[encryptionOperation]",
          "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
          "KeyVaultURL": "[keyVaultURL]",
          "KeyVaultResourceId": "[KeyVaultResourceId]",
          "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
          "KekVaultResourceId": "[KekVaultResourceId",
          "SequenceVersion": "sequenceVersion]",
          "VolumeType": "[volumeType]"
        }
  }
}
```


### <a name="schema-v01-with-aad"></a>Esquema v0.1: com AAD 

O esquema 0.1 `AADClientID` requer `AADClientSecret` e `AADClientCertificate`ou ou .

Usando `AADClientSecret`:

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2019-07-01",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientSecret": "[aadClientSecret]",
      "Passphrase": "[passphrase]"
    },
    "publisher": "Microsoft.Azure.Security",
    "type": "AzureDiskEncryptionForLinux",
    "typeHandlerVersion": "0.1",
    "settings": {
      "AADClientID": "[aadClientID]",
      "DiskFormatQuery": "[diskFormatQuery]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KeyVaultURL": "[keyVaultURL]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    }
  }
}
```

Usando `AADClientCertificate`:

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2019-07-01",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientCertificate": "[aadClientCertificate]",
      "Passphrase": "[passphrase]"
    },
    "publisher": "Microsoft.Azure.Security",
    "type": "AzureDiskEncryptionForLinux",
    "typeHandlerVersion": "0.1",
    "settings": {
      "AADClientID": "[aadClientID]",
      "DiskFormatQuery": "[diskFormatQuery]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KeyVaultURL": "[keyVaultURL]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    }
  }
}
```


### <a name="property-values"></a>Valores de propriedade

| Nome | Valor/Exemplo | Tipo de Dados |
| ---- | ---- | ---- |
| apiVersion | 2019-07-01 | date |
| publicador | Microsoft.Azure.Security | string |
| type | AzureDiskEncryptionForLinux | string |
| typeHandlerVersion | 1.1, 0.1 | INT |
| (0,1 esquema) Aadclientid | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | guid | 
| (0,1 esquema) AADClientSecret | password | string |
| (0,1 esquema) Certificado AADClient | thumbprint | string |
| (opcional) (0,1 esquema) Senha | password | string |
| DiskFormatQuery | {"dev_path":"","name":"","file_system":""} | Dicionário JSON |
| EncryptionOperation | EnableEncryption, EnableEncryptionFormatAll | string | 
| (opcional - RSA-OAEP padrão ) Keyencryptionalgorithm | 'RSA-OAEP', 'RSA-OAEP-256', 'RSA1_5' | string |
| KeyVaultURL | url | string |
| KeyVaultResourceId | url | string |
| (opcional) KeyEncryptionKeyURL | url | string |
| (opcional) KekVaultResourceId | url | string |
| (opcional) Versão seqüencial | UNIQUEIDENTIFIER | string |
| VolumeType | Sistema operacional, Dados, Tudo | string |

## <a name="template-deployment"></a>Implantação de modelo

Para obter um exemplo de implantação de modelo baseada no esquema v1.1, consulte o Azure Quickstart Template [201-encrypt-running-linux-vm-without-aad](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm-without-aad).

Para obter um exemplo de implantação de modelo baseada no esquema v0.1, consulte o Azure Quickstart Template [201-encrypt-running-linux-vm](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm).

>[!WARNING]
> - Se você já usou a Criptografia de Disco Do Azure com o Azure AD para criptografar uma VM, você deve continuar usando essa opção para criptografar sua VM.
> - Ao criptografar os volumes do Sistema Operacional Linux, o VM deve ser considerado indisponível. Recomendamos fortemente evitar logins SSH enquanto a criptografia estiver em andamento para evitar problemas que bloqueiem quaisquer arquivos abertos que precisarão ser acessados durante o processo de criptografia. Para verificar o progresso, use o [cmdlet Get-AzVMDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) PowerShell ou o comando [vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) CLI. Esse processo deve levar algumas horas para um volume do sistema operacional de 30 GB, mais algum tempo para criptografar volumes de dados. O tempo para criptografia de volume de dados será proporcional ao tamanho e à quantidade dos volumes de dados, a menos que a opção EncryptFormatAll seja usada. 
> - Desabilitar criptografia nas VMs do Linux tem suporte apenas para volumes de dados. Não haverá suporte em dados ou volumes de SO, se o volume de SO tiver sido criptografado. 

>[!NOTE]
> Além `VolumeType` disso, se o parâmetro estiver definido como All, os discos de dados serão criptografados somente se estiverem devidamente montados.

## <a name="troubleshoot-and-support"></a>Solução de problemas e suporte

### <a name="troubleshoot"></a>Solução de problemas

Para solução de problemas, consulte o [Guia de solução de problemas do Azure Disk Encryption](../linux/disk-encryption-troubleshooting.md).

### <a name="support"></a>Suporte

Se você precisar de mais ajuda em qualquer ponto deste artigo, você pode entrar em contato com os especialistas do Azure nos [fóruns MSDN Azure e Stack Overflow](https://azure.microsoft.com/support/community/). 

Como alternativa, você pode registrar um incidente de suporte do Azure. Vá para [o suporte do Azure](https://azure.microsoft.com/support/options/) e selecione Obter suporte. Para obter informações sobre como usar o Suporte ao Azure, leia o [FaQ de suporte do Microsoft Azure](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Próximas etapas

* Para obter mais informações sobre extensões de VM, consulte [Recursos e extensões da máquina virtual para Linux](features-linux.md).
* Para obter mais informações sobre o Azure Disk Encryption for Linux, consulte [máquinas virtuais Linux](../../security/fundamentals/azure-disk-encryption-vms-vmss.md#linux-virtual-machines).
