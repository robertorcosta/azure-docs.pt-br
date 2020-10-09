---
title: Suporte a alto volume de tráfego do Gateway de Aplicativo
description: Este artigo fornece orientação para configurar o Gateway de Aplicativo do Azure para dar suporte a cenários de alto volume de tráfego de rede.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: conceptual
ms.date: 03/24/2020
ms.author: caya
ms.openlocfilehash: b96720ead2c7b7bc942efca32a8510f57c2dbcad
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85250241"
---
# <a name="application-gateway-high-traffic-support"></a>Suporte a alto tráfego do Gateway de Aplicativo

>[!NOTE]
> Este artigo descreve algumas diretrizes sugeridas para ajudar você a configurar o Gateway de Aplicativo para lidar com o tráfego extra devido ao alto volume de tráfego que pode ocorrer devido à crise da COVID-19.

Você pode usar o Gateway de Aplicativo com o WAF (firewall do aplicativo Web) para um modo escalonável e seguro de gerenciar o tráfego para seus aplicativos Web.

As sugestões a seguir ajudam a configurar o Gateway de Aplicativo com o WAF para lidar com o tráfego extra.

## <a name="use-the-v2-sku-over-v1-for-its-autoscaling-capabilities-and-performance-benefits"></a>Use o SKU v2 em vez da v1 para funcionalidades de dimensionamento automático e benefícios de desempenho
O SKU v2 oferece dimensionamento automático para garantir que o Gateway de Aplicativo possa escalar verticalmente conforme o tráfego aumentar. Ela também oferece outros benefícios de desempenho significativos, como desempenho de descarregamento de TLS cinco vezes melhor, tempos de atualização e implantação mais rápidos, redundância de zona e muito mais em comparação com a v1. Para obter mais informações, confira a [documentação da v2](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant). 

## <a name="set-maximum-instance-count-to-the-maximum-possible-125"></a>Definir a contagem máxima de instâncias para o máximo possível (125)
 
Supondo que você tenha um SKU do Gateway de Aplicativo v2, definir a contagem máxima de instâncias para o valor máximo possível de 125 permite que o Gateway de Aplicativo seja escalado horizontalmente conforme necessário. Isso permite que ele manipule o possível aumento no tráfego para seus aplicativos. Você será cobrado apenas pelas CUs (unidades de capacidade) que usar.  

## <a name="set-your-minimum-instance-count-based-on-your-average-cu-usage"></a>Definir a contagem mínima de instâncias com base em seu uso médio de CU

Supondo que você tenha um SKU do Gateway de Aplicativo v2, o dimensionamento automático leva de seis a sete minutos para escalar horizontalmente. Com uma contagem de instâncias mínima mais alta, o Gateway de Aplicativo pode lidar melhor com o tráfego quando a carga é aumentada, pois um pico no tráfego não requer uma operação de dimensionamento automático.  

## <a name="alert-if-a-certain-metric-surpasses-75-of-average-cu-utilization"></a>Alertar se uma determinada métrica ultrapassar 75% da utilização média de CU 
Confira a [documentação Métricas do Gateway de Aplicativo](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics#metrics-visualization) para obter uma explicação detalhada de nossas métricas e outras orientações. 

### <a name="example-setting-up-an-alert-on-75-of-average-cu-usage"></a>Exemplo: como definir um alerta em 75% do uso médio de CU

Este exemplo mostra como usar o portal do Azure para configurar um alerta quando 75% do uso médio de CU é atingido. 
1. Navegue até o **Gateway de Aplicativo**.
2. No painel esquerdo, selecione **Métricas** na guia **Monitoramento**. 
3. Adicione uma métrica para a **média de unidades de computação atuais**. 
![Como definir a métrica do WAF](./media/application-gateway-covid-guidelines/waf-setup-metrics.png)
4. Se você tiver definido a contagem mínima de instâncias como seu uso médio de CU, prossiga e defina um alerta quando 75% de suas instâncias mínimas estiverem em uso. Por exemplo, se o uso médio for 10 CUs, defina um alerta em 7,5 CUs. Isso alertará se o uso estiver aumentando e dará tempo para você responder. Você pode aumentar o mínimo se considerar que esse tráfego será mantido para alertar que o tráfego pode estar aumentando. 
![Como definir o alerta do WAF](./media/application-gateway-covid-guidelines/waf-setup-monitoring-alert.png)

> [!NOTE]
> Você pode definir que o alerta ocorra em um percentual de utilização de CU mais baixa ou mais alta, dependendo da sensibilidade que você deseja para os picos de tráfego potenciais.

## <a name="set-up-waf-with-geofiltering-and-bot-protection-to-stop-attacks"></a>Configurar o WAF com proteção contra bots e geofiltragem para interromper ataques
Se você quiser uma camada extra de segurança na frente do seu aplicativo, use o SKU do WAF_v2 do Gateway de Aplicativo para funcionalidades de WAF. Você pode configurar o SKU v2 para permitir somente o acesso aos seus aplicativos de um determinado país/região ou países/regiões. Você configura uma regra personalizada do WAF para permitir ou bloquear explicitamente o tráfego com base na localização geográfica. Para obter mais informações, confira as [regras personalizadas de filtragem](https://docs.microsoft.com/azure/web-application-firewall/ag/geomatch-custom-rules) e [como configurar regras personalizadas no SKU do WAF_v2 do Gateway de Aplicativo por meio do PowerShell](https://docs.microsoft.com/azure/web-application-firewall/ag/configure-waf-custom-rules).

Habilite a proteção contra bots para bloquear bots defeituosos conhecidos. Isso deve reduzir a quantidade de tráfego que chegará ao seu aplicativo. Para obter mais informações, confira [Proteção contra bots com instruções de configuração](https://docs.microsoft.com/azure/web-application-firewall/ag/configure-waf-custom-rules).

## <a name="turn-on-diagnostics-on-application-gateway-and-waf"></a>Ativar diagnósticos no Gateway de Aplicativo e no WAF

Os logs de diagnóstico permitem ver logs de firewall, logs de desempenho e logs de acesso. Você pode usar esses logs no Azure para gerenciar e solucionar problemas de Gateways de Aplicativo. Para obter mais informações, confira a [documentação de diagnóstico](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#diagnostic-logging). 

## <a name="set-up-an-tls-policy-for-extra-security"></a>Configurar uma política de TLS para segurança extra
Verifique se você está usando a versão mais recente da política TLS ([AppGwSslPolicy20170401S](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-policy-overview#appgwsslpolicy20170401s)). Isso impõe o TLS 1.2 e codificações mais fortes. Para saber mais, confira [Como configurar versões de política TLS e conjuntos de codificação via PowerShell](https://docs.microsoft.com/azure/application-gateway/application-gateway-configure-ssl-policy-powershell).
