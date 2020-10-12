---
title: Gerenciando o acesso à rede pública para o Hub IoT do Azure
description: Documentação sobre como desabilitar e habilitar o acesso à rede pública para o Hub IoT
author: jlian
ms.author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/01/2020
ms.openlocfilehash: c82f98df8fb79fa10f2e30b219c1a02bb646e2de
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85937483"
---
# <a name="managing-public-network-access-for-your-iot-hub"></a>Gerenciando o acesso à rede pública para o Hub IoT

Para restringir o acesso somente ao [ponto de extremidade privado para o Hub IOT em sua VNet](virtual-network-support.md), desabilite o acesso à rede pública. Para fazer isso, use portal do Azure ou a `publicNetworkAccess` API. 

## <a name="turn-off-public-network-access-using-azure-portal"></a>Desligar o acesso à rede pública usando portal do Azure

1. Visite [portal do Azure](https://portal.azure.com)
2. Navegue até o seu Hub IoT.
3. Selecione **rede** no menu do lado esquerdo.
4. Em "permitir acesso de rede pública a", selecione **desabilitado**
5. Clique em **Salvar**.

:::image type="content" source="media/iot-hub-publicnetworkaccess/turn-off-public-network-access.png" alt-text="Imagem mostrando portal do Azure onde desativar o acesso à rede pública" lightbox="media/iot-hub-publicnetworkaccess/turn-off-public-network-access.png":::

Para ativar o acesso à rede pública, selecione **habilitado**e, em seguida, **salvar**.

## <a name="ip-filter"></a>Filtro IP 

Se o acesso à rede pública estiver desabilitado, todas as regras de [filtro IP](iot-hub-ip-filtering.md) serão ignoradas. Isso ocorre porque todos os IPs da Internet pública estão bloqueados. Para usar o filtro IP, use a opção **intervalos de IP selecionados** .

## <a name="bug-fix-with-built-in-event-hub-compatible-endpoint"></a>Correção de bug com ponto de extremidade compatível com o Hub de eventos interno

Há um bug com o Hub IoT em que o [ponto de extremidade compatível com o Hub de eventos interno](iot-hub-devguide-messages-read-builtin.md) continua a ser acessado por meio da Internet pública quando o acesso à rede pública ao Hub IOT é desabilitado. Para saber mais e entre em contato conosco sobre esse bug, consulte [desabilitar o acesso à rede pública para o Hub IOT desabilita o acesso ao ponto de extremidade do hub de eventos interno](https://azure.microsoft.com/updates/iot-hub-public-network-access-bug-fix).