---
title: 'Copiar e colar de e para uma máquina virtual: Azure Bastion'
description: Neste artigo, aprenda como copiar e colar de e para um Azure VM usando Bastion.
services: bastion
author: charwen
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: charwen
ms.openlocfilehash: 0af0fdfa1b0bcbb06d9424390e7ca8c8f406bcb3
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619313"
---
# <a name="copy-and-paste-to-a-virtual-machine-azure-bastion"></a>Copiar e colar em uma máquina virtual: Azure Bastion

Este artigo ajuda você a copiar e colar textos de e para máquinas virtuais ao usar o Azure Bastion. Antes de trabalhar com uma VM, certifique-se de ter seguido os passos para [criar um host Bastião](bastion-create-host-portal.md). Em seguida, conecte-se à VM com a que deseja trabalhar usando [RDP](bastion-connect-vm-rdp.md) ou [SSH](bastion-connect-vm-ssh.md).

Para navegadores que suportam o acesso avançado à API da área de transferência, você pode copiar e colar texto entre seu dispositivo local e a sessão remota da mesma forma que você copia e cola entre aplicativos em seu dispositivo local. Para outros navegadores, você pode usar a paleta de ferramentas de acesso à área de transferência Bastion.

   ![Permitir área de transferência](./media/bastion-vm-manage/allow.png)

Apenas a cópia/pasta de texto é suportada. Para copiar e colar diretamente, seu navegador pode solicitar acesso à área de transferência quando a sessão Bastion estiver sendo inicializada. **Permita que** a página da Web acesse a área de transferência.

## <a name="copy-to-a-remote-session"></a><a name="to"></a>Copiar para uma sessão remota

Depois de se conectar à máquina virtual usando o [portal Azure, ](https://portal.azure.com)complete as seguintes etapas:

1. Copie o texto/conteúdo do dispositivo local para a área de transferência local.
1. Durante a sessão remota, inicie a paleta de ferramentas de acesso à área de transferência Bastion selecionando as duas setas. As setas estão localizadas no centro esquerdo da sessão.

   ![paleta de ferramentas](./media/bastion-vm-manage/left.png)

   ![área de transferência](./media/bastion-vm-manage/clipboard.png)
1. Normalmente, o texto copiado é automaticamente mostrado na paleta de colares de cópia bastião. Se o texto não estiver lá, cole o texto na área de texto na paleta.
1. Uma vez que o texto esteja na área de texto, você pode colá-lo na sessão remota.

   ![colar](./media/bastion-vm-manage/local.png)

## <a name="copy-from-a-remote-session"></a><a name="from"></a>Copiar de uma sessão remota

Depois de se conectar à máquina virtual usando o [portal Azure, ](https://portal.azure.com)complete as seguintes etapas:

1. Copie o texto/conteúdo da sessão remota para a área de transferência remota (usando Ctrl-C).

   ![paleta de ferramentas](./media/bastion-vm-manage/remote.png)
1. Durante a sessão remota, inicie a paleta de ferramentas de acesso à área de transferência Bastion selecionando as duas setas. As setas estão localizadas no centro esquerdo da sessão.

   ![área de transferência](./media/bastion-vm-manage/clipboard2.png)
1. Normalmente, o texto copiado é automaticamente mostrado na paleta de colares de cópia bastião. Se o texto não estiver lá, cole o texto na área de texto na paleta.
1. Uma vez que o texto esteja na área de texto, você pode colá-lo no dispositivo local.

   ![colar](./media/bastion-vm-manage/local2.png)
 
## <a name="next-steps"></a>Próximas etapas

Leia o [Bastião FAQ](bastion-faq.md).