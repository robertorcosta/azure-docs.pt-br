---
title: Como corrigir manualmente problemas de sincronização do ServiceNow
description: Redefina a conexão com o ServiceNow para que os alertas no Microsoft Azure possam chamar o ServiceNow novamente
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 01/17/2021
ms.openlocfilehash: aede7e3dec886d6a6213c64b386cacd725dd74f5
ms.sourcegitcommit: 61d2b2211f3cc18f1be203c1bc12068fc678b584
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/18/2021
ms.locfileid: "98562787"
---
# <a name="how-to-manually-fix-sync-problems"></a>Como corrigir manualmente problemas de sincronização

Azure Monitor pode se conectar a provedores de gerenciamento de serviços de ti (ITSM) de terceiros. O ServiceNow é um desses provedores.

Por motivos de segurança, talvez seja necessário atualizar o token de autenticação usado para sua conexão com o ServiceNow.
Use o seguinte processo de sincronização para reativar a conexão e atualizar o token:

1. Pesquise a solução na faixa de pesquisa superior e, em seguida, selecione as soluções relevantes

    ![Captura de tela que mostra a faixa de pesquisa superior e onde selecionar as soluções relevantes.](media/itsmc-resync-servicenow/solution-search-8bit.png)

1. Na tela de solução, escolha "selecionar tudo" no filtro de assinatura e, em seguida, filtre por "assistência técnica"

    ![Captura de tela que mostra onde escolher Selecionar tudo e onde filtrar pela assistência técnica.](media/itsmc-resync-servicenow/solutions-list-8bit.png)

1. Selecione a solução de sua conexão de ITSM.
1. Selecione conexão de ITSM na faixa à esquerda.

    ![Captura de tela que mostra onde selecionar as conexões de ITSM.](media/itsmc-resync-servicenow/itsm-connector-8bit.png)

1. Selecione cada conector na lista. 
    1. Clique no nome do conector para configurá-lo
    1. Excluir todos os conectores que não estão mais em uso

    1. Atualizar os campos de acordo com [essas definições](./itsmc-connections.md) com base em seu tipo de parceiro

    1. Clique em sincronizar

       ![Captura de tela que realça o botão Sincronizar.](media/itsmc-resync-servicenow/resync-8bit2.png)

    1. Clique em salvar

        ![Nova conexão](media/itsmc-resync-servicenow/save-8bit.png)

f.    Examine as notificações para ver se o processo foi iniciado.
