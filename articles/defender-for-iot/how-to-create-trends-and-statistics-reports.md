---
title: Gerar relatórios de tendências e estatísticas
description: Conheça a atividade, as estatísticas e as tendências da rede usando o defender para tendências de IoT e os widgets de estatísticas.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 01/24/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 8bfbacc77843c7978bcb1d364bd93f5e09381144
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/26/2021
ms.locfileid: "98811603"
---
# <a name="sensor-trends-and-statistics-reports"></a>Tendências de sensor e relatórios de estatísticas

## <a name="about-sensor-trends-and-statistics-reports"></a>Sobre tendências de sensor e relatórios de estatísticas

Você pode criar gráficos de widgets e gráficos de pizza para obter informações sobre as tendências e as estatísticas da rede. Os widgets podem ser agrupados em painéis definidos pelo usuário.

> [!NOTE]
> Os analistas de administrador e segurança podem criar relatórios de tendências e estatísticas.

O painel consiste em widgets que descrevem graficamente os seguintes tipos de informações:

- Tráfego por porta
- Largura de banda do canal
- Largura de banda total
- Conexão TCP ativa
- Dispositivos:
  - Novos dispositivos
  - Dispositivos ocupados
  - Dispositivos por fornecedor
  - Dispositivos por sistema operacional
  - Dispositivos desconectados
- Descartar conectividade por horas
- Alertas para incidentes por tipo
- Acesso à tabela do banco de dados
- Widgets de desseção de protocolo
- Ethernet e endereço IP:
  - Tráfego de endereço IP e Ethernet pelo serviço CIP
  - Tráfego de endereço IP e Ethernet por classe CIP
  - Tráfego de endereço IP e Ethernet por comando
- OPC
  - Operações de gerenciamento superior do OPC
  - Operações de e/s superiores do OPC
- Siemens S7:
  - Tráfego S7 por função de controle
  - Tráfego S7 por subfunção
- SRTP
  - Tráfego de SRTP por código de serviço
  - Erros de SRTP por dia
- SuiteLink:
  - SuiteLink principais marcas consultadas
  - Comportamento de marca numérica SuiteLink
- Tráfego IEC-60870 por ASDU
- Tráfego DNP3 por função
- Tráfego de MMS por serviço
- Tráfego Modbus por função
- Tráfego OPC-UA por serviço

> [!NOTE]
>  A hora nos widgets é definida de acordo com o tempo do sensor.

## <a name="create-reports"></a>Criar relatórios

Para ver os painéis e widgets:

Selecione **tendências & estatísticas** no menu lateral.

:::image type="content" source="media/how-to-generate-reports/investigation-screenshot.png" alt-text="Captura de tela de uma investigação.":::

Por padrão, os resultados são exibidos para detecções nos últimos 7 dias. Você pode usar as ferramentas de filtro para alterar esse intervalo. Por exemplo, uma pesquisa de texto livre.

## <a name="see-also"></a>Confira também

Relatórios de avaliação de [risco](how-to-create-risk-assessment-reports.md) 
 Consultas de Data Mining de [sensor](how-to-create-data-mining-queries.md) 
 [Relatório de vetor de ataque](how-to-create-attack-vector-reports.md)