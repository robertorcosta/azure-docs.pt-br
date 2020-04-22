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
ms.openlocfilehash: e5804aa1b005e670d8b430b1c0a3bd62efd0bb06
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687037"
---
1. Selecione uma conexão de peering que deseja habilitar para o Azure Peering Service. Então **selecione...**  >  **Editar conexão**.
    > [!div class="mx-imgBorder"]
    > ![Conexão de conexão de peering Editar conexão](../media/setup-direct-modify-editconnection.png)
1. Em **Uso para serviço de peering,** selecione Ativado e **selecione** **Salvar**.
    > [!div class="mx-imgBorder"]
    > ![Conexão de peering habilita risque o serviço peering](../media/setup-direct-modify-editconnectionsettings-peering-service.png)
1. Na **tela Visão geral,** você vê os detalhes de implantação. Depois que sua implantação for concluída, **selecione Ir para recurso**.
    > [!div class="mx-imgBorder"]
    > ![Sua implantação está completa](../media/setup-direct-modify-overview-deployment-complete.png)

1. No painel **Prefixos registrados,** selecione **Adicionar prefixo registrado**.
    > [!div class="mx-imgBorder"]
    > ![Adicionar prefixo registrado](../media/setup-direct-modify-add-registered-prefix.png)
1. Registre um prefixo selecionando um **Nome** e um **Prefixo** e selecionando **Salvar**.
    > [!div class="mx-imgBorder"]
    >  ![Registre um prefixo](../media/setup-direct-modify-register-a-prefix.png) 

1. Depois que um prefixo é criado, você vê-lo na lista de **Prefixos Registrados**. Selecione o **nome** do prefixo para ver mais detalhes.
    > [!div class="mx-imgBorder"]
    > ![Prefixos e conexões registrados](../media/setup-direct-modify-registered-prefixes.png)
1. Na página de prefixo cadastrado, você vê os detalhes completos, que incluem a **tecla Prefixpara** cada prefixo. Esta chave deve ser fornecida ao cliente alocado este prefixo de seu provedor isp. O cliente pode então registrar seu prefixo em sua assinatura usando esta chave.
    > [!div class="mx-imgBorder"]
    > ![Prefixo com chave prefixo](../media/setup-direct-modify-registered-prefix-detail.png)