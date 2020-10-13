---
title: Stop/Start-portal do Azure-banco de dados do Azure para MySQL servidor flexível
description: Este artigo descreve como parar/iniciar operações no banco de dados do Azure para MySQL por meio do portal do Azure.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: how-to
ms.date: 09/29/2020
ms.openlocfilehash: e3e08ae9bbf00e1c9a44e6ba913cac1d842928b7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91567461"
---
# <a name="stopstart-an-azure-database-for-mysql---flexible-server-preview"></a>Parar/iniciar um banco de dados do Azure para MySQL – servidor flexível (visualização)

> [!IMPORTANT]
> O Banco de Dados do Azure para MySQL – Servidor Flexível está atualmente na versão prévia pública.

Este artigo fornece um procedimento passo a passo para executar a interrupção e o início do servidor flexível.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de instruções, você precisa:

-   Você deve ter um servidor flexível do banco de dados do Azure para MySQL.

## <a name="stop-a-running-server"></a>Parar um servidor em execução

1.  Na [portal do Azure](https://portal.azure.com/), escolha o servidor flexível que você deseja parar.

2.  Na página **visão geral** , clique no botão **parar** na barra de ferramentas.
    
    :::image type="content" source="media/how-to-stop-start-server-portal/stop-server.png" alt-text="Pare o servidor flexível."::: 

3.  Clique em **Sim** para confirmar a interrupção do servidor.

    :::image type="content" source="media/how-to-stop-start-server-portal/confirm-stop.png" alt-text="Pare o servidor flexível."::: 

> [!NOTE]
> Depois que o servidor for interrompido, as outras operações de gerenciamento não estarão disponíveis para o servidor flexível.

## <a name="start-a-stopped-server"></a>Iniciar um servidor interrompido

1.  Na [portal do Azure](https://portal.azure.com/), escolha o servidor flexível que você deseja iniciar.

2.  Na página **visão geral** , clique no botão **Iniciar** na barra de ferramentas.

    :::image type="content" source="media/how-to-stop-start-server-portal/start-server.png" alt-text="Pare o servidor flexível.":::  

> [!NOTE]
> Depois que o servidor é iniciado, todas as operações de gerenciamento agora estão disponíveis para o servidor flexível.

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre a [rede no banco de dados do Azure para MySQL servidor flexível](./concepts-networking.md)
- [Crie e gerencie uma rede virtual do Servidor Flexível do Banco de Dados do Azure para MySQL usando o portal do Azure](./how-to-manage-virtual-network-portal.md).

