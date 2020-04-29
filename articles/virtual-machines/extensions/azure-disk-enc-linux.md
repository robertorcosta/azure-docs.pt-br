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
ms.date: 03/19/2020
ms.author: ejarvi
ms.openlocfilehash: 22568c7c23771f143f6cd583114949c380d15e3d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80066910"
---
# <a name="azure-disk-encryption-for-linux-microsoftazuresecurityazurediskencryptionforlinux"></a>Azure Disk Encryption para Linux (Microsoft.Azure.Security.AzureDiskEncryptionForLinux)

## <a name="overview"></a>Visão geral

Azure Disk Encryption aproveita o subsistema de dm-crypt no Linux para fornecer criptografia de disco completo em [selecionar distribuições de Linux do Azure](https://aka.ms/adelinux).  A solução é integrada ao Azure Key Vault para gerenciar as chaves de criptografia de disco e segredos.

## <a name="prerequisites"></a>Pré-requisitos

Para obter uma lista completa de pré-requisitos, consulte [Azure Disk Encryption para VMs do Linux](../linux/disk-encryption-overview.md), especificamente as seguintes seções:

- [VMs e sistemas operacionais com suporte](../linux/disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Requisitos adicionais de VM](../linux/disk-encryption-overview.md#additional-vm-requirements)
- [Requisitos de rede](../linux/disk-encryption-overview.md#networking-requirements)
- [Requisitos de armazenamento de chave de criptografia](../linux/disk-encryption-overview.md#encryption-key-storage-requirements)

## <a name="extension-schema"></a>Esquema de Extensão

Há duas versões do esquema de extensão para Azure Disk Encryption (ADE):
- v 1.1-um esquema mais recente recomendado que não usa as propriedades Azure Active Directory (AAD).
- v 0.1-um esquema mais antigo que requer Propriedades Azure Active Directory (AAD). 

Para selecionar um esquema de destino, `typeHandlerVersion` a propriedade deve ser definida igual à versão do esquema que você deseja usar.

### <a name="schema-v11-no-aad-recommended"></a>Esquema v 1.1: sem AAD (recomendado)

O esquema v 1.1 é recomendado e não requer as propriedades Azure Active Directory (AAD).

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


### <a name="schema-v01-with-aad"></a>Esquema v 0,1: com o AAD 

O esquema 0,1 requer `AADClientID` o e `AADClientSecret` o `AADClientCertificate`ou o.

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

| Name | Valor/Exemplo | Tipo de Dados |
| ---- | ---- | ---- |
| apiVersion | 2019-07-01 | date |
| editor | Microsoft.Azure.Security | cadeia de caracteres |
| type | AzureDiskEncryptionForLinux | cadeia de caracteres |
| typeHandlerVersion | 1,1, 0,1 | INT |
| (esquema 0,1) AADClientID | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | guid | 
| (esquema 0,1) AADClientSecret | password | cadeia de caracteres |
| (esquema 0,1) AADClientCertificate | thumbprint | cadeia de caracteres |
| adicional (esquema 0,1) Senha | password | cadeia de caracteres |
| DiskFormatQuery | {"dev_path":"","name":"","file_system":""} | Dicionário JSON |
| EncryptionOperation | EnableEncryption, EnableEncryptionFormatAll | cadeia de caracteres | 
| (opcional-padrão RSA-OAEP) KeyEncryptionAlgorithm | 'RSA-OAEP', 'RSA-OAEP-256', 'RSA1_5' | cadeia de caracteres |
| KeyVaultURL | url | cadeia de caracteres |
| KeyVaultResourceId | url | cadeia de caracteres |
| adicional KeyEncryptionKeyURL | url | cadeia de caracteres |
| adicional KekVaultResourceId | url | cadeia de caracteres |
| adicional SequenceVersion | UNIQUEIDENTIFIER | cadeia de caracteres |
| VolumeType | Sistema operacional, Dados, Tudo | cadeia de caracteres |

## <a name="template-deployment"></a>Implantação de modelo

Para obter um exemplo de implantação de modelo com base no esquema v 1.1, consulte o modelo de início rápido do Azure [201-Encrypt-running-Linux-VM-sem-AAD](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm-without-aad).

Para obter um exemplo de implantação de modelo com base no esquema v 0.1, consulte o modelo de início rápido do Azure [201-Encrypt-running-Linux-VM](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm).

>[!WARNING]
> - Se você tiver usado anteriormente Azure Disk Encryption com o Azure AD para criptografar uma VM, você deve continuar usando essa opção para criptografar sua VM.
> - Ao criptografar volumes do sistema operacional Linux, a VM deve ser considerada indisponível. É altamente recomendável evitar logons SSH enquanto a criptografia estiver em andamento para evitar problemas ao bloquear os arquivos abertos que precisarão ser acessados durante o processo de criptografia. Para verificar o progresso, use o cmdlet do PowerShell [Get-AzVMDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) ou o comando [VM Encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) CLI. Esse processo deve levar algumas horas para um volume do sistema operacional de 30 GB, mais algum tempo para criptografar volumes de dados. O tempo para criptografia de volume de dados será proporcional ao tamanho e à quantidade dos volumes de dados, a menos que a opção EncryptFormatAll seja usada. 
> - Desabilitar criptografia nas VMs do Linux tem suporte apenas para volumes de dados. Não haverá suporte em dados ou volumes de SO, se o volume de SO tiver sido criptografado. 

>[!NOTE]
> Além disso `VolumeType` , se o parâmetro for definido como todos, os discos de dados serão criptografados somente se forem montados corretamente.

## <a name="troubleshoot-and-support"></a>Solução de problemas e suporte

### <a name="troubleshoot"></a>Solucionar problemas

Para solução de problemas, consulte o [Guia de solução de problemas do Azure Disk Encryption](../linux/disk-encryption-troubleshooting.md).

### <a name="support"></a>Suporte

Se precisar de mais ajuda a qualquer momento neste artigo, você poderá entrar em contato com os especialistas do Azure nos [fóruns do Azure e do Stack Overflow do MSDN](https://azure.microsoft.com/support/community/). 

Como alternativa, você pode registrar um incidente de suporte do Azure. Vá para o [suporte do Azure](https://azure.microsoft.com/support/options/) e selecione obter suporte. Para obter informações sobre como usar o suporte do Azure, leia as [perguntas frequentes sobre suporte do Microsoft Azure](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Próximas etapas

* Para obter mais informações sobre extensões de VM, consulte [Recursos e extensões da máquina virtual para Linux](features-linux.md).
* Para obter mais informações sobre Azure Disk Encryption para Linux, consulte [máquinas virtuais do Linux](../../security/fundamentals/azure-disk-encryption-vms-vmss.md#linux-virtual-machines).
