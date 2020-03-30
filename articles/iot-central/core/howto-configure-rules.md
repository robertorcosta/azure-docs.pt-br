---
title: Configurar regras e ações no Azure IoT Central | Microsoft Docs
description: Este artigo de como fazer mostra, como construtor, como um construtor, como configurar regras e ações baseadas em telemetria em seu aplicativo Azure IoT Central.
author: vavilla
ms.author: vavilla
ms.date: 11/27/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 509f9557a8128df12353ad02a7c7db02b7b42631
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158431"
---
# <a name="configure-rules"></a>Configurar regras



*Este artigo aplica-se a operadores, construtores e administradores.*

As regras na IoT Central servem como uma ferramenta de resposta personalizável que aciona eventos monitorados ativamente de dispositivos conectados. As seções a seguir descrevem como as regras são avaliadas.

## <a name="select-target-devices"></a>Selecione dispositivos de destino

Use a seção dispositivos de destino para selecionar em que tipo de dispositivos esta regra será aplicada. Os filtros permitem refinar ainda mais quais dispositivos devem ser incluídos. Os filtros usam propriedades no modelo do dispositivo para filtrar o conjunto de dispositivos. Os filtros não desencadeiam uma ação. Na captura de tela a seguir, os dispositivos que estão sendo alvo são do tipo modelo de dispositivo **Geladeira**. O filtro afirma que a regra deve incluir apenas **refrigeradores** onde a propriedade **do Estado Fabricado** seja igual a **Washington.**

![Condições](media/howto-configure-rules/filters.png)

## <a name="use-multiple-conditions"></a>Use múltiplas condições

As condições são o que as regras desencadeiam. Atualmente, quando você adiciona múltiplas condições a uma regra, elas são logicamente e juntas. Em outras palavras, todas as condições devem ser atendidas para que a regra seja avaliada como verdadeira.  

Na captura de tela a seguir, as condições&deg; verificam quando a temperatura é superior a 70 F e a umidade é inferior a 10. Quando ambas as afirmações são verdadeiras, a regra avalia a verdade e desencadeia uma ação.

![Condições](media/howto-configure-rules/conditions.png)

## <a name="use-aggregate-windowing"></a>Use janelas agregadas

As regras avaliam janelas de tempo agregados como janelas caindo. Na imagem abaixo, a janela de tempo é de cinco minutos. A cada cinco minutos, a regra avalia os últimos cinco minutos de dados. Os dados só são avaliados uma vez na janela à qual correspondem.

![Janelas em cascata](media/howto-configure-rules/tumbling-window.png)

## <a name="use-rules-with-iot-edge-modules"></a>Use regras com módulos IoT Edge

Uma restrição se aplica às regras aplicadas aos módulos IoT Edge. As regras sobre telemetria de diferentes módulos não são avaliadas como regras válidas. Tome o seguinte como exemplo. A primeira condição da regra é em uma telemetria de temperatura do Módulo A. A segunda condição da regra é uma telemetria de umidade no Módulo B. Como as duas condições são de módulos diferentes, este é um conjunto de condições inválidas. A regra não é válida e lançará um erro ao tentar salvar a regra.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu como configurar uma regra no seu aplicativo Central IoT do Azure, você pode:

> [!div class="nextstepaction"]
> [Analise seus dados em tempo real](howto-create-analytics.md)
