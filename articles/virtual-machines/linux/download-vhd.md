---
title: Baixar um VHD do Linux por meio do Azure
description: Baixe um VHD do Linux usando a CLI do Azure e o portal do Azure.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 08/03/2020
ms.author: cynthn
ms.openlocfilehash: 897cae53e589f4058e5499c0e6e941d4f1d9bb2f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87761035"
---
# <a name="download-a-linux-vhd-from-azure"></a>Baixar um VHD do Linux por meio do Azure

Neste artigo, você aprende a baixar um arquivo de VHD (disco rígido virtual) do Linux do Azure usando o portal do Azure. 

## <a name="stop-the-vm"></a>Pare a VM.

Não é possível baixar um VHD por meio do Azure se ele estiver anexado a uma VM em execução. Você precisa parar a VM para baixar o VHD. 

1.  Entre no [portal do Azure](https://portal.azure.com/).
2.  No menu à esquerda, selecione **máquinas virtuais**.
3.  Selecione a VM na lista.
4.  Na página da VM, selecione **parar**.

    :::image type="content" source="./media/download-vhd/export-stop.PNG" alt-text="Mostra o botão de menu para parar a VM.":::

## <a name="generate-sas-url"></a>Gerar a URL de SAS

Para baixar o arquivo VHD, você precisa gerar uma URL de [SAS (assinatura de acesso compartilhado)](../../storage/common/storage-sas-overview.md?toc=/azure/virtual-machines/windows/toc.json). Quando a URL é gerada, uma hora de expiração é atribuída à URL.

1. No menu da página da VM, selecione **discos**.
2. Selecione o disco do sistema operacional para a VM e, em seguida, selecione **exportação de disco**.
1. Se necessário, atualize o valor da **URL expira em (segundos)** para fornecer tempo suficiente para concluir o download. O padrão é 3600 segundos (uma hora).
3. Selecione **gerar URL**.
 
      
## <a name="download-vhd"></a>Baixar o VHD

1.  Na URL que foi gerada, selecione **baixar o arquivo VHD**.

    :::image type="content" source="./media/download-vhd/export-download.PNG" alt-text="Mostra o botão de menu para parar a VM.":::

2.  Talvez seja necessário selecionar **salvar** no navegador para iniciar o download. O nome padrão do arquivo VHD é *abcd*.

## <a name="next-steps"></a>Próximas etapas

- Saiba como [carregar e criar uma VM do Linux com base em um disco personalizado com a CLI do Azure](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 
- [Gerenciar discos do Azure com a CLI do Azure](tutorial-manage-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
