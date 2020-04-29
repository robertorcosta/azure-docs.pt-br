---
title: Como corrigir manualmente problemas de sincronização do ServiceNow
description: Redefina a conexão com o ServiceNow para que os alertas no Microsoft Azure possam chamar o ServiceNow novamente
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 04/12/2020
ms.openlocfilehash: f09f5010c18f5ea064b02f0fbbae107bf473e1f8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81313676"
---
# <a name="how-to-manually-fix-servicenow-sync-problems"></a>Como corrigir manualmente problemas de sincronização do ServiceNow

Azure Monitor pode se conectar a provedores de gerenciamento de serviços de ti (ITSM) de terceiros. O ServiceNow é um desses provedores.

Por motivos de segurança, talvez seja necessário atualizar o token de autenticação usado para sua conexão com o ServiceNow.
Use o seguinte processo de sincronização para reativar a conexão e atualizar o token:


1. Pesquise a solução na faixa de pesquisa superior e, em seguida, selecione as soluções relevantes

    ![Nova conexão](media/itsmc-resync-servicenow/solution-search-8bit.png)

1. Na tela de solução, escolha "selecionar tudo" no filtro de assinatura e, em seguida, filtre por "assistência técnica"

    ![Nova conexão](media/itsmc-resync-servicenow/solutions-list-8bit.png)

1. Selecione a solução de sua conexão de ITSM.
1. Selecione conexão de ITSM na faixa à esquerda.

    ![Nova conexão](media/itsmc-resync-servicenow/itsm-connector-8bit.png)

1. Selecione cada conector na lista. 
    1. Clique no nome do conector para configurá-lo
    1. Excluir todos os conectores que não estão mais em uso

    1. Atualizar os campos de acordo com [essas definições](https://docs.microsoft.com/azure/azure-monitor/platform/itsmc-connections) com base em seu tipo de parceiro

    1. Clique em sincronizar

       ![Nova conexão](media/itsmc-resync-servicenow/resync-8bit2.png)

    1. Clique em salvar

        ![Nova conexão](media/itsmc-resync-servicenow/save-8bit.png)

f.    Examine as notificações para ver se o processo foi concluído com êxito 

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [as conexões de gerenciamento de serviços de ti](itsmc-connections.md)
