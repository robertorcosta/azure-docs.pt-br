---
title: incluir arquivo
description: incluir arquivo
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: include
ms.date: 10/12/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: b079ede0845de3794507691bc3c252930e2a6604
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/13/2020
ms.locfileid: "91977144"
---
1. Abra o [Portal do Azure](https://portal.azure.com). Navegue até a máquina virtual à qual você deseja se conectar e selecione **Conectar**. Selecione **Bastion** no menu suspenso.

   :::image type="content" source="./media/bastion-vm-rdp/connect-vm.png" alt-text="Selecionar Bastion" border="false":::

1. Depois de selecionar o Bastion no menu suspenso, uma barra lateral será exibida com três guias: RDP, SSH e Bastion. Como o Bastion foi provisionado para a rede virtual, a guia Bastion está ativa por padrão. Selecione **Usar Bastion**.

   :::image type="content" source="./media/bastion-vm-rdp/use-bastion.png" alt-text="Selecionar Bastion" border="false":::

1. Na página **Conectar-se usando o Azure Bastion**, insira o nome de usuário e a senha para sua máquina virtual e selecione **Conectar**.

   :::image type="content" source="./media/bastion-vm-rdp/host.png" alt-text="Selecionar Bastion" border="false":::

1. A conexão RDP com essa máquina virtual via Bastion será aberta diretamente no portal do Azure (via HTML5) usando a porta 443 e o serviço Bastion.

   :::image type="content" source="./media/bastion-vm-rdp/connection.png" alt-text="Selecionar Bastion" border="false":::
