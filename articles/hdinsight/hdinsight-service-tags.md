---
title: Tags de serviço do grupo de segurança de rede (NSG) para Azure HDInsight
description: Use as tags de serviço HDInsight para permitir tráfego de entrada para o cluster a partir de nós de serviços de saúde e gerenciamento do HDInsight, sem adicionar explicitamente endereços IP aos seus grupos de segurança de rede.
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 03/10/2020
ms.openlocfilehash: a72753d5553e79a8ed28c3afcc7e54af6c2d230c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79117242"
---
# <a name="network-security-group-nsg-service-tags-for-azure-hdinsight"></a>Tags de serviço do grupo de segurança de rede (NSG) para Azure HDInsight

As tags de serviço HDInsight para grupos de segurança de rede (NSGs) são grupos de endereços IP para serviços de saúde e gerenciamento. Esses grupos ajudam a minimizar a complexidade para a criação de regras de segurança. [As tags de](../virtual-network/security-overview.md#service-tags) serviço fornecem um método alternativo para permitir tráfego de entrada de endereços IP específicos sem inserir cada um dos [endereços IP](hdinsight-management-ip-addresses.md) de gerenciamento em seus grupos de segurança de rede.

Essas tags de serviço são criadas e gerenciadas pelo serviço HDInsight. Você não pode criar sua própria tag de serviço ou modificar uma tag existente. A Microsoft gerencia os prefixos de endereço que correspondem à tag de serviço e atualiza automaticamente a tag de serviço à medida que os endereços mudam.

## <a name="getting-started-with-service-tags"></a>Começando com tags de serviço

Você tem duas opções para usar tags de serviço em seus grupos de segurança de rede:

1. Use uma única tag de serviço HDInsight - esta opção abrirá sua rede virtual para todos os endereços IP que o serviço HDInsight está usando para monitorar clusters em todas as regiões. Esta opção é o método mais simples, mas pode não ser apropriado se você tiver requisitos de segurança restritivos.

1. Use várias tags de serviço regionais - essa opção abrirá sua rede virtual apenas para os endereços IP que o HDInsight está usando nessa região específica. No entanto, se você estiver usando várias regiões, então você precisará adicionar várias tags de serviço à sua rede virtual.

## <a name="use-a-single-global-hdinsight-service-tag"></a>Use uma única tag global de serviço HDInsight

A maneira mais fácil de começar a usar tags de `HDInsight` serviço com seu cluster HDInsight é adicionar a tag global a uma regra de grupo de segurança de rede.

1. No [portal Azure,](https://portal.azure.com/)selecione seu grupo de segurança de rede.

1. Em **Configurações,** selecione **Regras de segurança de entrada**e selecione + **Adicionar**.

1. Na **lista** de paradas de origem, selecione **'Tag**de serviço ''''''''

1. Na lista de saque saudosista da tag de **serviço Origem,** selecione **HDInsight**.

    ![Portal do Azure adiciona tag de serviço](./media/hdinsight-service-tags/azure-portal-add-service-tag.png)

Esta tag contém os endereços IP de serviços de saúde e gerenciamento para todas as regiões onde o HDInsight está disponível e garantirá que seu cluster possa se comunicar com os serviços de saúde e gerenciamento necessários, não importa onde seja criado.

## <a name="use-regional-hdinsight-service-tags"></a>Use tags de serviço regionais HDInsight

Se a opção um não funcionar porque você precisa de permissões mais restritivas, então você pode permitir apenas as tags de serviço aplicáveis para sua região. As tags de serviço aplicáveis podem ser uma, duas ou três tags de serviço, dependendo da região onde seu cluster é criado.

Para saber quais tags de serviço adicionar para sua região, leia as seguintes seções do documento.

### <a name="use-a-single-regional-service-tag"></a>Use uma única tag de serviço regional

Se você preferir a opção de tag de serviço dois, e seu cluster estiver localizado em uma das regiões listadas nesta tabela, então você só precisa adicionar uma única tag de serviço regional ao seu grupo de segurança de rede.

| País/Região | Região | Tag de serviço |
| ---- | ---- | ---- |
| Austrália | Leste da Austrália | HDInsight.AustraliaEast |
| &nbsp; | Sudeste da Austrália | HDInsight.AustraliaSoutheast |
| &nbsp; | Austrália Central | HDInsight.AustraliaCentral |
| China | Leste da China 2 | HDInsight.ChinaEast2 |
| &nbsp; | Norte da China 2 | HDInsight.ChinaNorth2 |
| Estados Unidos | Centro-Norte dos EUA | HDInsight.NorthCentralUS |
| &nbsp; | Oeste dos EUA 2 | HDInsight.WestUS2 |
| &nbsp; | Centro-Oeste dos EUA | HDInsight.WestCentralUS |
| Canada | Leste do Canadá | HDInsight.CanadaEast |
| Brasil | Sul do Brasil | HDInsight.BrazilSouth |
| Coreia do Sul | Coreia Central | HDInsight.KoreaCentral |
| &nbsp; | Sul da Coreia | HDInsight.KoreaSouth |
| Índia | Índia Central | HDInsight.CentralIndia |
| &nbsp; | Sul da Índia | HDInsight.SouthIndia |
| Japão | Oeste do Japão | HDInsight.JapanWest |
| França | França Central| HDInsight.FranceCentral |
| Reino Unido | Sul do Reino Unido | HDInsight.UKSouth |
| Azure Government | USDoD Central   | HDInsight.USDoDCentral |
| &nbsp; | Gov. dos EUA – Texas | HDInsight.USGovTexas |
| &nbsp; | Usdod Leste | HDInsight.USDoDEast |
| &nbsp; | Gov. dos EUA – Arizona | HDInsight.USGovArizona |

### <a name="use-multiple-regional-service-tags"></a>Use várias tags de serviço regionais

Se você preferir a opção de tag de serviço dois, e a região onde seu cluster foi criado não foi listada acima, então você precisa permitir várias tags de serviço regionais. A necessidade de usar mais de um deve-se a diferenças no arranjo de provedores de recursos para as diversas regiões.

As demais regiões são divididas em grupos com base em quais etiquetas de serviço regionais eles usam.

#### <a name="group-1"></a>Grupo 1

Se o seu cluster for criado em uma das regiões `HDInsight.WestUS` da `HDInsight.EastUS` tabela abaixo, permita as tags de serviço e, além da tag de serviço regional listada. As regiões desta seção exigem três tags de serviço.

Por exemplo, se o `East US 2` cluster for criado na região, você precisará adicionar as seguintes tags de serviço ao seu grupo de segurança de rede:

- `HDInsight.EastUS2`
- `HDInsight.WestUS`
- `HDInsight.EastUS`

| País/Região | Região | Tag de serviço |
| ---- | ---- | ---- |
| Estados Unidos | Leste dos EUA 2 | HDInsight.EastUS2 |
| &nbsp; | Centro dos EUA | HDInsight.CentralUS |
| &nbsp; | Norte-norte dos EUA | O HDInsight. Central do Norte |
| &nbsp; | Centro-Sul dos Estados Unidos | HDInsight.SouthCentralUS |
| &nbsp; | Leste dos EUA | HDInsight.EastUS |
| &nbsp; | Oeste dos EUA | HDInsight.WestUS |
| Japão | Leste do Japão | HDInsight.JapanEast |
| Europa | Norte da Europa | HDInsight.NorthEurope |
| &nbsp; | Europa Ocidental| HDInsight.WestEurope |
| Ásia | Leste da Ásia | HDInsight.EastAsia |
| &nbsp; | Sudeste Asiático | HDInsight.SoutheastAsia |
| Austrália | Leste da Austrália | HDInsight.AustraliaEast |

#### <a name="group-2"></a>Grupo 2

Os clusters nas regiões da **China Norte** e China `HDInsight.ChinaNorth` **Leste,** precisam permitir duas etiquetas de serviço: e `HDInsight.ChinaEast`.

#### <a name="group-3"></a>Grupo 3

Os clusters nas regiões dos **EUA Gov Iowa** e Us Gov **Virginia**, precisam permitir duas etiquetas de serviço: `HDInsight.USGovIowa` e `HDInsight.USGovVirginia`.

#### <a name="group-4"></a>Grupo 4

Os clusters nas regiões da **Alemanha Central** e Alemanha `HDInsight.GermanyCentral` **Nordeste,** precisam permitir duas etiquetas de serviço: e `HDInsight.GermanyNorthEast`.

## <a name="next-steps"></a>Próximas etapas

- [Grupos de segurança de rede - tags de serviço](../virtual-network/security-overview.md#security-rules)
- [Crie redes virtuais para clusters Azure HDInsight](hdinsight-create-virtual-network.md)
