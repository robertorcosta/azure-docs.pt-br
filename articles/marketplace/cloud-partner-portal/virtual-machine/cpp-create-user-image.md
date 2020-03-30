---
title: Crie uma imagem VM do usuário para o Azure Marketplace
description: Lista as etapas e as referências necessárias para criar uma imagem de VM de usuário.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: dsindona
ms.openlocfilehash: 49db8c6717cd26886c3b49f8c99fdd2b08e8713d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80278000"
---
# <a name="create-a-user-vm-image"></a>Criar uma imagem VM de usuário

Este artigo explica as duas etapas gerais necessárias para criar uma imagem não gerenciada a partir de um VHD generalizado.  Há referências para orientar em cada etapa: capturar a imagem e generalizar a imagem.


## <a name="capture-the-vm-image"></a>Captura da imagem da VM

Use as instruções no artigo a seguir sobre como capturar a VM que corresponde à sua abordagem de acesso:

-  PowerShell: [Como criar uma imagem VM não gerenciada de uma VM do Azure](../../../virtual-machines/windows/capture-image-resource.md)
-  CLI do Azure: [Como criar uma imagem de uma máquina virtual ou um VHD](../../../virtual-machines/linux/capture-image.md)
-  API: [Máquinas Virtuais – Capturar](https://docs.microsoft.com/rest/api/compute/virtualmachines/capture)


## <a name="generalize-the-vm-image"></a>Generalizar a imagem VM

Porque você gerou a imagem de usuário de um VHD generalizado anteriormente, a imagem também deve ser generalizada.  Novamente, selecione o artigo a seguir que corresponde ao seu mecanismo de acesso.  (você pode ter já generalizado seu disco quando você o capturou)

-  PowerShell: [Generalizar a VM](https://docs.microsoft.com/azure/virtual-machines/windows/sa-copy-generalized#generalize-the-vm)
-  CLI do Azure: [Etapa 2: criar imagem VM](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image#step-2-create-vm-image)
-  API: [Máquinas Virtuais – Generalizar](https://docs.microsoft.com/rest/api/compute/virtualmachines/generalize)


## <a name="next-steps"></a>Próximas etapas

Em seguida, você irá [criar um certificado](cpp-create-key-vault-cert.md) e armazená-lo em um novo Azure Key Vault.  É necessário um certificado para estabelecer uma conexão segura do WinRM com a VM.
