---
title: Habilitar o Serviço de Emparelhamento em um Emparelhamento direto usando o portal
titleSuffix: Azure
description: Habilitar o Serviço de Emparelhamento em um Emparelhamento direto usando o portal
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: article
ms.date: 3/18/2020
ms.author: derekol
ms.openlocfilehash: 87a65826a338f4098ae24c33939ea7f9d4475e36
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80129941"
---
# <a name="enable-peering-service-on-a-direct-peering-using-the-portal"></a>Habilitar o Serviço de Emparelhamento em um Emparelhamento direto usando o portal

Este artigo descreve como ativar [o Peering Service](overview-peering-service.md) em um peering direto usando o portal.

Se preferir, você pode completar este guia usando o [PowerShell](howto-peering-service-powershell.md).

## <a name="before-you-begin"></a>Antes de começar
* Revise [os pré-requisitos](prerequisites.md) antes de iniciar a configuração.
* Escolha um peering direto em sua assinatura que deseja ativar o Peering Service. Se você não tiver um, converta um peering direct legado ou crie um novo peering direto.
    * Para converter um peering direct legado, siga as instruções em [Converter um correspondente legado ao recurso Azure usando o portal](howto-legacy-direct-portal.md).
    * Para criar um novo peering direto, siga as instruções em [Criar ou modificar um peering direto usando o portal](howto-direct-portal.md).

## <a name="enable-peering-service-on-a-direct-peering"></a>Habilitar o Serviço de Emparelhamento em um Emparelhamento direto

### <a name="view-direct-peering"></a><a name= get></a>Ver peering direto
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

### <a name="enable-the-direct-peering-for-peering-service"></a><a name= get></a>Habilite o peering direto para o serviço de peering

Depois de abrir o peering direto na etapa anterior, habilite-o para serviço de peering.
[!INCLUDE [peering-direct-modify](./includes/peering-service-direct-portal.md)]

## <a name="modify-a-direct-peering-connection"></a>Modifique uma conexão de peering direto

Se você precisar modificar as configurações de conexão, consulte Modificar uma seção **de peering direto** em [Criar ou modificar um peering direto usando o portal](howto-direct-portal.md)

## <a name="next-steps"></a>Próximas etapas

* [Criar ou modificar o peering do Exchange usando o portal](howto-exchange-portal.md)
* [Converter um emparelhamento do Exchange herdado para o recurso do Azure usando o portal](howto-legacy-exchange-portal.md)

## <a name="additional-resources"></a>Recursos adicionais

Para perguntas frequentes, consulte [Peering Service FAQ](service-faqs.md).