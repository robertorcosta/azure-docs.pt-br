---
title: Dimensionamento automático e Gateway de Aplicativo com redundância de zona v2
description: Este artigo introduz o Standard_v2 de aplicativos Do Azure e WAF_v2 SKU, que inclui recursos de Autoscaling e Zone-redundante.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 03/24/2020
ms.author: victorh
ms.openlocfilehash: 7feb0f00c5431048d19d4ad6cb3860f6eb8ed052
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81312700"
---
# <a name="autoscaling-and-zone-redundant-application-gateway-v2"></a>Dimensionamento automático e Gateway de Aplicativo com redundância de zona v2 

O Application Gateway e o Web Application Firewall (WAF) também estão disponíveis sob um Standard_v2 e WAF_v2 SKU. O V2 SKU oferece melhorias de desempenho e adiciona suporte para novos recursos críticos, como autodimensionamento, redundância de zona e suporte para VIPs estáticos. Os recursos existentes sob o Padrão e waf SKU continuam a ser suportados no novo V2 SKU, com algumas exceções listadas na seção [de comparação.](#differences-with-v1-sku)

O novo V2 SKU inclui os seguintes aprimoramentos:

- **Escalonamento automático**: implantações de Gateway de Aplicativo ou WAF sob o SKU de escalonamento automático podem ser ampliados ou reduzidos com base na mudança de padrões de carga de tráfego. O escalonamento automático também remove o requisito de escolher um tamanho de implantação ou contagem de instâncias durante o provisionamento. Este SKU oferece verdadeira elasticidade. No Standard_v2 e WAF_v2 SKU, o Application Gateway pode operar tanto na capacidade fixa (autoscaling desativada) quanto no modo ativado para autodimensionamento. O modo de capacidade fixa é útil para cenários com cargas de trabalho consistentes e previsíveis. O modo de autodimensionamento é benéfico em aplicativos que veem variância no tráfego de aplicativos.
- **Redundância de região**: Uma implantação de Gateway de aplicativo ou WAF pode abranger várias Zonas de Disponibilidade, removendo a necessidade de provisionar instâncias separadas do Gateway de aplicativo em cada região com um Gerenciador de tráfego. Você pode escolher uma única região ou várias zonas onde as instâncias do Application Gateway são implantadas, o que a torna mais resistente à falha de região. O pool de back-end para aplicativos pode ser distribuído de maneira semelhante em Zonas de Disponibilidade.

  A redundância de região só está disponível quando as Zonas Azure estiverem disponíveis. Em outras regiões, todos os outros recursos são suportados. Para obter mais informações, consulte [Quais são as zonas de disponibilidade no Azure?](../availability-zones/az-overview.md#services-support-by-region)
- **VIP estático**: O Gateway de aplicativo v2 SKU suporta exclusivamente o tipo VIP estático. Isso garante que o VIP associado ao gateway do aplicativo não mude para o ciclo de vida da implantação, mesmo após uma reinicialização.  Não há um VIP estático no v1, então você deve usar a URL do gateway do aplicativo em vez do endereço IP para roteamento de nome de domínio para Serviços de Aplicativo através do gateway do aplicativo.
- **Cabeçalho Rewrite**: O Gateway de aplicativo permite adicionar, remover ou atualizar cabeçalhos de solicitação e resposta HTTP com v2 SKU. Para obter mais informações, consulte [Reescrever cabeçalhos HTTP com o Gateway de aplicativo](rewrite-http-headers.md)
- **Integração do Cofre chave**: O Gateway de aplicativo v2 suporta a integração com o Key Vault para certificados de servidor que são anexados a ouvintes habilitados para HTTPS. Para obter mais informações, consulte [o término do TLS com os certificados Key Vault](key-vault-certs.md).
- **Controlador de entrada de serviços Do Azure Kubernetes**: O Application Gateway v2 Ingress Controller permite que o Gateway de aplicativo Azure seja usado como entrada para um Azure Kubernetes Service (AKS) conhecido como AKS Cluster. Para obter mais informações, consulte [O que é o Controlador de Entrada do Gateway de Aplicativo?](ingress-controller-overview.md)
- **Melhorias de desempenho**: O V2 SKU oferece até 5X melhor desempenho de descarga TLS em comparação com o Standard/WAF SKU.
- **Mais rápido implantação e tempo de atualização** O V2 SKU fornece um tempo de implantação e atualização mais rápido em comparação com o Standard/WAF SKU. Isso também inclui alterações de configuração do WAF.

![](./media/application-gateway-autoscaling-zone-redundant/application-gateway-autoscaling-zone-redundant.png)

## <a name="supported-regions"></a>Regiões com suporte

O Standard_v2 e WAF_v2 SKU está disponível nas seguintes regiões: Norte Central dos EUA, Centro-Sul dos EUA, Oeste dos EUA, Oeste dos EUA 2, Leste dos EUA, Eua Central, Europa Norte, Europa Ocidental, Sudeste Asiático, França Central, Reino Unido Ocidental, Japão Leste, Austrália Sudeste, Brasil Sul, Canadá Central, Canadá Leste, Ásia Oriental, Coréia Central, Coréia do Sul , Reino Unido Sul, Índia Central, Índia Ocidental, Índia do Sul.

## <a name="pricing"></a>Preços

Com o v2 SKU, o modelo de preços é impulsionado pelo consumo e não está mais ligado às contagens ou tamanhos de ocorrência. O preço v2 SKU tem dois componentes:

- **Preço fixo** - Este é o preço por hora (ou hora parcial) para provisão de um gateway de Standard_v2 ou WAF_v2. Observe que 0 instâncias mínimas adicionais ainda garante alta disponibilidade do serviço que está sempre incluído com preço fixo.
- **Preço unitário de capacidade** - Este é um custo baseado no consumo que é cobrado além do custo fixo. A carga da unidade de capacidade também é calculada de hora em hora ou parcialmente. Existem três dimensões para unidade de capacidade - unidade de computação, conexões persistentes e throughput. A unidade de computação é uma medida da capacidade do processador consumida. Os fatores que afetam a unidade de computação são conexões TLS/seg, computação de regravação de URL e processamento de regras WAF. Conexão persistente é uma medida de conexões TCP estabelecidas ao gateway de aplicativo em um determinado intervalo de faturamento. O throughput é megabits/seg médio processado pelo sistema em um determinado intervalo de faturamento.  O faturamento é feito em um nível de Unidade de Capacidade para qualquer coisa acima da contagem de instâncias reservada.

Cada unidade de capacidade é composta de no máximo: 1 unidade de computação, ou 2500 conexões persistentes, ou throughput de 2,22 Mbps.

Orientação da unidade de computação:

- **Standard_v2** - Cada unidade de computação é capaz de aproximadamente 50 conexões por segundo com o certificado TLS de chave RSA 2048 bits.
- **WAF_v2** - Cada unidade de computação pode suportar aproximadamente 10 solicitações simultâneas por segundo para um mix de tráfego de 70 a 30% com 70% de solicitações inferiores a 2 KB GET/POST e permanecendo mais alto. O desempenho do WAF não é afetado pelo tamanho da resposta atualmente.

> [!NOTE]
> Cada instância pode atualmente suportar aproximadamente 10 unidades de capacidade.
> O número de solicitações que uma unidade de computação pode lidar depende de vários critérios, como tamanho da chave do certificado TLS, algoritmo de troca de chaves, regravações de cabeçalho e no caso do tamanho da solicitação de entrada do WAF. Recomendamos que você realize testes de aplicação para determinar a taxa de solicitação por unidade de computação. Tanto a unidade de capacidade quanto a unidade de computação serão disponibilizadas como métrica antes do início do faturamento.

A tabela a seguir mostra preços de exemplo e são apenas para fins de ilustração.

**Preços no Leste dos EUA**:

|              Nome do SKU                             | Preço fixo ($/hr)  | Preço unitário de capacidade ($/CU-hr)   |
| ------------------------------------------------- | ------------------- | ------------------------------- |
| Standard_v2                                       |    0,20             | 0.0080                          |
| WAF_v2                                            |    0.36             | 0.0144                          |

Para obter mais informações sobre preços, consulte a [página de preços](https://azure.microsoft.com/pricing/details/application-gateway/). 

**Exemplo 1**

Um Standard_v2 do Application Gateway é provisionado sem a escalação automática no modo de dimensionamento manual com capacidade fixa de cinco instâncias.

Preço fixo = 744(horas) * $0,20 = $148,8 <br>
Unidades de capacidade = 744 (horas) * 10 unidade de capacidade por instância * 5 instâncias * $0,008 por hora unitária de capacidade = $297,6

Preço total = $148.8 + $297.6 = $446.4

**Exemplo 2**

Um Gateway de aplicativo standard_v2 é provisionado por um mês, com zero instâncias mínimas, e durante esse tempo recebe 25 novas conexões TLS/seg, média de 8,88 Mbps de transferência de dados. Supondo que as conexões sejam de curta duração, seu preço seria:

Preço fixo = 744(horas) * $0,20 = $148,8

Preço unitário de capacidade = 744(horas) * Max (unidade de computação 25/50 para conexões/seg, 8,88/2,22 unidade de capacidade para produção) * $0,008 = 744 * 4 * 0,008 = $23.81

Preço total = $148.8+23.81 = $172.61

Como você pode ver, você só é cobrado por quatro Unidades de Capacidade, não por toda a instância. 

> [!NOTE]
> A função Max retorna o maior valor em um par de valores.


**Exemplo 3**

Um standard_v2 de Gateway de aplicativo é provisionado por um mês, com um mínimo de cinco instâncias. Supondo que não há tráfego e conexões são de curta duração, seu preço seria:

Preço fixo = 744(horas) * $0,20 = $148,8

Preço unitário de capacidade = 744(horas) * Max (0/50 unidade de computação para conexões/seg, 0/2,22 unidade de capacidade para throughput) * $0,008 = 744 * 50 * 0,008 = $297,60

Preço total = $148.80+297.60 = $446.4

Neste caso, você é cobrado pela totalidade das cinco instâncias, mesmo que não haja tráfego.

**Exemplo 4**

Um standard_v2 de Gateway de aplicativo é provisionado por um mês, com um mínimo de cinco instâncias, mas desta vez há uma média de transferência de dados de 125 mbps e 25 conexões TLS por segundo. Supondo que não há tráfego e conexões são de curta duração, seu preço seria:

Preço fixo = 744(horas) * $0,20 = $148,8

Preço unitário de capacidade = 744(horas) * Max (unidade de computação 25/50 para conexões/seg, unidade de capacidade 125/2,22 para throughput) * $0,008 = 744 * 57 * 0,008 = $339.26

Preço total = $148.80+339.26 = $488.06

Neste caso, você é cobrado para as cinco instâncias completas, mais sete Unidades de Capacidade (que é 7/10 de uma instância).  

**Exemplo 5**

Um gateway de aplicativo WAF_v2 é provisionado por um mês. Durante esse período, ele recebe 25 novas conexões TLS/seg, média de 8,88 Mbps de transferência de dados e faz 80 solicitações por segundo. Supondo que as conexões sejam de curta duração, e que o cálculo da unidade de computação para o aplicativo suporte 10 RPS por unidade de computação, seu preço seria:

Preço fixo = 744(horas) * $0,36 = $267,84

Preço unitário de capacidade = 744(horas) * Max (unidade de computação Max(25/50 para conexões/seg, 80/10 WAF RPS), 8,88/2,22 unidade de capacidade para throughput) * $0,0144 = 744 * 8 * 0,0144 = $85,71

Preço total = $267.84 + $85.71 = $353.55

> [!NOTE]
> A função Max retorna o maior valor em um par de valores.

## <a name="scaling-application-gateway-and-waf-v2"></a>Gateway de aplicativo de escala e WAF v2

O Gateway de aplicativo e o WAF podem ser configurados para dimensionar em dois modos:

- **Autoscaling** - Com o autoscaling ativado, o Application Gateway e o WAF v2 SKUs são dimensionados para cima ou para baixo com base nos requisitos de tráfego de aplicativos. Este modo oferece melhor elasticidade ao seu aplicativo e elimina a necessidade de adivinhar o tamanho do gateway do aplicativo ou a contagem de instâncias. Este modo também permite que você economize custos, não exigindo que o gateway seja executado no pico de capacidade provisionada para carga máxima de tráfego antecipada. Você deve especificar uma contagem mínima e opcionalmente máxima de instâncias. A capacidade mínima garante que o Application Gateway e o WAF v2 não fiquem abaixo da contagem mínima de instâncias especificada, mesmo na ausência de tráfego. Cada instância conta como 10 Unidades de Capacidade reservadas adicionais. Zero não significa capacidade reservada e é puramente autoscaling na natureza. Observe que zero instâncias mínimas adicionais ainda garante alta disponibilidade do serviço que está sempre incluído com preço fixo. Você também pode especificar opcionalmente uma contagem máxima de instâncias, o que garante que o Gateway de aplicativo não seja dimensionado além do número especificado de instâncias. Você continuará a ser cobrado pela quantidade de tráfego servido pelo Gateway. As contagens de ocorrênciapodem variar de 0 a 125. O valor padrão para a contagem máxima de instâncias é de 20, se não especificado.
- **Manual** - Você pode escolher o modo Manual, onde o gateway não será escalado automaticamente. Neste modo, se houver mais tráfego do que o Gateway de Aplicativo ou WAF pode lidar, isso pode resultar em perda de tráfego. Com o modo manual, especificar a contagem de instâncias é obrigatório. A contagem de instâncias pode variar de 1 a 125 instâncias.

## <a name="feature-comparison-between-v1-sku-and-v2-sku"></a>Comparação de recursos entre v1 SKU e v2 SKU

A tabela a seguir compara os recursos disponíveis com cada SKU.

|                                                   | v1 SKU   | v2 SKU   |
| ------------------------------------------------- | -------- | -------- |
| Dimensionamento automático                                       |          | &#x2713; |
| Redundância de zona                                   |          | &#x2713; |
| VIP estático                                        |          | &#x2713; |
| Controlador de ingress s azure Kubernetes Service (AKS) |          | &#x2713; |
| Integração do Cofre da Chave do Azure                       |          | &#x2713; |
| Reescrever cabeçalhos HTTP(S)                           |          | &#x2713; |
| Roteamento baseado em URL                                 | &#x2713; | &#x2713; |
| Hospedagem de vários sites                             | &#x2713; | &#x2713; |
| Redirecionamento de tráfego                               | &#x2713; | &#x2713; |
| Firewall do aplicativo Web (WAF)                    | &#x2713; | &#x2713; |
| Regras personalizadas de WAF                                  |          | &#x2713; |
| TLS(Transport Layer Security, segurança da camada de transporte)/terminação de soquetes seguros (SSL)            | &#x2713; | &#x2713; |
| Criptografia TLS de ponta a ponta                         | &#x2713; | &#x2713; |
| Afinidade de sessão                                  | &#x2713; | &#x2713; |
| Páginas de erro personalizadas                                | &#x2713; | &#x2713; |
| Suporte para WebSocket                                 | &#x2713; | &#x2713; |
| Suporte do HTTP/2                                    | &#x2713; | &#x2713; |
| Descarregamento de conexão                               | &#x2713; | &#x2713; |

> [!NOTE]
> O SKU de autodimensionamento v2 agora suporta testes de saúde padrão para monitorar automaticamente a saúde de todos os recursos em seu pool back-end e destacar os membros back-end que são considerados [insalubres.](application-gateway-probe-overview.md#default-health-probe) O teste de saúde padrão é configurado automaticamente para backends que não possuem nenhuma configuração personalizada do teste. Para saber mais, consulte [testes de saúde no gateway de aplicação](application-gateway-probe-overview.md).

## <a name="differences-with-v1-sku"></a>Diferenças com v1 SKU

|Diferença|Detalhes|
|--|--|
|Certificado de autenticação|Sem suporte.<br>Para obter mais informações, consulte [Visão geral do TLS de ponta a ponta com o Gateway de aplicativo](ssl-overview.md#end-to-end-tls-with-the-v2-sku).|
|Combinando Standard_v2 e o Gateway de Aplicativo Standard na mesma sub-rede|Sem suporte|
|UDR (User-Defined Route, rota definida pelo usuário) na sub-rede do Gateway do aplicativo|Suportado (cenários específicos). Na pré-visualização.<br> Para obter mais informações sobre cenários suportados, consulte [visão geral da configuração do Application Gateway](configuration-overview.md#user-defined-routes-supported-on-the-application-gateway-subnet).|
|NSG para o intervalo de porta de entrada| -65200 a 65535 para Standard_v2 SKU<br>-65503 to 65534 para Standard SKU.<br>Consulte mais informações em [Perguntas Frequentes](application-gateway-faq.md#are-network-security-groups-supported-on-the-application-gateway-subnet).|
|Logs de desempenho no diagnóstico do Azure|Sem suporte.<br>As métricas do Azure devem ser usadas.|
|Cobrança|O faturamento está previsto para começar em 1º de julho de 2019.|
|Modo FIPS|Essas não atualmente têm suporte.|
|Modo somente de ILB|Não há suporte para esse recurso no momento. Público e o modo ILB juntos tem suporte.|
|Integração do Netwatcher|Sem suporte.|
|Integração da Central de Segurança do Azure|Ainda não está disponível.

## <a name="migrate-from-v1-to-v2"></a>Migrar de v1 para v2

Um script Azure PowerShell está disponível na galeria PowerShell para ajudá-lo a migrar do seu gateway/WAF de aplicativo v1 para o SKU de autodimensionamento v2. Este script ajuda a copiar a configuração do gateway v1. A migração de tráfego ainda é sua responsabilidade. Para obter mais informações, consulte [Migrate Azure Application Gateway de v1 para v2](migrate-v1-v2.md).

## <a name="next-steps"></a>Próximas etapas

- [Início Rápido: Direcionar o tráfego da Web com o Gateway de Aplicativo do Azure – portal do Azure](quick-create-portal.md)
- [Criar um gateway de aplicativo com redundância de zona e dimensionamento automático com um endereço IP virtual reservado usando o Azure PowerShell](tutorial-autoscale-ps.md)
- Saiba mais sobre [Gateway de aplicativo](overview.md).
- Saiba mais sobre [Firewall do Azure](../firewall/overview.md).
