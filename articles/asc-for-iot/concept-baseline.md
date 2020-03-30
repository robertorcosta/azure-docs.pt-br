---
title: Entenda o Azure Security Center para a linha de base de IoT| Microsoft Docs
description: Conheça o conceito do Azure Security Center para a linha de base de IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: ef839708-4574-4a40-bc45-07005f8e9daf
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/07/2019
ms.author: mlottner
ms.openlocfilehash: ce5a0625a16c5a02d03ee74f894c585820414fa4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "72176609"
---
# <a name="azure-security-center-for-iot-baseline-and-custom-checks"></a>Centro de Segurança Do Azure para linha de base ioT e verificações personalizadas

Este artigo explica o Azure Security Center para a linha de base ioT e resume todas as propriedades associadas das verificações personalizadas da linha de base.

## <a name="baseline"></a>Linha de base

Uma linha de base estabelece o comportamento padrão para cada dispositivo e facilita o estabelecimento de comportamentos incomuns ou desvio de normas esperadas.  

## <a name="baseline-custom-checks"></a>Verificações personalizadas da linha de base

As verificações personalizadas da linha de base estabelecem uma lista personalizada de verificações para cada linha de base do dispositivo usando o gêmeo de **identidade do módulo** do dispositivo. 

## <a name="setting-baseline-properties"></a>Definindo propriedades da linha de base

1. No seu IoT Hub, localize e selecione o dispositivo que deseja alterar.
1. Clique no dispositivo e clique no módulo **de segurança azureiot.**
1. Clique em **'Gêmeo de identidade do módulo '**
1. Carregue o arquivo **de verificações personalizadas** da linha de base para o dispositivo.
1. Adicione propriedades de linha de base ao módulo de segurança e clique **em Salvar**.

### <a name="baseline-custom-check-file-example"></a>Exemplo de arquivo de verificação personalizado da linha de base

Para configurar verificações personalizadas da linha de base:

   ```json
    "desired": {
      "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration": {
        "baselineCustomChecksEnabled": {
          "value" : true
        },
        "baselineCustomChecksFilePath": {
          "value" : "/home/user/full_path.xml"
        },
        "baselineCustomChecksFileHash": {
          "value" : "#hashexample!"
        }
      }
    },
   ```

## <a name="baseline-custom-check-properties"></a>Propriedades de verificação personalizada da linha de base

| Nome| Status | Valores válidos| Valores padrão| Descrição |
|----------|------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|---------------|
|linha de baseVerificações de vidahabilitadas|Necessário: verdadeiro |Valores válidos: **Boolean** |Valor padrão: **falso** |Intervalo de tempo máximo antes que mensagens de alta prioridade seja enviada.|
|linha de baseCustomChecksFilePath |Necessário: verdadeiro|Valores válidos: **String**, **nulo** |Valor padrão: **nulo** |Caminho completo da configuração xml da linha de base|
|linha de baseCustomChecksFileHash |Necessário: verdadeiro|Valores válidos: **String**, **nulo** |Valor padrão: **nulo** |`sha256sum`do arquivo de configuração xml. Use a [referência sha256sum](https://linux.die.net/man/1/sha256sum) para obter informações adicionais. |

Para revisar exemplos adicionais de linha de base, consulte [o exemplo de linha de base personalizado -1](https://ascforiot.blob.core.windows.net/public/custom_baseline_example_hyperv_ubuntu1804.xml) e o exemplo de linha de base personalizado [-2](https://ascforiot.blob.core.windows.net/public/oms_audits.xml).

## <a name="next-steps"></a>Próximas etapas

- Acesse seus [dados de segurança brutos](how-to-security-data-access.md)
- [Investigar um dispositivo](how-to-investigate-device.md)
- Entenda e explore [recomendações de segurança](concept-recommendations.md)
- Entenda e explore [alertas de segurança](concept-security-alerts.md)
