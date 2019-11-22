---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 11/20/2019
ms.custom: include file
ms.openlocfilehash: e22ca39536e8e5d216d786c9d3c473f13431859f
ms.sourcegitcommit: 8a2949267c913b0e332ff8675bcdfc049029b64b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/21/2019
ms.locfileid: "74307262"
---
O controle de acesso baseado em função é uma estratégia de segurança orientada por herança para o gerenciamento de acesso, permissões e funções. Funções descendentes herdam permissões das funções pai. As permissões também podem ser atribuídas sem serem herdadas de uma função pai. Eles também podem ser atribuídos para personalizar uma função conforme necessário.

Por exemplo, um administrador de espaço pode precisar de acesso global para executar todas as operações de um espaço especificado. O acesso inclui todos os nós abaixo ou dentro do espaço. Um instalador de dispositivo pode precisar apenas de *read* e *atualização* de permissões para dispositivos e sensores.

Em todos os casos, as funções são concedidas *exatamente e não mais do que o acesso necessário* para cumprir suas tarefas de acordo com o Princípio de Menor Privilégio. De acordo com esse princípio, uma identidade é concedida *apenas*:

* A extensão de acesso necessária para concluir o trabalho.
* Uma função apropriada e limitada para a realização do trabalho.

>[!IMPORTANT]
> Siga sempre o Princípio do Menor Privilégio.

Duas outras importantes práticas de controle de acesso baseadas em função devem ser seguidas:

> [!div class="checklist"]
> * Auditar periodicamente as atribuições de função para verificar se cada função tem as permissões corretas.
> * Limpe funções e atribuições quando os indivíduos mudam de função ou atribuição.