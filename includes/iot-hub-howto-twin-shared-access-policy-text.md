---
title: incluir arquivo
description: incluir arquivo
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 08/07/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: e81ce2743d2509ce52f3190218decfa4e518bdad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "70050386"
---
<!-- This contains intro text for the "Get an IoT hub connection string" section in the iot-hub-lang-lang-twin-getstarted.md files-->

Neste artigo, você cria um serviço de back-end que adiciona propriedades desejadas a um gêmeo de dispositivo e, em seguida, consulta o registro de identidade para encontrar todos os dispositivos com propriedades relatadas que foram atualizadas de acordo. Seu serviço precisa da permissão **de conexão** de serviço para modificar as propriedades desejadas de um dispositivo gêmeo, e ele precisa da permissão **de leitura** do registro para consultar o registro de identidade. Não há nenhuma política de acesso compartilhado padrão que contenha apenas essas duas permissões, então você precisa criar uma.
