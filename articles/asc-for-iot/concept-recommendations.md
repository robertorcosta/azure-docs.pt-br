---
title: Recomendações de segurança
description: Conheça o conceito de recomendações de segurança e como elas são usadas no Azure Security Center para IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 02ced504-d3aa-4770-9d10-b79f80af366c
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/24/2019
ms.author: mlottner
ms.openlocfilehash: 213595ac69efc90ec855b2891641e1f00bd1ba92
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311558"
---
# <a name="security-recommendations"></a>Recomendações de segurança

O Azure Security Center for IoT verifica seus recursos do Azure e dispositivos IoT e fornece recomendações de segurança para reduzir sua superfície de ataque.
As recomendações de segurança são acionáveis e visam ajudar os clientes a cumprir as práticas recomendadas de segurança.

Neste artigo, você encontrará uma lista de recomendações que podem ser acionadas em seu Hub IoT e/ou dispositivos IoT.

## <a name="recommendations-for-iot-devices"></a>Recomendações para dispositivos IoT

As recomendações do dispositivo fornecem insights e sugestões para melhorar a postura de segurança do dispositivo.

| Severity | Nome                                                      | fonte de dados | Descrição                                                                                                                                                                                           |
|----------|-----------------------------------------------------------|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Médio   | Abrir portas no dispositivo                                      | Agente       | Um ponto final de escuta foi encontrado no dispositivo.                                                                                                                                                        |
| Médio   | Política de firewall permissiva encontrada em uma das cadeias. | Agente       | Política de firewall permitida encontrada (ENTRADA/SAÍDA). A política de firewall deve negar todo o tráfego por padrão e definir regras para permitir a comunicação necessária para/a partir do dispositivo.                               |
| Médio   | A regra de firewall permissivo na cadeia de entrada foi encontrada     | Agente       | Foi encontrada uma regra no firewall que contém um padrão permissivo para uma ampla gama de endereços IP ou portas.                                                                                    |
| Médio   | A regra de firewall permissivo na cadeia de saída foi encontrada    | Agente       | Foi encontrada uma regra no firewall que contém um padrão permissivo para uma ampla gama de endereços IP ou portas.                                                                                   |
| Médio   | A validação da linha de base do sistema de operação falhou           | Agente       | O dispositivo não está em conformidade com [os benchmarks do CIS Linux](https://www.cisecurity.org/cis-benchmarks/).                                                                                                        |

### <a name="operational-recommendations-for-iot-devices"></a>Recomendações operacionais para dispositivos IoT

Recomendações operacionais fornecem insights e sugestões para melhorar a configuração do agente de segurança.

| Severity | Nome                                    | fonte de dados | Descrição                                                                       |
|----------|-----------------------------------------|-------------|-----------------------------------------------------------------------------------|
| Baixo      | Agente envia mensagens não utilizadas          | Agente       | 10% ou mais das mensagens de segurança foram menores que 4 KB nas últimas 24 horas.  |
| Baixo      | Configuração dupla de segurança não é a ideal | Agente       | A configuração de gêmeos de segurança não é a ideal.                                        |
| Baixo      | Conflito de configuração de gêmeos de segurança    | Agente       | Os conflitos foram identificados na configuração de gêmeos de segurança. |                          |
|

## <a name="recommendations-for-iot-hub"></a>Recomendações para IoT Hub

Os alertas de recomendação fornecem insights e sugestões de ações para melhorar a postura de segurança do seu ambiente.

| Severity | Nome                                                     | fonte de dados | Descrição                                                                                                                                                                                                             |
|----------|----------------------------------------------------------|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Alta     | Credenciais de autenticação idênticas usadas por vários dispositivos | Hub IoT     | As credenciais de autenticação do IoT Hub são usadas por vários dispositivos. Isso pode indicar um dispositivo ilegítimo se passando por um dispositivo legítimo. O uso duplicado de credenciais aumenta o risco de personificação do dispositivo por um ator mal-intencionado. |
| Médio   | A política padrão do filtro IP deve ser negado                  | Hub IoT     | A configuração do filtro IP deve ter regras definidas para o tráfego permitido e, por padrão, negar todos os outros tráfegos por padrão.                                                                                                     |
| Médio   | A regra do filtro IP inclui grande alcance IP                   | Hub IoT     | Um intervalo IP de fonte de regra de filtro IP é muito grande. Regras excessivamente permissivas podem expor seu hub de IoT a atores maliciosos.                                                                                       |
| Baixo      | Habilite os logs de diagnóstico no IoT Hub                       | Hub IoT     | Habilite os logs e retenha-os por até um ano. A retenção de logs permite que você recrie trilhas de atividade para fins de investigação quando ocorre um incidente de segurança ou sua rede é comprometida.                                       |
|

## <a name="next-steps"></a>Próximas etapas

- Azure Security Center para [visão geral](overview.md) do serviço de IoT
- Saiba como [acessar seus dados de segurança](how-to-security-data-access.md)
- Saiba mais sobre [investigar um dispositivo](how-to-investigate-device.md)
