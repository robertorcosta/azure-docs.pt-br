---
title: Entender a central de segurança do Azure para obter recomendações de segurança de IoT | Microsoft Docs
description: Saiba mais sobre o conceito de recomendações de segurança e como elas são usadas na central de segurança do Azure para IoT.
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
ms.openlocfilehash: a8de821abcedf6bb9a331852a2c0af9b6439667a
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78303572"
---
# <a name="security-recommendations"></a>Recomendações de segurança

A central de segurança do Azure para IoT examina seus recursos do Azure e dispositivos IoT e fornece recomendações de segurança para reduzir a superfície de ataque. As recomendações de segurança são acionáveis e visam ajudar os clientes a cumprir as práticas recomendadas de segurança.

Neste artigo, você encontrará uma lista de recomendações que podem ser disparadas em seus dispositivos de Hub IoT e/ou IoT.

## <a name="recommendations-for-iot-devices"></a>Recomendações para dispositivos IoT

As recomendações de dispositivo fornecem ideias e sugestões para melhorar a postura de segurança do dispositivo. 

| Severity | {1&gt;Nome&lt;1}                                                      | Fonte de Dados | Descrição                                                                                                                                                                                           |
|----------|-----------------------------------------------------------|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Média   | Abrir portas no dispositivo                                      | Agente do       | Um ponto de extremidade de escuta foi encontrado no dispositivo.                                                                                                                                                        |
| Média   | Política de firewall permissiva encontrada em uma das cadeias. | Agente do       | Política de firewall permitida encontrada (entrada/saída). A política de firewall deve negar todo o tráfego por padrão e definir regras para permitir a comunicação necessária de/para o dispositivo.                               |
| Média   | A regra de firewall permissiva na cadeia de entrada foi encontrada     | Agente do       | Foi encontrada uma regra no firewall que contém um padrão permissivo para uma grande variedade de endereços IP ou portas.                                                                                    |
| Média   | A regra de firewall permissiva na cadeia de saída foi encontrada    | Agente do       | Foi encontrada uma regra no firewall que contém um padrão permissivo para uma grande variedade de endereços IP ou portas.                                                                                   |
| Média   | Falha na validação da linha de base do sistema operacional           | Agente do       | O dispositivo não está em conformidade com os [benchmarks Linux do CIS](https://www.cisecurity.org/cis-benchmarks/).                                                                                                        |

### <a name="operational-recommendations-for-iot-devices"></a>Recomendações operacionais para dispositivos IoT

As recomendações operacionais fornecem informações e sugestões para melhorar a configuração do agente de segurança.

| Severity | {1&gt;Nome&lt;1}                                    | Fonte de Dados | Descrição                                                                       |
|----------|-----------------------------------------|-------------|-----------------------------------------------------------------------------------|
| Baixo      | O Agent envia mensagens não utilizadas          | Agente do       | 10% ou mais de mensagens de segurança foram menores que 4 KB durante as últimas 24 horas.  |
| Baixo      | Configuração de configurações de segurança não ideal | Agente do       | A configuração de configurações de segurança não é ideal.                                        |
| Baixo      | Conflito de configuração de configurações de segurança    | Agente do       | Os conflitos foram identificados na configuração de configurações de segurança. |                          |
|

## <a name="recommendations-for-iot-hub"></a>Recomendações para o Hub IoT

Alertas de recomendação fornecem Insight e sugestões para ações para melhorar a postura de segurança do seu ambiente.  

| Severity | {1&gt;Nome&lt;1}                                                     | Fonte de Dados | Descrição                                                                                                                                                                                                             |
|----------|----------------------------------------------------------|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Alta     | Credenciais de autenticação idênticas usadas por vários dispositivos | Hub IoT     | As credenciais de autenticação do Hub IoT são usadas por vários dispositivos. Isso pode indicar um dispositivo ilegítimo representando um dispositivo legítimo. O uso de credenciais duplicadas aumenta o risco de representação do dispositivo por um ator mal-intencionado. |
| Média   | A política de filtro IP padrão deve ser Deny                  | Hub IoT     | A configuração do filtro IP deve ter regras definidas para tráfego permitido e, por padrão, negar todos os outros tráfego por padrão.                                                                                                     |
| Média   | A regra de filtro IP inclui um intervalo IP grande                   | Hub IoT     | Um intervalo de IP de origem de regra de filtro IP de permissão é muito grande. Regras excessivamente permissivas podem expor seu hub IoT para atores mal-intencionados.                                                                                       |
| Baixo      | Habilitar logs de diagnóstico no Hub IoT                       | Hub IoT     | Habilite os logs e retenha-os por até um ano. A retenção de logs permite recriar trilhas de atividade para fins de investigação quando ocorre um incidente de segurança ou sua rede é comprometida.                                       |
|

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

- [Visão geral](overview.md) da central de segurança do Azure para serviços de IOT
- Saiba como [acessar seus dados de segurança](how-to-security-data-access.md)
- Saiba mais sobre [a investigação de um dispositivo](how-to-investigate-device.md)