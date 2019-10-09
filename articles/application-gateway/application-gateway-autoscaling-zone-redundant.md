---
title: Dimensionamento automático e Gateway de Aplicativo com redundância de zona v2
description: Este artigo apresenta o Aplicativo Azure SKU Standard_v2 e WAF_v2, que inclui dimensionamento automático e recursos com redundância de zona.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 10/09/2019
ms.author: victorh
ms.openlocfilehash: f58ac4448f50e8e02f2838fef02c9f884f69266b
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72177443"
---
# <a name="autoscaling-and-zone-redundant-application-gateway-v2"></a>Dimensionamento automático e Gateway de Aplicativo com redundância de zona v2 

O gateway de aplicativo e o WAF (firewall do aplicativo Web) também estão disponíveis em um SKU Standard_v2 e WAF_v2. A SKU v2 oferece aprimoramentos de desempenho e adiciona suporte a novos recursos críticos como dimensionamento automático, redundância de zona e suporte para VIPs estáticos. Os recursos existentes no SKU Standard e WAF continuam com suporte no novo SKU v2, com algumas exceções listadas na seção [comparação](#differences-with-v1-sku) .

A nova SKU v2 inclui os seguintes aprimoramentos:

- **Dimensionamento automático**: implantações do Gateway de Aplicativo ou do WAF com o SKU de dimensionamento automático podem ser expandidas ou reduzidas com base na mudança dos padrões de carga de tráfego. O escalonamento automático também remove o requisito de escolher um tamanho de implantação ou contagem de instâncias durante o provisionamento. Esta SKU oferece elasticidade verdadeira. No SKU Standard_v2 e WAF_v2, o gateway de aplicativo pode operar em capacidade fixa (dimensionamento automático desabilitado) e no modo de dimensionamento automático habilitado. O modo de capacidade fixa é útil para cenários com cargas de trabalho consistentes e previsíveis. O modo de dimensionamento automático é benéfico em aplicativos que veem a variação no tráfego do aplicativo.
- **Redundância de Zona**: Um gateway de aplicativo ou implantação de WAF pode abranger vários Zonas de Disponibilidade, removendo a necessidade de provisionar instâncias de gateway de aplicativo separadas em cada zona com um Gerenciador de tráfego. Você pode escolher uma única zona ou várias zonas nas quais as instâncias do gateway de aplicativo são implantadas, o que o torna mais resiliente a falhas de zona. O pool de back-end para aplicativos pode ser distribuído de maneira semelhante em Zonas de Disponibilidade.

  A redundância de zona está disponível somente quando as zonas do Azure estão disponíveis. Em outras regiões, há suporte para todos os outros recursos. Para saber mais, confira [O que são Zonas de Disponibilidade no Azure?](../availability-zones/az-overview.md#services-support-by-region)
- **VIP estático**: O SKU do gateway de aplicativo v2 dá suporte exclusivamente ao tipo de VIP estático. Isso garante que o VIP associado ao gateway de aplicativo não mude para o ciclo de vida da implantação, mesmo após uma reinicialização.  Não há um VIP estático na V1, portanto, você deve usar a URL do gateway de aplicativo em vez do endereço IP para roteamento de nome de domínio para os serviços de aplicativos por meio do gateway de aplicativo.
- **Reescrita de cabeçalho**: O gateway de aplicativo permite adicionar, remover ou atualizar cabeçalhos de solicitação e resposta HTTP com o SKU v2. Para obter mais informações, consulte [reescrever cabeçalhos HTTP com o gateway de aplicativo](rewrite-http-headers.md)
- **Integração de Key Vault (versão prévia)** : O gateway de aplicativo v2 dá suporte à integração com o Key Vault (em visualização pública) para certificados de servidor anexados a ouvintes habilitados para HTTPS. Para obter mais informações, consulte [terminação SSL com certificados Key Vault](key-vault-certs.md).
- **Controlador de entrada do serviço kubernetes do Azure (versão prévia)** : O controlador de entrada do gateway de aplicativo V2 permite que o gateway de Aplicativo Azure seja usado como entrada para um serviço de kubernetes do Azure (AKS) conhecido como cluster AKS. Para obter mais informações, consulte a [página de documentação](https://azure.github.io/application-gateway-kubernetes-ingress/).
- **Aprimoramentos de desempenho**: A SKU v2 oferece um desempenho de descarregamento de SSL de até 5 vezes melhor em comparação com o SKU Standard/WAF.
- **Tempo de implantação e atualização mais rápido** A SKU v2 fornece implantação e tempo de atualização mais rápidos em comparação com a SKU Standard/WAF. Isso também inclui alterações de configuração do WAF.

![](./media/application-gateway-autoscaling-zone-redundant/application-gateway-autoscaling-zone-redundant.png)

## <a name="supported-regions"></a>Regiões com suporte

O SKU Standard_v2 e WAF_v2 está disponível nas seguintes regiões: Norte EUA Central, Sul EUA Central, oeste dos EUA, oeste dos EUA 2, leste dos EUA, leste dos EUA 2, EUA Central, Europa Setentrional, Europa Ocidental, Sudeste Asiático, França central, Oeste do Reino Unido, leste do Japão, oeste do Japão, leste da Austrália, sudeste da Austrália, sul do Brasil, centro do Canadá, leste do Canadá, leste Ásia, Coreia central, sul da Coreia, sul da Índia, Sul do Reino Unido, Índia central, Índia ocidental, sul da Índia.

## <a name="pricing"></a>Preços

Com a SKU v2, o modelo de preços é acionado pelo consumo e não é mais anexado a contagens ou tamanhos de instância. O preço da SKU V2 tem dois componentes:

- **Preço fixo** – é o preço por hora (ou hora parcial) para provisionar um gateway Standard_v2 ou WAF_v2.
- **Preço unitário de capacidade** -esse é o custo baseado em consumo que é cobrado além do custo fixo. A cobrança da unidade de capacidade também é calculada por hora ou hora parcial. Existem três dimensões de unidade de capacidade: a unidade de computação, as conexões persistentes e a taxa de transferência. A unidade de computação é a medida da capacidade consumida do processador. Fatores que afetam a unidade de computação são conexões TLS/s, computações de regravação de URL e processamento de regra WAF. Conexão persistente é uma medida de conexões TCP estabelecidas com o gateway de aplicativo em um determinado intervalo de cobrança. A taxa de transferência é média de megabits/s processadas pelo sistema em um determinado intervalo de cobrança.

Cada unidade de capacidade é composta por, no máximo: 1 unidade de computação, ou 2500 conexões persistentes ou taxa de transferência de 2,22 Mbps.

Diretrizes da unidade de computação:

- **Standard_v2** -cada unidade de computação é capaz de aproximadamente 50 conexões por segundo com o certificado TLS de chave de 2048 bits da RSA.
- **WAF_v2** -cada unidade de computação pode dar suporte a aproximadamente 10 solicitações simultâneas por segundo para 70-30% de combinação de tráfego com 70% de solicitações com menos de 2 KB Get/post e restantes. O desempenho do WAF não é afetado pelo tamanho da resposta no momento.

> [!NOTE]
> Atualmente, cada instância pode oferecer suporte a aproximadamente 10 unidades de capacidade.
> O número de solicitações que uma unidade de computação pode manipular depende de vários critérios, como o tamanho da chave do certificado TLS, o algoritmo de troca de chave, as regravações de cabeçalho e, no caso do tamanho da solicitação de entrada do WAF. Recomendamos que você execute testes de aplicativo para determinar a taxa de solicitação por unidade de computação. A unidade de capacidade e a unidade de computação serão disponibilizadas como uma métrica antes do início da cobrança.

A tabela a seguir mostra os preços de exemplo e são apenas para fins ilustrativos.

**Preços no leste dos EUA**:

|              Nome do SKU                             | Preço fixo ($/HR)  | Preço unitário de capacidade ($/CU-hr)   |
| ------------------------------------------------- | ------------------- | ------------------------------- |
| Standard_v2                                       |    0.20             | 0, 80                          |
| WAF_v2                                            |    0,36             | 0, 144                          |

Para obter mais informações sobre preços, consulte a [página de preços](https://azure.microsoft.com/pricing/details/application-gateway/). A cobrança está agendada para iniciar em 1º de julho de 2019.

**Exemplo 1**

Um Standard_v2 do gateway de aplicativo é provisionado sem o dimensionamento automático no modo de dimensionamento manual com capacidade fixa de cinco instâncias.

Preço fixo = 744 (horas) * $0.20 = $148.08 <br>
Unidades de capacidade = 744 (horas) * 10 unidades de capacidade por instância * 5 instâncias * $0.08 por unidade de capacidade hora = $297.06

Preço total = $148.08 + $297.06 = $446.04

**Exemplo 2**

Um standard_v2 do gateway de aplicativo é provisionado por um mês e, durante esse tempo, ele recebe 25 novas conexões SSL/s, a média da transferência de dados de 8,88 Mbps. Supondo que as conexões sejam de curta duração, o preço seria:

Preço fixo = 744 (horas) * $0.20 = $148.08

Preço da unidade de capacidade = 744 (horas) * máx (25/50 unidade de computação para conexões/s, unidade de capacidade 8.88/2.22 para taxa de transferência) * $0.08 = 744 * 4 * 0, 8 = $23.81

Preço total = $148.8 + 23.81 = $172.61

> [!NOTE]
> A função Max retorna o maior valor em um par de valores.

**Exemplo 3**

Um WAF_v2 do gateway de aplicativo é provisionado por um mês. Durante esse tempo, ele recebe 25 novas conexões SSL/s, a média da transferência de dados de 8,88 Mbps e a 80 solicitação por segundo. Supondo que as conexões sejam de curta duração e que o cálculo da unidade de computação para o aplicativo dê suporte a 10 RPS por unidade de computação, seu preço seria:

Preço fixo = 744 (horas) * $0.36 = $267.84

Preço da unidade de capacidade = 744 (horas) * máx (unidade de computação máx. (25/50 para conexões/s, 80/10 WAF RPS), unidade de capacidade 8.88/2.22 para taxa de transferência) * $0.144 = 744 * 8 * 0, 144 = $85.71

Preço total = $267.84 + $85.71 = $353.55

> [!NOTE]
> A função Max retorna o maior valor em um par de valores.

## <a name="scaling-application-gateway-and-waf-v2"></a>Dimensionando o gateway de aplicativo e o WAF v2

O gateway de aplicativo e o WAF podem ser configurados para dimensionar em dois modos:

- **Dimensionamento** automático-com o dimensionamento automático habilitado, os SKUs do gateway de aplicativo e do WAF v2 são escalados verticalmente com base nos requisitos de tráfego do aplicativo. Esse modo oferece maior elasticidade ao seu aplicativo e elimina a necessidade de adivinhar o tamanho do gateway de aplicativo ou a contagem de instâncias. Esse modo também permite que você economize custos não exigindo que o gateway seja executado no pico de capacidade provisionada para carga de tráfego máxima prevista. Você deve especificar uma contagem mínima e opcional máxima de instâncias. A capacidade mínima garante que o gateway de aplicativo e o WAF v2 não fiquem abaixo da contagem mínima de instâncias especificada, mesmo na ausência de tráfego. Cada instância conta como 10 unidades de capacidade reservadas adicionais. 0 significa nenhuma capacidade reservada e é puramente autoescala por natureza. Observe que 0 instâncias mínimas adicionais ainda garantem a alta disponibilidade do serviço, que é sempre incluído com o preço fixo. Opcionalmente, você também pode especificar uma contagem máxima de instâncias, o que garante que o gateway de aplicativo não seja dimensionado além do número especificado de instâncias. Você continuará sendo cobrado pela quantidade de tráfego servida pelo Gateway. As contagens de instâncias podem variar de 0 a 125. O valor padrão para contagem máxima de instâncias é 20, se não for especificado. 
- **Manual** -você também pode escolher o modo manual em que o gateway não fará o dimensionamento automático. Nesse modo, se houver mais tráfego do que o gateway de aplicativo ou o WAF pode manipular, isso poderá resultar em perda de tráfego. Com o modo manual, especificar a contagem de instâncias é obrigatório. A contagem de instâncias pode variar de 1 a 125 instâncias.

## <a name="feature-comparison-between-v1-sku-and-v2-sku"></a>Comparação de recursos entre SKU v1 e SKU v2

A tabela a seguir compara os recursos disponíveis com cada SKU.

|                                                   | SKU v1   | SKU v2   |
| ------------------------------------------------- | -------- | -------- |
| Dimensionamento automático                                       |          | &#x2713; |
| Redundância de zona                                   |          | &#x2713; |
| VIP estático                                        |          | &#x2713; |
| Controlador de entrada do AKS (serviço kubernetes do Azure) |          | &#x2713; |
| Integração do Cofre da Chave do Azure                       |          | &#x2713; |
| Reescrever cabeçalhos HTTP (S)                           |          | &#x2713; |
| Roteamento baseado em URL                                 | &#x2713; | &#x2713; |
| Hospedagem de vários sites                             | &#x2713; | &#x2713; |
| Redirecionamento de tráfego                               | &#x2713; | &#x2713; |
| Firewall do aplicativo Web (WAF)                    | &#x2713; | &#x2713; |
| Encerramento do protocolo SSL            | &#x2713; | &#x2713; |
| Criptografia SSL de ponta a ponta                         | &#x2713; | &#x2713; |
| Afinidade de sessão                                  | &#x2713; | &#x2713; |
| Páginas de erro personalizadas                                | &#x2713; | &#x2713; |
| Suporte para WebSocket                                 | &#x2713; | &#x2713; |
| Suporte do HTTP/2                                    | &#x2713; | &#x2713; |
| Descarregamento de conexão                               | &#x2713; | &#x2713; |

> [!NOTE]
> O SKU v2 de dimensionamento automático agora dá suporte a [investigações de integridade padrão](application-gateway-probe-overview.md#default-health-probe) para monitorar automaticamente a integridade de todos os recursos em seu pool de back-ends e realçar os membros de back-end que são considerados não íntegros. A investigação de integridade padrão é configurada automaticamente para back-ends que não têm nenhuma configuração de investigação personalizada. Para saber mais, consulte [investigações de integridade no gateway de aplicativo](application-gateway-probe-overview.md).

## <a name="differences-with-v1-sku"></a>Diferenças com o SKU v1

|Entre|Detalhes|
|--|--|
|Certificado de autenticação|Não compatível.<br>Para saber mais, confira [Visão geral de SSL de ponta a ponta com o Gateway de Aplicativo](ssl-overview.md#end-to-end-ssl-with-the-v2-sku).|
|Combinando Standard_v2 e o Gateway de Aplicativo Standard na mesma sub-rede|Sem suporte|
|Rota Definida pelo Usuário (UDR) na sub-rede de Gateway de Aplicativo|Sem suporte|
|NSG para o intervalo de porta de entrada| -65200 a 65535 para Standard_v2 SKU<br>-65503 to 65534 para Standard SKU.<br>Consulte mais informações em [Perguntas Frequentes](application-gateway-faq.md#are-network-security-groups-supported-on-the-application-gateway-subnet).|
|Logs de desempenho no diagnóstico do Azure|Não compatível.<br>As métricas do Azure devem ser usadas.|
|Cobrança|Cobrança agendada para iniciar em 1º de julho de 2019.|
|Modo FIPS|Essas não atualmente têm suporte.|
|Modo somente de ILB|Não há suporte para esse recurso no momento. Público e o modo ILB juntos tem suporte.|
|Integração do Netwatcher|Não compatível.|
|Integração da central de segurança do Azure|Ainda não está disponível.

## <a name="migrate-from-v1-to-v2"></a>Migrar de v1 para v2

Um script de Azure PowerShell está disponível na galeria do PowerShell para ajudá-lo a migrar do seu Application Gateway/WAF v1 para o SKU de dimensionamento automático v2. Esse script ajuda a copiar a configuração do seu gateway v1. A migração de tráfego ainda é sua responsabilidade. Para obter mais informações, consulte [migrar aplicativo Azure gateway de v1 para v2](migrate-v1-v2.md).

## <a name="next-steps"></a>Próximas etapas

- [Início Rápido: Direcionar o tráfego da Web com o Gateway de Aplicativo do Azure – portal do Azure](quick-create-portal.md)
- [Criar um gateway de aplicativo com redundância de zona e dimensionamento automático com um endereço IP virtual reservado usando o Azure PowerShell](tutorial-autoscale-ps.md)
- Saiba mais sobre [Gateway de aplicativo](overview.md).
- Saiba mais sobre [Firewall do Azure](../firewall/overview.md).
