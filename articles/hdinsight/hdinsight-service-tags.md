---
title: Tags de serviço do grupo de segurança de rede (NSG) para Azure HDInsight
description: Use as tags de serviço HDInsight para permitir tráfego de entrada para o cluster a partir de nós de serviços de saúde e gerenciamento, sem adicionar endereços IP aos seus NSGs.
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 03/10/2020
ms.openlocfilehash: 34ec05a8362f5947cb61924b19c6b1a52e5d91a4
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437667"
---
# <a name="nsg-service-tags-for-azure-hdinsight"></a>Tags de serviço NSG para Azure HDInsight

As tags de serviço Azure HDInsight para grupos de segurança de rede (NSGs) são grupos de endereços IP para serviços de saúde e gerenciamento. Esses grupos ajudam a minimizar a complexidade para a criação de regras de segurança. [As tags de](../virtual-network/security-overview.md#service-tags) serviço fornecem um método alternativo para permitir tráfego de entrada de endereços IP específicos sem inserir cada um dos [endereços IP](hdinsight-management-ip-addresses.md) de gerenciamento em seus NSGs.

O serviço HDInsight gerencia essas tags de serviço. Você não pode criar sua própria tag de serviço ou modificar uma tag existente. A Microsoft gerencia os prefixos de endereço que correspondem à tag de serviço e atualiza automaticamente a tag de serviço à medida que os endereços mudam.

## <a name="get-started-with-service-tags"></a>Comece com as tags de serviço

Você tem duas opções para usar tags de serviço em seus grupos de segurança de rede:

- **Use uma única tag de serviço GLOBAL HDInsight**: Esta opção abre sua rede virtual para todos os endereços IP que o serviço HDInsight usa para monitorar clusters em todas as regiões. Esta opção é o método mais simples, mas pode não ser apropriado se você tiver requisitos de segurança restritivos.

- **Use várias tags de serviço regionais**: Esta opção abre sua rede virtual apenas para os endereços IP que o HDInsight usa nessa região específica. No entanto, se você estiver usando várias regiões, você precisará adicionar várias tags de serviço à sua rede virtual.

## <a name="use-a-single-global-hdinsight-service-tag"></a>Use uma única tag global de serviço HDInsight

A maneira mais fácil de começar a usar tags de `HDInsight` serviço com seu cluster HDInsight é adicionar a tag global a uma regra NSG.

1. No [portal Azure,](https://portal.azure.com/)selecione seu grupo de segurança de rede.

1. Em **Configurações,** selecione **Regras de segurança de entrada**e selecione + **Adicionar**.

1. Na **lista** de paradas de origem, selecione **'Tag**de serviço ''''''''

1. Na lista de saque saudosista da tag de **serviço Origem,** selecione **HDInsight**.

    ![Adicione uma tag de serviço do portal Azure](./media/hdinsight-service-tags/azure-portal-add-service-tag.png)

Esta tag contém os endereços IP de serviços de saúde e gerenciamento para todas as regiões onde o HDInsight está disponível. A tag garantirá que seu cluster possa se comunicar com os serviços de saúde e gerenciamento necessários, não importa onde seja criado.

## <a name="use-regional-hdinsight-service-tags"></a>Use tags de serviço regionais HDInsight

Se a opção de tag global não funcionar porque você precisa de permissões mais restritivas, você pode permitir apenas as tags de serviço aplicáveis para sua região. Pode haver uma, duas ou três tags de serviço aplicáveis, dependendo da região onde seu cluster é criado.

Para saber quais tags de serviço adicionar para sua região, leia as seguintes seções do artigo.

### <a name="use-a-single-regional-service-tag"></a>Use uma única tag de serviço regional

Se você preferir usar tags de serviço regionais e seu cluster estiver localizado em uma das regiões listadas nesta tabela, você só precisa adicionar uma única tag de serviço regional ao seu grupo de segurança de rede.

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
| Azure Government | USDoD Central | HDInsight.USDoDCentral |
| &nbsp; | Gov. dos EUA – Texas | HDInsight.USGovTexas |
| &nbsp; | Usdod Leste | HDInsight.USDoDEast |
| &nbsp; | Gov. dos EUA – Arizona | HDInsight.USGovArizona |

### <a name="use-multiple-regional-service-tags"></a>Use várias tags de serviço regionais

Se você preferir usar tags de serviço regionais, mas a região onde seu cluster é criado não estava listada na tabela anterior, você precisa permitir várias tags de serviço regionais. A necessidade de usar mais de um deve-se a diferenças no arranjo de provedores de recursos para as diversas regiões.

As demais regiões são divididas em grupos com base em quais etiquetas de serviço regionais eles usam.

#### <a name="group-1"></a>Grupo 1

Se o cluster for criado em uma das regiões na `HDInsight.WestUS` tabela `HDInsight.EastUS` a seguir, permita as tags de serviço e, além disso, a tag de serviço regional listada. As regiões desta seção exigem três tags de serviço.

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

Os clusters nas regiões da *China Norte* e China `HDInsight.ChinaNorth` `HDInsight.ChinaEast` *Leste* precisam permitir duas etiquetas de serviço: e .

#### <a name="group-3"></a>Grupo 3

Os clusters nas regiões dos *EUA Gov Iowa* e US Gov *Virginia* precisam permitir duas etiquetas de serviço: `HDInsight.USGovIowa` e `HDInsight.USGovVirginia`.

#### <a name="group-4"></a>Grupo 4

Os clusters nas regiões da *Alemanha Central* e Alemanha `HDInsight.GermanyCentral` `HDInsight.GermanyNortheast` *Nordeste* precisam permitir duas etiquetas de serviço: e .

## <a name="next-steps"></a>Próximas etapas

- [Grupos de segurança de rede: tags de serviço](../virtual-network/security-overview.md#security-rules)
- [Crie redes virtuais para clusters Azure HDInsight](hdinsight-create-virtual-network.md)
