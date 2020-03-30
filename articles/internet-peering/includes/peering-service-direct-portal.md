---
title: incluir arquivo
titleSuffix: Azure
description: incluir arquivo
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: include
ms.date: 3/18/2020
ms.author: derekol
ms.openlocfilehash: 60752cf1b3c05ab7817083e70310ba7b40227dec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80129915"
---
1. Clique em uma conexão de peering que você deseja ativar para peering Service e, em seguida, clique no **...**  > Editar botão **de conexão.**
    > [!div class="mx-imgBorder"]
    > ![Edição de conexão de peering](../media/setup-direct-modify-editconnection.png)
1. Na seção ***Use for Peering Service***, clique em **Ativado** e **Salve**.
    > [!div class="mx-imgBorder"]
    > ![Conexão de peering habilita risque o serviço peering](../media/setup-direct-modify-editconnectionsettings-peering-service.png)
1. Na tela Visão geral, você verá os detalhes de implantação. Uma vez que sua implantação esteja concluída, clique em **Ir para recurso**.
    > [!div class="mx-imgBorder"]
    > ![Sua implantação está completa](../media/setup-direct-modify-overview-deployment-complete.png)

1. Em seguida, você verá em Configurações **Prefixos registrados**. Clique em **Adicionar prefixo registrado**.
    > [!div class="mx-imgBorder"]
    > ![Prefixos e Conexões Registrados](../media/setup-direct-modify-add-registered-prefix.png)
1. Registre um prefixo selecionando um **Nome** e um **Prefixo** e clique **em Salvar**
    > [!div class="mx-imgBorder"]
    >  ![Registre um prefixo](../media/setup-direct-modify-register-a-prefix.png) 

1. Uma vez que um prefixo é criado, você vai vê-lo na lista de Prefixos Registrados. Clique no **nome** do prefixo para ver mais detalhes.
    > [!div class="mx-imgBorder"]
    > ![Prefixos e Conexões Registrados](../media/setup-direct-modify-registered-prefixes.png)
1. Na página de prefixo cadastrado, você verá os detalhes completos para incluir a **chave Prefixo** para cada prefixo. Essa chave precisará ser fornecida ao cliente alocado neste prefixo do provedor ISP. O cliente pode então registrar seu prefixo dentro de sua assinatura com esta chave.
    > [!div class="mx-imgBorder"]
    > ![Prefixo com chave prefixo](../media/setup-direct-modify-registered-prefix-detail.png)