---
title: incluir arquivo
description: incluir arquivo
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 09/25/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 5104c3b4446a8d3747ce7cc1648ef05dd117eb3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "74013790"
---
O Storage Explorer 1.10.0 permite que os usuários carreguem, baixem e copiem discos gerenciados, bem como criem snapshots. Por causa desses recursos adicionais, você pode usar o Storage Explorer para migrar dados de locais para o Azure e migrar dados através das regiões do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este artigo, você precisará do seguinte:
- Uma assinatura do Azure
- Um ou mais discos gerenciados do Azure
- A versão mais recente do [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)

## <a name="connect-to-an-azure-subscription"></a>Conectar-se a uma assinatura do Azure

Se o seu Explorador de Armazenamento não estiver conectado ao Azure, você não poderá usá-lo para gerenciar recursos. Esta seção passa por cima de conectá-la à sua conta do Azure para que você possa gerenciar recursos usando o Storage Explorer.

1. Inicie o Azure Storage Explorer e clique no ícone **de plug-in** à esquerda.

    ![Clique no ícone de plug-in](media/disks-upload-vhd-to-managed-disk-storage-explorer/plug-in-icon.png)

1. Selecione **Adicionar uma conta azure**e, em seguida, clique **em Avançar**.

    ![Adicionar uma conta do Azure](media/disks-upload-vhd-to-managed-disk-storage-explorer/connect-to-azure.png)

1. Na caixa de diálogo **Azure Sign in,** insira suas credenciais do Azure.

    ![Sinal azure em diálogo](media/disks-upload-vhd-to-managed-disk-storage-explorer/sign-in.png)

1. Selecione sua assinatura na lista e, em seguida, clique em **Aplicar**.

    ![Selecione sua assinatura](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-subscription.png)

## <a name="upload-a-managed-disk-from-an-on-prem-vhd"></a>Faça upload de um disco gerenciado a partir de um VHD on-prem

1. No painel esquerdo, expanda **discos** e selecione o grupo de recursos para o que deseja carregar seu disco.

    ![Selecione o grupo de recursos 1](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1.png)

1. Selecione **Carregar**.

    ![Selecionar Carregar](media/disks-upload-vhd-to-managed-disk-storage-explorer/upload-button.png)

1. No **Upload VHD** especifique sua fonte VHD, o nome do disco, o tipo de sistema operacional, a região para a qual você deseja carregar o disco, bem como o tipo de conta. Em algumas regiões, as zonas de disponibilidade são suportadas, para essas regiões você pode selecionar uma zona de sua escolha.
1. Selecione **Criar** para começar a carregar seu disco.

    ![Carregar caixa de diálogo vhd](media/disks-upload-vhd-to-managed-disk-storage-explorer/upload-vhd-dialog.png)

1. O status do upload será agora exibido em **Atividades**.

    ![Status de upload](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-uploading.png)

1. Se o upload tiver terminado e você não ver o disco no painel direito, selecione **Atualizar**.

## <a name="download-a-managed-disk"></a>Baixe um disco gerenciado

As etapas a seguir explicam como baixar um disco gerenciado para um VHD on-prem. O estado de um disco deve ser **desconectado** para ser baixado, você não pode baixar um disco **anexado.**

1. No painel esquerdo, se ele ainda não estiver expandido, expanda **discos** e selecione o grupo de recursos do que deseja baixar seu disco.

    ![Selecione o grupo de recursos 1](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1.png)

1. No painel direito, selecione o disco que deseja baixar.
1. Selecione **Baixar** e escolha onde deseja salvar o disco.

    ![Baixe um disco gerenciado](media/disks-upload-vhd-to-managed-disk-storage-explorer/download-button.png)

1. Selecione **Salvar** e seu disco começará a ser baixado. O status do download será exibido em **Atividades**.

    ![Status de download](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-downloading.png)

## <a name="copy-a-managed-disk"></a>Copiar um disco gerenciado

Com o Storage Explorer, você pode copiar um disco manged dentro ou em todas as regiões. Para copiar um disco:

1. Na parada **de discos** à esquerda, selecione o grupo de recursos que contém o disco que deseja copiar.

    ![Selecione o grupo de recursos 1](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1.png)

1. No painel direito, selecione o disco que deseja copiar e selecione **Copiar**.

    ![Copiar um disco gerenciado](media/disks-upload-vhd-to-managed-disk-storage-explorer/copy-button.png)

1. No painel esquerdo, selecione o grupo de recursos que deseja colar o disco.

    ![Selecione o grupo de recursos 2](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg2.png)

1. Selecione **Colar** no painel direito.

    ![Cole um disco gerenciado](media/disks-upload-vhd-to-managed-disk-storage-explorer/paste-button.png)

1. Na caixa de diálogo **Colar disco,** preencha os valores. Você também pode especificar uma zona de disponibilidade em regiões suportadas.

    ![Caixa de diálogo de disco de colar](media/disks-upload-vhd-to-managed-disk-storage-explorer/paste-disk-dialog.png)

1. Selecione **Colar** e seu disco começará a copiar, o status será exibido em **Atividades**.

    ![Copiar o status da pasta](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-copying.png)

## <a name="create-a-snapshot"></a>Criar um instantâneo

1. Na queda de **discos** à esquerda, selecione o grupo de recursos que contém o disco que deseja fazer o snapshot.

    ![Selecione o grupo de recursos 1](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1.png)

1. À direita, selecione o disco que deseja fazer o instantâneo e selecione **Criar instantâneo**.

    ![Criar um instantâneo](media/disks-upload-vhd-to-managed-disk-storage-explorer/create-snapshot-button.png)

1. Em **Criar instantâneo,** especifique o nome do instantâneo, bem como o grupo de recursos em que deseja criá-lo. Em seguida, **selecione Criar**.

    ![Criar caixa de diálogo instantânea](media/disks-upload-vhd-to-managed-disk-storage-explorer/create-snapshot-dialog.png)

1. Uma vez que o snapshot tenha sido criado, você pode selecionar **Abrir no Portal** em **Atividades** para visualizar o instantâneo no portal Azure.

    ![Abra instantâneo no portal](media/disks-upload-vhd-to-managed-disk-storage-explorer/open-in-portal.png)

## <a name="next-steps"></a>Próximas etapas
