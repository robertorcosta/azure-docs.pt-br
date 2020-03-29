---
title: Carregar arquivo VHD no Azure DevTest Labs usando o PowerShell | Microsoft Docs
description: Este artigo fornece um passo a passo que mostra como carregar um arquivo VHD para o Azure DevTest Labs usando o PowerShell.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: a9e5a69cd7840bbaff081a4d47cb60f62a76eda6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76759748"
---
# <a name="upload-vhd-file-to-labs-storage-account-using-powershell"></a>Carregar arquivo VHD na conta de armazenamento do laboratório usando o PowerShell

[!INCLUDE [devtest-lab-upload-vhd-selector](../../includes/devtest-lab-upload-vhd-selector.md)]

No Azure DevTest Labs, os arquivos VHD podem ser usados para criar imagens personalizadas, que são usadas para provisionar máquinas virtuais. As etapas a seguir mostrarão como usar o PowerShell para carregar um arquivo VHD em uma conta de armazenamento do laboratório. Depois de carregar seu arquivo VHD, a [seção Próximas etapas](#next-steps) lista alguns dos artigos que ilustram como criar uma imagem personalizada do arquivo VHD carregado. Para saber mais sobre os discos e VHDs do Azure, confira [Introdução aos discos gerenciados](../virtual-machines/linux/managed-disks-overview.md)

## <a name="step-by-step-instructions"></a>Instruções passo a passo

As etapas a seguir mostram como carregar um arquivo VHD no Azure DevTest Labs usando o PowerShell. 

1. Faça login no [portal Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Selecione **Todos os serviços**e selecione **DevTest Labs** na lista.

1. Na lista de laboratórios, selecione o laboratório desejado.  

1. Na folha do laboratório, selecione **Configuração**. 

1. Na folha **Configuração** do laboratório, selecione **Imagens personalizadas (VHDs)**.

1. Na **lâmina de imagens personalizadas,** Selecione **+Adicionar**. 

1. Na folha **Imagem personalizada**, selecione **VHD**.

1. Na folha **VHD**, selecione **Carregar um VHD usando o PowerShell**.

    ![Carregar o VHD usando o PowerShell](./media/devtest-lab-upload-vhd-using-powershell/upload-image-using-psh.png)

1. Na folha **Carregar uma imagem usando o PowerShell**, copie o script do PowerShell gerado em um editor de texto.

1. Modifique o parâmetro **LocalFilePath** do cmdlet **Add-AzureVhd** para apontar para o local do arquivo VHD que deseja carregar.

1. Em um prompt do PowerShell, execute o cmdlet **Add-AzureVhd** (com o parâmetro **LocalFilePath** modificado).

> [!WARNING] 
> 
> O processo de carregamento de um arquivo VHD pode ser demorado, dependendo do tamanho do arquivo VHD e da velocidade de conexão.

## <a name="next-steps"></a>Próximas etapas

- [Criar uma imagem personalizada no Azure DevTest Labs de um arquivo VHD usando o Portal do Azure](devtest-lab-create-template.md)
- [Criar uma imagem personalizada no Azure DevTest Labs de um arquivo VHD usando o PowerShell](devtest-lab-create-custom-image-from-vhd-using-powershell.md)
