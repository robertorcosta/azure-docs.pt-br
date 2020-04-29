---
title: Marcas de serviço do NSG (grupo de segurança de rede) para o Azure HDInsight
description: Use as marcas de serviço do HDInsight para permitir o tráfego de entrada para o cluster nos nós de serviços de integridade e gerenciamento, sem adicionar endereços IP ao seu NSGs.
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/15/2020
ms.openlocfilehash: 5608d0cd83e506bc6b30337db5148f344f59f80e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81410863"
---
# <a name="nsg-service-tags-for-azure-hdinsight"></a>Marcas de serviço NSG para o Azure HDInsight

As marcas de serviço do Azure HDInsight para NSGs (grupos de segurança de rede) são grupos de endereços IP para serviços de integridade e gerenciamento. Esses grupos ajudam a minimizar a complexidade para a criação de regras de segurança. As [marcas de serviço](../virtual-network/security-overview.md#service-tags) permitem o tráfego de entrada de IPS específicos sem inserir cada um dos [endereços IP de gerenciamento](hdinsight-management-ip-addresses.md) em seu NSGs.

O serviço HDInsight gerencia essas marcas de serviço. Você não pode criar sua própria marca de serviço ou modificar uma marca existente. A Microsoft gerencia os prefixos de endereço que correspondem à marca de serviço e atualiza automaticamente a marca de serviço à medida que os endereços são alterados.

## <a name="get-started-with-service-tags"></a>Introdução às marcas de serviço

Você tem duas opções para usar marcas de serviço em seus grupos de segurança de rede:

- **Usar uma única marcação de serviço hdinsight global**: essa opção abre sua rede virtual para todos os endereços IP que o serviço HDInsight usa para monitorar clusters em todas as regiões. Essa opção é o método mais simples, mas pode não ser apropriada se você tiver requisitos de segurança restritivos.

- **Usar várias marcas de serviço regional**: essa opção abre sua rede virtual somente para os endereços IP que o HDInsight usa nessa região específica. No entanto, se você estiver usando várias regiões, precisará adicionar várias marcas de serviço à sua rede virtual.

## <a name="use-a-single-global-hdinsight-service-tag"></a>Usar uma única marca de serviço do HDInsight global

A maneira mais fácil de começar a usar marcas de serviço com o cluster HDInsight é adicionar a marca `HDInsight` global a uma regra NSG.

1. No [portal do Azure](https://portal.azure.com/), selecione o grupo de segurança de rede.

1. Em **configurações**, selecione **regras de segurança de entrada**e, em seguida, selecione **+ Adicionar**.

1. Na lista suspensa **origem** , selecione **marca de serviço**.

1. Na lista suspensa **marca de serviço de origem** , selecione **HDInsight**.

    ![Adicionar uma marca de serviço do portal do Azure](./media/hdinsight-service-tags/azure-portal-add-service-tag.png)

Essa marca contém os endereços IP dos serviços de integridade e gerenciamento para todas as regiões em que o HDInsight está disponível. A marca garantirá que o cluster possa se comunicar com os serviços de integridade e gerenciamento necessários, independentemente de onde eles forem criados.

## <a name="use-regional-hdinsight-service-tags"></a>Usar marcas regionais de serviço do HDInsight

Se a opção de marca global não funcionar porque você precisa de permissões mais restritivas, você poderá permitir apenas as marcas de serviço aplicáveis à sua região. Pode haver várias marcas de serviço, dependendo da região em que o cluster é criado.

Para descobrir quais marcas de serviço adicionar para sua região, leia as seções a seguir do artigo.

### <a name="use-a-single-regional-service-tag"></a>Usar uma única marca de serviço regional

Se o cluster estiver localizado em uma região listada nesta tabela, você só precisará adicionar uma única marca de serviço regional ao seu NSG.

| País/Região | Região | Marca de serviço |
| ---- | ---- | ---- |
| Austrália | Leste da Austrália | HDInsight. AustraliaEast |
| &nbsp; | Sudeste da Austrália | HDInsight. AustraliaSoutheast |
| &nbsp; | Austrália Central | HDInsight. AustraliaCentral |
| China | Leste da China 2 | HDInsight. ChinaEast2 |
| &nbsp; | Norte da China 2 | HDInsight. ChinaNorth2 |
| Estados Unidos | Centro-Norte dos EUA | HDInsight. NorthCentralUS |
| &nbsp; | Oeste dos EUA 2 | HDInsight. WestUS2 |
| &nbsp; | Centro-Oeste dos EUA | HDInsight. WestCentralUS |
| Canada | Leste do Canadá | HDInsight. CanadaEast |
| Brasil | Sul do Brasil | HDInsight. BrazilSouth |
| Coreia do Sul | Coreia Central | HDInsight. KoreaCentral |
| &nbsp; | Sul da Coreia | HDInsight. KoreaSouth |
| Índia | Índia Central | HDInsight. CentralIndia |
| &nbsp; | Sul da Índia | HDInsight. SouthIndia |
| Japão | Oeste do Japão | HDInsight. JapanWest |
| França | França Central| HDInsight. FranceCentral |
| Reino Unido | Sul do Reino Unido | HDInsight. UKSouth |
| Azure Government | USDoD central | HDInsight. USDoDCentral |
| &nbsp; | Gov. dos EUA – Texas | HDInsight. USGovTexas |
| &nbsp; | Leste UsDoD | HDInsight. USDoDEast |
| &nbsp; | Gov. dos EUA – Arizona | HDInsight. USGovArizona |

### <a name="use-multiple-regional-service-tags"></a>Usar várias marcas de serviço regional

Se a região em que o cluster foi criado não estiver listada na tabela anterior, você precisará permitir várias marcas de serviço regional. A necessidade de usar mais de um é devido às diferenças na organização dos provedores de recursos para as várias regiões.

As regiões restantes são divididas em grupos com base nas marcas de serviço regional que usam.

#### <a name="group-1"></a>Grupo 1

Se o cluster for criado em uma das regiões na tabela a seguir, permita as marcas `HDInsight.WestUS` de serviço e `HDInsight.EastUS`. Além disso, a marca de serviço regional listada. As regiões nesta seção exigem três marcas de serviço.

Por exemplo, se o cluster for criado na `East US 2` região, você precisará adicionar as seguintes marcas de serviço ao seu grupo de segurança de rede:

- `HDInsight.EastUS2`
- `HDInsight.WestUS`
- `HDInsight.EastUS`

| País/Região | Região | Marca de serviço |
| ---- | ---- | ---- |
| Estados Unidos | Leste dos EUA 2 | HDInsight. EastUS2 |
| &nbsp; | Centro dos EUA | HDInsight. Centralus |
| &nbsp; | NorthCentral nós | HDInsight. NorthCentralUS |
| &nbsp; | Centro-Sul dos Estados Unidos | HDInsight. SouthCentralUS |
| &nbsp; | Leste dos EUA | HDInsight. Eastus |
| &nbsp; | Oeste dos EUA | HDInsight. Westus |
| Japão | Leste do Japão | HDInsight. JapanEast |
| Europa | Norte da Europa | HDInsight. NorthEurope |
| &nbsp; | Europa Ocidental| HDInsight. WestEurope |
| Ásia | Leste da Ásia | HDInsight. EastAsia |
| &nbsp; | Sudeste Asiático | HDInsight. SoutheastAsia |
| Austrália | Leste da Austrália | HDInsight. AustraliaEast |

#### <a name="group-2"></a>Grupo 2

Os clusters nas regiões de *norte da China* e *leste da China* precisam permitir duas marcas de serviço: `HDInsight.ChinaNorth` e `HDInsight.ChinaEast`.

#### <a name="group-3"></a>Grupo 3

Os clusters nas regiões de *US gov Iowa* e *US gov-Virgínia* precisam permitir duas marcas de serviço: `HDInsight.USGovIowa` e `HDInsight.USGovVirginia`.

#### <a name="group-4"></a>Grupo 4

Os clusters nas regiões da *Alemanha central* e da *Alemanha nordeste* precisam permitir duas marcas de serviço `HDInsight.GermanyCentral` : `HDInsight.GermanyNortheast`e.

## <a name="next-steps"></a>Próximas etapas

- [Grupos de segurança de rede: marcas de serviço](../virtual-network/security-overview.md#security-rules)
- [Criar redes virtuais para clusters do Azure HDInsight](hdinsight-create-virtual-network.md)
