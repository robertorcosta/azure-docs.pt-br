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
ms.openlocfilehash: 3894bf046ed4ee3f068e43dbc5bc5b7f2a1002b4
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81681062"
---
Esta seção descreve como executar as seguintes operações de modificação para peering direto.

### <a name="add-direct-peering-connections"></a>Adicionar conexões de peering direto
1. Selecione o botão **+ Adicionar conexões** e configure uma nova conexão de peering.
    > [!div class="mx-imgBorder"]
    > ![Exibição de recursos de peering](../media/setup-direct-modify-addconnection.png)

1. Preencha o **formulário Conexão de peering direta** e selecione **Salvar**. Para obter ajuda na configuração de uma conexão de peering, revise as etapas na seção "Criar e provisionar um peering direto".
    > [!div class="mx-imgBorder"]
    > ![Formulário de conexão de peering direto](../media/setup-direct-modify-savenewconnection.png)

### <a name="remove-direct-peering-connections"></a>Remova conexões de peering direto

A remoção de uma conexão não é suportada no portal Azure. Para obter mais informações, entre em contato com [a Microsoft .](mailto:peeringexperience@microsoft.com)

### <a name="upgrade-or-downgrade-bandwidth-on-active-connections"></a>Atualizar ou desfazer a largura de banda em conexões Ativas
1. Selecione uma conexão de peering que você deseja modificar e, em seguida, selecione **...**  >  **Editar conexão**.
    > [!div class="mx-imgBorder"]
    > ![Editar conexão](../media/setup-direct-modify-editconnection.png)

1. Modifique a largura de banda movendo o controle deslizante e, em seguida, **selecione Salvar**.
    > [!div class="mx-imgBorder"]
    > ![Modificar a largura de banda](../media/setup-direct-modify-editconnectionsettings.png)

### <a name="add-ipv4-or-ipv6-session-information-on-active-connections"></a>Adicionar informações de sessão IPv4 ou IPv6 sobre conexões ativas
1. Selecione uma conexão de peering que você deseja modificar e, em seguida, selecione **...**  >  **Editar a conexão** conforme mostrado na etapa 1.
1. Digite as informações **do prefixo Session IPv4** ou **Session IPv6** e selecione **Salvar**.

### <a name="remove-ipv4-or-ipv6-session-information-on-active-connections"></a>Remova as informações da sessão IPv4 ou IPv6 sobre conexões ativas
A remoção do **prefixo Session IPv4** ou **do prefixo Session IPv6** não é suportada no portal no momento. Para obter mais informações, entre em contato com [a Microsoft .](mailto:peeringexperience@microsoft.com)
