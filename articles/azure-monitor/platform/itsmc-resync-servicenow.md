---
title: Como corrigir manualmente problemas de sincronização do ServiceNow
description: Redefina a conexão com o ServiceNow para que os alertas no Microsoft Azure possam chamar o ServiceNow novamente
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 04/12/2020
ms.openlocfilehash: 2a9a8f3fe7422468ff2c8886dff7415322cf70f0
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92676868"
---
# <a name="how-to-manually-fix-servicenow-sync-problems"></a>Como corrigir manualmente problemas de sincronização do ServiceNow

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

f.    Examine as notificações para ver se o processo foi concluído com êxito 

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [as conexões de gerenciamento de serviços de ti](itsmc-connections.md)
