---
title: Suporte ao alto volume de tráfego do Application Gateway
description: Este artigo fornece orientação para configurar o Azure Application Gateway em suporte a cenários de alto volume de tráfego de rede.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 03/24/2020
ms.author: caya
ms.openlocfilehash: 65f404b52a5fc06d8fa5bb5aad291e57fde8caba
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80617193"
---
# <a name="application-gateway-high-traffic-support"></a>Suporte a alto tráfego do Gateway de Aplicativo

>[!NOTE]
> Este artigo descreve algumas diretrizes sugeridas para ajudá-lo a configurar seu Gateway de aplicativo para lidar com tráfego extra devido ao alto volume de tráfego que pode ocorrer devido à crise do COVID-19.

Você pode usar o Application Gateway com o Web Application Firewall (WAF) para uma maneira escalável e segura de gerenciar o tráfego para seus aplicativos web.

As seguintes sugestões ajudam você a configurar o Application Gateway com o WAF para lidar com o tráfego extra.

## <a name="use-the-v2-sku-over-v1-for-its-autoscaling-capabilities-and-performance-benefits"></a>Use o V2 SKU sobre v1 para seus recursos de autodimensionamento e benefícios de desempenho
O v2 SKU oferece autodimensionamento para garantir que o Gateway de aplicativo possa aumentar à medida que o tráfego aumenta. Ele também oferece outros benefícios significativos de desempenho, como desempenho de descarga TLS 5x melhor, tempos de implantação e atualização mais rápidos, redundância de zona e muito mais quando comparado ao v1. Para obter mais informações, consulte nossa [documentação v2](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant). 

## <a name="set-maximum-instance-count-to-the-maximum-possible-125"></a>Definir a contagem máxima de instâncias ao máximo possível (125)
 
Supondo que você tenha um Application Gateway v2 SKU, definir a contagem máxima de instâncias para o valor máximo possível de 125 permite que o Gateway de aplicativo seja dimensionado conforme necessário. Isso permite lidar com o possível aumento do tráfego para seus aplicativos. Você só será cobrado pelas Unidades de Capacidade (UCs) que você usa.  

## <a name="set-your-minimum-instance-count-based-on-your-average-cu-usage"></a>Defina sua contagem mínima de instâncias com base no seu uso médio de

Supondo que você tenha um Application Gateway v2 SKU, o autoscaling leva de seis a sete minutos para ser dimensionado. Com uma contagem mínima mais alta, o Gateway de aplicativo pode lidar melhor com o tráfego quando a carga é aumentada, porque um pico no tráfego não requer uma operação de autodimensionamento.  

## <a name="alert-if-a-certain-metric-surpasses-75-of-average-cu-utilization"></a>Alerte se uma determinada métrica ultrapassar 75% da utilização média da U 
Consulte a [documentação do Application Gateway Metrics](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics#metrics-visualization) para obter uma explicação detalhada de nossas métricas e outros passo a passo. 

### <a name="example-setting-up-an-alert-on-75-of-average-cu-usage"></a>Exemplo: Configuração de um alerta em 75% do uso médio de

Este exemplo mostra como usar o portal Azure para configurar um alerta quando 75% do uso médio de é atingido. 
1. Navegue até o **gateway de aplicativos**.
2. No painel esquerdo, selecione **Métricas** na guia **Monitorar.** 
3. Adicione uma métrica para **unidades de computação atuais médias**. 
![Configuração da métrica WAF](./media/application-gateway-covid-guidelines/waf-setup-metrics.png)
4. Se você definiu sua contagem mínima de instâncias como seu uso médio de, vá em frente e defina um alerta quando 75% de suas instâncias mínimas estiverem em uso. Por exemplo, se o seu uso médio for de 10 UCs, defina um alerta em 7,5 CUs. Isso alerta você se o uso está aumentando e lhe dá tempo para responder. Você pode aumentar o mínimo se você acha que esse tráfego será mantido para alertá-lo de que o tráfego pode estar aumentando. 
![Configuração do alerta WAF](./media/application-gateway-covid-guidelines/waf-setup-monitoring-alert.png)

> [!NOTE]
> Você pode definir o alerta para ocorrer em uma porcentagem de utilização de menor ou maior, dependendo de quão sensível você deseja ser para picos de tráfego potenciais.

## <a name="set-up-waf-with-geofiltering-and-bot-protection-to-stop-attacks"></a>Configure o WAF com geofiltragem e proteção contra bots para parar ataques
Se você quiser uma camada extra de segurança na frente do seu aplicativo, use o Application Gateway WAF_v2 SKU para recursos WAF. Você pode configurar o V2 SKU para permitir apenas o acesso aos seus aplicativos de um determinado país ou países. Você configura uma regra personalizada do WAF para permitir ou bloquear explicitamente o tráfego com base na geolocalização. Para obter mais informações, consulte [regras personalizadas de geofiltragem](https://docs.microsoft.com/azure/web-application-firewall/ag/geomatch-custom-rules) e [como configurar regras personalizadas no Application Gateway WAF_v2 SKU através do PowerShell](https://docs.microsoft.com/azure/web-application-firewall/ag/configure-waf-custom-rules).

Habilite a proteção do bot para bloquear bots ruins conhecidos. Isso deve reduzir a quantidade de tráfego que chegam ao seu aplicativo. Para obter mais informações, consulte [a proteção do bot com instruções de configuração](https://docs.microsoft.com/azure/web-application-firewall/ag/configure-waf-custom-rules).

## <a name="turn-on-diagnostics-on-application-gateway-and-waf"></a>Ativar diagnósticos no Gateway de aplicativo e waf

Os registros de diagnóstico permitem visualizar registros de firewall, registros de desempenho e registros de acesso. Você pode usar esses logs no Azure para gerenciar e solucionar problemas dos Gateways de aplicativos. Para obter mais informações, consulte nossa [documentação de diagnóstico.](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#diagnostic-logging) 

## <a name="set-up-an-tls-policy-for-extra-security"></a>Configure uma política TLS para segurança extra
Certifique-se de que está usando a versão mais recente da política TLS[(AppGwSslPolicy20170401S).](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-policy-overview#appgwsslpolicy20170401s) Isso impõe o TLS 1.2 e as cifras mais fortes. Para obter mais informações, consulte [a configuração de versões de política TLS e suítes de cifras via PowerShell](https://docs.microsoft.com/azure/application-gateway/application-gateway-configure-ssl-policy-powershell).
