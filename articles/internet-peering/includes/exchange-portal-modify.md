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
ms.openlocfilehash: e3e87e59f5b3c95051d9ee53e4b8d87afe9d9ba8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774556"
---
Esta seção descreve como executar as seguintes operações de modificação para peering direto:

### <a name="add-exchange-peering-connections"></a>Adicionar conexões de peering do Exchange

1. Clique no botão **+ Adicionar conexões** na parte superior e configurar uma nova conexão de peering.
    > [!div class="mx-imgBorder"]
    > ![Exibição de recursos de peering](../media/setup-exchange-modify-addconnection.png)
1. Preencha o formulário **'Conexão de abulamento' do Exchange** e clique **em Salvar**. Para obter ajuda na configuração de uma conexão de peering, revise as etapas na seção "Criar e provisionar um peering direto" acima.
    > [!div class="mx-imgBorder"]
    > ![Exibição de recursos de peering](../media/setup-exchange-modify-savenewconnection.png)

### <a name="remove-exchange-peering-connections"></a>Remover conexões de peering do Exchange

1. Clique em uma conexão de peering que você deseja excluir e, em seguida, clique no **...**  > Excluir botão **de conexão.**
    > [!div class="mx-imgBorder"]
    > ![Exclusão de conexão de peering](../media/setup-exchange-modify-deleteconnection.png)
1. Digite o ID de recurso na caixa **Confirmar exclusão** como mostrado nas caixas destacadas e clique em **Excluir**.
    > [!div class="mx-imgBorder"]
    > ![Exclusão de conexão de peeringConfirmar](../media/setup-exchange-modify-deleteconnectionconfirm.png)

### <a name="add-ipv4ipv6-session-on-active-connections"></a>Adicionar sessão IPv4/IPv6 em conexões ativas

1. Clique em uma conexão de peering que você deseja modificar e, em seguida, clique no **...**  > Editar botão **de conexão.**
    > [!div class="mx-imgBorder"]
    > ![Edição de conexão de peering](../media/setup-exchange-modify-editconnection.png)
1. Adicione **informações de endereço IPv4** ou **IPv6** e clique **em Salvar**.
    > [!div class="mx-imgBorder"]
    > ![Modificação de conexão de peering](../media/setup-exchange-modify-editconnectionsettings.png)

### <a name="remove-ipv4ipv6-session-on-active-connections"></a>Remova a sessão IPv4/IPv6 em conexões ativas

A remoção de uma sessão IPv4/IPv6 de uma conexão existente não é suportada no portal no momento. Entre em contato com [a Microsoft .](mailto:peeringexperience@microsoft.com)