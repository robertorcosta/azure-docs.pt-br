---
title: Suporte ao volume de alto tráfego do gateway de aplicativo
description: Este artigo fornece orientação para configurar Aplicativo Azure gateway para dar suporte a cenários de alto volume de tráfego de rede.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 03/24/2020
ms.author: caya
ms.openlocfilehash: 65f404b52a5fc06d8fa5bb5aad291e57fde8caba
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80617193"
---
# <a name="application-gateway-high-traffic-support"></a>Suporte a alto tráfego do Gateway de Aplicativo

>[!NOTE]
> Este artigo descreve algumas diretrizes sugeridas para ajudá-lo a configurar o gateway de aplicativo para lidar com o tráfego extra devido ao alto volume de tráfego que pode ocorrer devido à crise COVID-19.

Você pode usar o gateway de aplicativo com o WAF (firewall do aplicativo Web) para uma maneira escalonável e segura de gerenciar o tráfego para seus aplicativos Web.

As sugestões a seguir ajudam a configurar o gateway de aplicativo com o WAF para lidar com o tráfego extra.

## <a name="use-the-v2-sku-over-v1-for-its-autoscaling-capabilities-and-performance-benefits"></a>Use a SKU V2 em v1 para seus recursos de dimensionamento automático e benefícios de desempenho
A SKU v2 oferece dimensionamento automático para garantir que o gateway de aplicativo possa escalar verticalmente conforme o tráfego aumentar. Ele também oferece outros benefícios de desempenho significativos, como desempenho de descarregamento de TLS mais rápido, tempos de atualização e implantação mais rápidos, redundância de zona e muito mais em comparação com v1. Para obter mais informações, consulte nossa [documentação do v2](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant). 

## <a name="set-maximum-instance-count-to-the-maximum-possible-125"></a>Definir a contagem máxima de instâncias para o máximo possível (125)
 
Supondo que você tenha um SKU do gateway de aplicativo v2, definir a contagem máxima de instâncias para o valor máximo possível de 125 permite que o gateway de aplicativo seja expandido conforme necessário. Isso permite que ele manipule o possível aumento no tráfego para seus aplicativos. Você será cobrado apenas pelas unidades de capacidade (CUs) que usar.  

## <a name="set-your-minimum-instance-count-based-on-your-average-cu-usage"></a>Definir a contagem mínima de instâncias com base em seu uso médio de CU

Supondo que você tenha um SKU do gateway de aplicativo v2, o dimensionamento automático leva de seis a sete minutos para escalar horizontalmente. Com uma contagem de instâncias mínima mais alta, o gateway de aplicativo pode lidar melhor com o tráfego quando a carga é aumentada, pois um pico no tráfego não requer uma operação de dimensionamento automático.  

## <a name="alert-if-a-certain-metric-surpasses-75-of-average-cu-utilization"></a>Alertar se uma determinada métrica ultrapassar 75% da utilização média de CU 
Consulte a [documentação de métricas do gateway de aplicativo](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics#metrics-visualization) para obter uma explicação detalhada de nossas métricas e outras orientações. 

### <a name="example-setting-up-an-alert-on-75-of-average-cu-usage"></a>Exemplo: Configurando um alerta em 75% do uso médio de CU

Este exemplo mostra como usar o portal do Azure para configurar um alerta quando 75% do uso médio de CU é atingido. 
1. Navegue até o **Gateway de aplicativo**.
2. No painel esquerdo, selecione **métricas** na guia **monitoramento** . 
3. Adicione uma métrica para **média de unidades de computação atuais**. 
![Configurando a métrica WAF](./media/application-gateway-covid-guidelines/waf-setup-metrics.png)
4. Se você tiver definido a contagem mínima de instâncias como seu uso médio de CU, vá em frente e defina um alerta quando 75% de suas instâncias mínimas estiverem em uso. Por exemplo, se seu uso médio for 10 CUs, defina um alerta em 7,5 CUs. Isso alertará se o uso estiver aumentando e lhe dará tempo para responder. Você pode aumentar o mínimo se considerar que esse tráfego será mantido para alertar que o tráfego pode estar aumentando. 
![Configurando o alerta do WAF](./media/application-gateway-covid-guidelines/waf-setup-monitoring-alert.png)

> [!NOTE]
> Você pode definir que o alerta ocorra em uma porcentagem de utilização mais baixa ou mais alta, dependendo de quão confidencial você deseja para os picos de tráfego potenciais.

## <a name="set-up-waf-with-geofiltering-and-bot-protection-to-stop-attacks"></a>Configurar o WAF com proteção geofiltragem e bot para interromper ataques
Se você quiser uma camada extra de segurança na frente do seu aplicativo, use o SKU do gateway de aplicativo WAF_v2 para recursos de WAF. Você pode configurar a SKU v2 para permitir somente o acesso aos seus aplicativos de um determinado país ou países. Você configura uma regra personalizada WAF para permitir ou bloquear explicitamente o tráfego com base na localização geográfica. Para obter mais informações, consulte [regras personalizadas de filtragem](https://docs.microsoft.com/azure/web-application-firewall/ag/geomatch-custom-rules) e [como configurar regras personalizadas no gateway de aplicativo WAF_v2 SKU por meio do PowerShell](https://docs.microsoft.com/azure/web-application-firewall/ag/configure-waf-custom-rules).

Habilite a proteção de bot para bloquear bots defeituosos conhecidos. Isso deve reduzir a quantidade de tráfego que chegará ao seu aplicativo. Para obter mais informações, consulte [proteção de bot com instruções de configuração](https://docs.microsoft.com/azure/web-application-firewall/ag/configure-waf-custom-rules).

## <a name="turn-on-diagnostics-on-application-gateway-and-waf"></a>Ativar diagnósticos no gateway de aplicativo e WAF

Os logs de diagnóstico permitem exibir logs de firewall, logs de desempenho e logs de acesso. Você pode usar esses logs no Azure para gerenciar e solucionar problemas de gateways de aplicativo. Para obter mais informações, consulte nossa [documentação de diagnóstico](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#diagnostic-logging). 

## <a name="set-up-an-tls-policy-for-extra-security"></a>Configurar uma política de TLS para segurança extra
Verifique se você está usando a versão mais recente da política TLS ([AppGwSslPolicy20170401S](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-policy-overview#appgwsslpolicy20170401s)). Isso impõe o TLS 1,2 e codificações mais fortes. Para obter mais informações, consulte [Configurando versões de política de TLS e conjuntos de codificação por meio do PowerShell](https://docs.microsoft.com/azure/application-gateway/application-gateway-configure-ssl-policy-powershell).
