---
title: Como corrigir manualmente os problemas de sincronização do ServiceNow
description: Redefinir a conexão ao ServiceNow para que os alertas no Microsoft Azure possam novamente chamar o ServiceNow
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 04/12/2020
ms.openlocfilehash: f09f5010c18f5ea064b02f0fbbae107bf473e1f8
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81313676"
---
# <a name="how-to-manually-fix-servicenow-sync-problems"></a>Como corrigir manualmente os problemas de sincronização do ServiceNow

O Azure Monitor pode se conectar a provedores de GERENCIAMENTO DE SERVIÇOS de TI (ITSM) de terceiros. O ServiceNow é um desses provedores.

Por razões de segurança, talvez seja necessário atualizar o token de autenticação usado para sua conexão com o ServiceNow.
Use o seguinte processo de sincronização para reativar a conexão e atualizar o token:


1. Procure a solução no banner de pesquisa superior e selecione as soluções relevantes

    ![Nova conexão](media/itsmc-resync-servicenow/solution-search-8bit.png)

1. Na tela da solução, escolha "Selecionar Tudo" no filtro de assinatura e, em seguida, filtre por "ServiceDesk"

    ![Nova conexão](media/itsmc-resync-servicenow/solutions-list-8bit.png)

1. Selecione a solução da sua conexão ITSM.
1. Selecione a conexão ITSM no banner esquerdo.

    ![Nova conexão](media/itsmc-resync-servicenow/itsm-connector-8bit.png)

1. Selecione cada conector da lista. 
    1. Clique no nome do Conector para configurá-lo
    1. Exclua quaisquer conectores que não estão mais em uso

    1. Atualize os campos de acordo com [essas definições](https://docs.microsoft.com/azure/azure-monitor/platform/itsmc-connections) com base no seu tipo de parceiro

    1. Clique em sincronização

       ![Nova conexão](media/itsmc-resync-servicenow/resync-8bit2.png)

    1. Clique em salvar

        ![Nova conexão](media/itsmc-resync-servicenow/save-8bit.png)

f.    Revise as notificações para ver se o processo terminou com sucesso 

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [conexões de gerenciamento de serviços de TI](itsmc-connections.md)
