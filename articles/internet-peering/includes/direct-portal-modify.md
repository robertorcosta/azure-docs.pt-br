---
title: incluir arquivo
titleSuffix: Azure
description: incluir arquivo
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: d9ff01cf0180dae7f75d9753ba889d0caddad937
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775336"
---
Esta seção descreve como executar as seguintes operações de modificação para peering direto:

### <a name="add-direct-peering-connections"></a>Adicionar conexões de peering direto
1. Clique no botão **+ Adicionar conexões** na parte superior e configurar uma nova conexão de peering.
    > [!div class="mx-imgBorder"]
    > ![Exibição de recursos de peering](../media/setup-direct-modify-addconnection.png)
1. Preencha o **formulário Conexão de peering direta** e clique **em Salvar**. Para obter ajuda na configuração de uma conexão de peering, revise as etapas na seção "Criar e provisionar um peering direto" acima.
    > [!div class="mx-imgBorder"]
    > ![Exibição de recursos de peering](../media/setup-direct-modify-savenewconnection.png)

### <a name="remove-direct-peering-connections"></a>Remova conexões de peering direto

A remoção de uma conexão não é suportada no portal no momento. Entre em contato com [a Microsoft .](mailto:peeringexperience@microsoft.com)

### <a name="upgrade-or-downgrade-bandwidth-on-active-connections"></a>Atualizar ou desfazer a largura de banda em conexões Ativas
1. Clique em uma conexão de peering que você deseja modificar e, em seguida, clique no **...**  > Editar botão **de conexão.**
    > [!div class="mx-imgBorder"]
    > ![Edição de conexão de peering](../media/setup-direct-modify-editconnection.png)
1. Modifique a largura de banda como mostrado abaixo e clique **em Salvar**.
    > [!div class="mx-imgBorder"]
    > ![Conexão de peering modificar largura de banda](../media/setup-direct-modify-editconnectionsettings.png)

### <a name="add-ipv4ipv6-session-on-active-connections"></a>Adicione a sessão IPv4/IPv6 em conexões ativas.
1. Clique em uma conexão de peering que você deseja modificar e, em seguida, clique no **...**  > Editar o botão **de conexão** como mostrado acima.
1. Adicione o **prefixo Session IPv4** ou as informações **do prefixo session IPv6** e clique **em Salvar**.

### <a name="remove-ipv4ipv6-session-on-active-connections"></a>Remova a sessão IPv4/IPv6 em conexões ativas.
    Removing a **Session IPv4 prefix** or **Session IPv6 prefix** info is not currently supported on portal. Contact [Microsoft peering](mailto:peeringexperience@microsoft.com).
