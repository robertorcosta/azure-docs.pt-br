---
title: Habilitar e criar compartilhamentos de arquivos grandes-arquivos do Azure
description: Neste artigo, você aprenderá a habilitar e a criar grandes compartilhamentos de arquivos.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 00db5905c008644bc21704179363849756fa7dcc
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72518415"
---
# <a name="how-to-enable-and-create-large-file-shares"></a>Como habilitar e criar compartilhamentos de arquivos grandes

Originalmente, os compartilhamentos de arquivos padrão só poderiam ser escalados para até 5 TiB, agora, com grandes compartilhamentos de arquivos, eles podem ser escalados verticalmente para 100 TiB. Os compartilhamentos de arquivos Premium são escalados verticalmente até 100 TiB por padrão. 

Para escalar verticalmente até 100 TiB usando compartilhamentos de arquivos padrão, você deve habilitar sua conta de armazenamento para usar grandes compartilhamentos de arquivos. Você pode habilitar uma conta existente ou criar uma nova conta para usar compartilhamentos de arquivos grandes.

## <a name="prerequisites"></a>Pré-requisitos

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="restrictions"></a>Quanto

As contas habilitadas para compartilhamentos de arquivos grandes dão suporte a LRS ou ZRS. Por enquanto, as contas habilitadas para compartilhamentos de arquivos grandes não dão suporte a GZRS, GRS ou RA-GRS. Habilitar grandes compartilhamentos de arquivos em uma conta é um processo irreversível, uma vez habilitado, sua conta não pode ser convertida em GZRS, GRS ou RA-GRS.

## <a name="create-a-new-storage-account"></a>Criar uma nova conta de armazenamento

Entre no [portal do Azure](https://portal.azure.com).

1. Na portal do Azure, selecione **todos os serviços**. Na lista de recursos, digite **contas de armazenamento**. Quando você começa a digitar, a lista é filtrada com base em sua entrada. Selecione **Contas de Armazenamento**.
1. Na janela **contas de armazenamento** que aparece, escolha **Adicionar**.
1. Selecione a assinatura na qual criar a conta de armazenamento.
1. No campo **grupo de recursos** , selecione **criar novo**. Insira um nome para o novo grupo de recursos, conforme mostrado na imagem a seguir.

    ![Captura de tela mostrando como criar um grupo de recursos no portal](media/storage-files-how-to-create-large-file-share/create-large-file-share.png)

1. Em seguida, insira um nome para sua conta de armazenamento. O nome escolhido deve ser exclusivo no Azure. O nome também deve ter entre 3 e 24 caracteres de comprimento e pode incluir apenas números e letras minúsculas.
1. Selecione um local para sua conta de armazenamento, verifique se ele é [uma das regiões com suporte para LFS](storage-files-planning.md#regional-availability).
1. Defina a replicação para o **armazenamento** com redundância local ou **armazenamento com redundância de zona**.
1. Deixe esses campos definidos com seus valores padrão:

   |campo  |Valor  |
   |---------|---------|
   |Modelo de implantação     |Resource Manager         |
   |Performance     |Standard         |
   |Tipo de conta     |StorageV2 (uso geral v2)         |
   |Camada de acesso     |Quente         |

1. Selecione **avançado** e selecione **habilitado** para **compartilhamentos de arquivos grandes**.
1. Selecione **examinar + criar** para revisar as configurações da conta de armazenamento e criar a conta.

    ![Large-File-shares-Advanced-Enable. png](media/storage-files-how-to-create-large-file-share/large-file-shares-advanced-enable.png)

1. Selecione **Criar**.

## <a name="enable-on-existing-account"></a>Habilitar na conta existente

Você também pode habilitar compartilhamentos de arquivos grandes em contas existentes. Se você fizer isso, a conta não será mais capaz de converter em GZRS, GRS ou RA-GRS. Essa opção é irreversível nessa conta.

1. Abra o [portal do Azure](https://portal.azure.com) e navegue até a conta de armazenamento na qual você deseja habilitar compartilhamentos de arquivos grandes.
1. Abra a conta de armazenamento e selecione **configuração**.
1. Selecione **habilitado** em **compartilhamentos de arquivos grandes**e, em seguida, selecione salvar.
1. Selecione **visão geral** e selecione **Atualizar**.

![Enable-Large-File-shares-on-existing. png](media/storage-files-how-to-create-large-file-share/enable-large-file-shares-on-existing.png)

Agora você habilitou grandes compartilhamentos de arquivos em sua conta de armazenamento.

Se você receber o seguinte erro: "grandes compartilhamentos de arquivos não estão disponíveis para a conta ainda". Você pode aguardar algum tempo, pois sua região provavelmente está no meio da conclusão da distribuição ou, se você tiver necessidades urgentes, entre em contato com o suporte.

## <a name="create-a-large-file-share"></a>Criar um compartilhamento de arquivos grande

A criação de um compartilhamento de arquivos grande é quase idêntica à criação de um compartilhamento de arquivos padrão. A principal diferença é que você pode definir uma cota de até 100 TiB.

1. Em sua conta de armazenamento, selecione **compartilhamentos de arquivos**.
1. Selecione **+ compartilhamento de arquivos**.
1. Insira um nome para o compartilhamento de arquivos e (opcionalmente) o tamanho da cota que você deseja, até 100 TiB e, em seguida, selecione **criar**. 

![Large-File-shares-Create-share. png](media/storage-files-how-to-create-large-file-share/large-file-shares-create-share.png)

## <a name="expand-existing-file-shares"></a>Expandir compartilhamentos de arquivos existentes

Depois de habilitar grandes compartilhamentos de arquivos em sua conta de armazenamento, você poderá expandir os compartilhamentos existentes para a cota mais alta.

1. Em sua conta de armazenamento, selecione **compartilhamentos de arquivos**.
1. Clique com o botão direito do mouse no compartilhamento de arquivos e selecione **cota**.
1. Digite o novo tamanho desejado e selecione **OK**.

![Update-Large-File-Share-quota. png](media/storage-files-how-to-create-large-file-share/update-large-file-share-quota.png)

## <a name="next-steps"></a>Próximos passos

* [Conectar e montar o compartilhamento de arquivos-Windows](storage-how-to-use-files-windows.md)
* [Conectar e montar o compartilhamento de arquivos-Linux](../storage-how-to-use-files-linux.md)
* [Conectar e montar compartilhamento de arquivos – macOS](storage-how-to-use-files-mac.md)