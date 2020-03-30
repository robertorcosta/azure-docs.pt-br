---
title: Passo a passo do Emparelhamento direto
titleSuffix: Azure
description: Passo a passo do Emparelhamento direto
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: d88fcfc4d3e073bf544f2ca0f4d01dbe305b45da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775505"
---
# <a name="direct-peering-walkthrough"></a>Passo a passo do Emparelhamento direto

Esta seção explica os passos que você precisa seguir para configurar e gerenciar um peering direto.

## <a name="create-a-direct-peering"></a>Crie um peering direto
> [!div class="mx-imgBorder"]
> ![Fluxo de trabalho e estados de conexão de peering diretos](./media/direct-peering.png)

As seguintes etapas devem ser seguidas para prover um peering direto:
1. Revise a [política de peering da](https://peering.azurewebsites.net/peering) Microsoft para entender os requisitos para peering direto.
1. Siga as instruções em [Criar ou modificar um peering direto](howto-direct-powershell.md) para enviar uma solicitação de peering.
1. Depois de enviar uma solicitação de peering, a Microsoft entrará em contato usando seu endereço de e-mail registrado para fornecer LOA (Carta de Autorização) ou para outras informações.
1. Uma vez aprovada a solicitação de peering, o estado de conexão altera o ProvisionamentoIniciado.
1. Você precisa:
    1. fiação completa de acordo com a LOA
    1. (opcionalmente) realizar teste de link usando 169.254.0.0/16
    1. configurar sessão BGP e, em seguida, notificar-nos.
1. A Microsoft prospec a sessão BGP com a política DENY ALL e valida de ponta a ponta.
1. Se for bem-sucedido, você receberá uma notificação de que o estado de conexão de peering está ativo.
1. O tráfego será permitido através do novo peering.

Observe que os estados de conexão não devem ser confundidos com os estados de sessão [BGP](https://en.wikipedia.org/wiki/Border_Gateway_Protocol) padrão.

## <a name="convert-a-legacy-direct-peering-to-azure-resource"></a>Converter um Emparelhamento direto herdado para o recurso do Azure
As seguintes etapas devem ser seguidas para converter um correspondente direto legado ao recurso DoZure:
1. Siga as instruções em [Converter um peering direto legado para o recurso DoZure](howto-legacy-direct-powershell.md)
1. Depois de enviar a solicitação de conversão, a Microsoft analisará a solicitação e entrará em contato com você, se necessário.
1. Uma vez aprovado, você verá seu peering direto com um estado de conexão como Ativo.

## <a name="deprovision-direct-peering"></a>Deprovision Direct peering
Entre em contato com a equipe [de peering da Microsoft](mailto:peering@microsoft.com) para desprovisionar o peering direto.

Quando um peering direto é definido para desprovisionamento, você verá o estado de conexão como **PendingRemove**

> [!NOTE]
> Se você executar o cmdlet PowerShell para excluir o peering direto quando o Provisionamento do Estado de Conexãoiniciado ou ProvisionamentoConcluído, a operação falhará.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [pré-requisitos para configurar peering com a Microsoft](prerequisites.md).
