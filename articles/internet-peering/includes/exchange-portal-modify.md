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
ms.openlocfilehash: e06b5261ca6923e158c818d236a30cf6ebff189b
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680919"
---
Esta seção descreve como executar as seguintes operações de modificação para peering direto.

### <a name="add-exchange-peering-connections"></a>Adicionar conexões de peering do Exchange

1. Selecione o botão **+ Adicionar conexões** e configure uma nova conexão de peering.
    > [!div class="mx-imgBorder"]
    > ![Exibição de recursos de peering](../media/setup-exchange-modify-addconnection.png)
1. Preencha o formulário **'Conexão de peering' do Exchange** e selecione **Salvar**. Para obter ajuda na configuração de uma conexão de peering, revise as etapas na seção "Criar e provisionar um peering direto".
    > [!div class="mx-imgBorder"]
    > ![Formulário de conexão de peering de intercâmbio](../media/setup-exchange-modify-savenewconnection.png)

### <a name="remove-exchange-peering-connections"></a>Remover conexões de peering do Exchange

1. Selecione uma conexão de peering que você deseja excluir e, em seguida, selecione **...**  >  **Excluir conexão**.
    > [!div class="mx-imgBorder"]
    > ![Excluir botão de conexão](../media/setup-exchange-modify-deleteconnection.png)
1. Digite o ID de recurso na caixa **Confirmar exclusão** e **selecione Excluir**.
    > [!div class="mx-imgBorder"]
    > ![Exclua a confirmação](../media/setup-exchange-modify-deleteconnectionconfirm.png)

### <a name="add-an-ipv4-or-ipv6-session-on-active-connections"></a>Adicione uma sessão IPv4 ou IPv6 em conexões ativas

1. Selecione uma conexão de peering que você deseja modificar e, em seguida, selecione **...**  >  **Editar conexão**.
    > [!div class="mx-imgBorder"]
    > ![Editar botão de conexão](../media/setup-exchange-modify-editconnection.png)
1. Adicione informações **de endereço IPv4** ou **IPv6** e selecione **Salvar**.
    > [!div class="mx-imgBorder"]
    > ![Modificações de conexão peering](../media/setup-exchange-modify-editconnectionsettings.png)

### <a name="remove-an-ipv4-or-ipv6-session-on-active-connections"></a>Remova uma sessão IPv4 ou IPv6 em conexões ativas

A remoção de uma sessão IPv4 ou IPv6 de uma conexão existente não é suportada no momento no portal. Para obter mais informações, entre em contato com [a Microsoft .](mailto:peeringexperience@microsoft.com)