---
title: incluir arquivo
description: incluir arquivo
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 09/12/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 945d701a2a7dffc259c601b4dab9fa1333ccc066
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "74896600"
---
Antes de iniciar sua configuração, verifique se você atende aos seguintes critérios:

* Se você já tem uma rede virtual à quais deseja se conectar, verifique se nenhuma das sub-redes da sua rede local se sobrepõe a ela. Sua rede virtual não requer uma sub-rede de gateway e não pode ter gateways de rede virtuais. Se você não tem uma rede virtual, você pode criar uma usando as etapas deste artigo.
* Obtenha um intervalo de endereços IP para sua região de hub. O hub é uma rede virtual, e o intervalo de endereços que você especifica para a região do hub não pode se sobrepor a uma rede virtual existente à qual você se conecta. Ele também não pode se sobrepor aos intervalos de endereços aos que você se conecta no local. Se você não estiver familiarizado com as faixas de endereçoIP localizadas na configuração de rede local, coordene com alguém que possa fornecer esses detalhes para você.
* Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.