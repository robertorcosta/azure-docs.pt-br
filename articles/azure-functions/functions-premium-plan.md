---
title: Azure Functions plano Premium
description: Detalhes e opções de configuração (VNet, sem início frio, duração de execução ilimitada) para o plano Azure Functions Premium.
author: jeffhollan
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: jehollan
ms.openlocfilehash: dd7f6d0760f2b848435e7c77657e261517d29dd8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "79276901"
---
# <a name="azure-functions-premium-plan"></a>Azure Functions plano Premium

O plano Azure Functions Premium (às vezes chamado de plano Premium elástico) é uma opção de hospedagem para aplicativos de funções. O plano Premium fornece recursos como conectividade VNet, sem início frio e hardware Premium.  Vários aplicativos de funções podem ser implantados no mesmo plano Premium e o plano permite que você configure o tamanho da instância de computação, o tamanho do plano base e o tamanho máximo do plano.  Para obter uma comparação do plano Premium e outros tipos de plano e hospedagem, consulte [Opções de escala e Hospedagem de função](functions-scale.md).

## <a name="create-a-premium-plan"></a>Criar um plano Premium

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]

Você também pode criar um plano Premium usando [AZ functionapp Plan Create](/cli/azure/functionapp/plan#az-functionapp-plan-create) na CLI do Azure. O exemplo a seguir cria um plano de camada _Premium 1 elástico_ :

```azurecli-interactive
az functionapp plan create --resource-group <RESOURCE_GROUP> --name <PLAN_NAME> \
--location <REGION> --sku EP1
```

Neste exemplo, substitua `<RESOURCE_GROUP>` pelo seu grupo de recursos e `<PLAN_NAME>` por um nome para seu plano que seja exclusivo no grupo de recursos. Especifique um [suporte `<REGION>` ](https://azure.microsoft.com/global-infrastructure/services/?products=functions). Para criar um plano Premium que ofereça suporte ao Linux, `--is-linux` inclua a opção.

Com o plano criado, você pode usar [AZ functionapp Create](/cli/azure/functionapp#az-functionapp-create) para criar seu aplicativo de funções. No portal, o plano e o aplicativo são criados ao mesmo tempo. Para obter um exemplo de um script de CLI do Azure completo, consulte [criar um aplicativo de funções em um plano Premium](scripts/functions-cli-create-premium-plan.md).

## <a name="features"></a>Recursos

Os recursos a seguir estão disponíveis para aplicativos de funções implantados em um plano Premium.

### <a name="pre-warmed-instances"></a>Instâncias pré-passivas

Se nenhum evento e execução ocorrer hoje no plano de consumo, seu aplicativo poderá ser dimensionado para uma instância zero. Quando novos eventos chegam, uma nova instância precisa ser especializada em seu aplicativo em execução.  A especialização de novas instâncias pode levar algum tempo, dependendo do aplicativo.  Essa latência adicional na primeira chamada geralmente é chamada de inicialização a frio do aplicativo.

No plano Premium, você pode fazer com que seu aplicativo fique quente em um número especificado de instâncias, até o tamanho mínimo do plano.  As instâncias pré-configuradas também permitem que você dimensione previamente um aplicativo antes da alta carga. À medida que o aplicativo é dimensionado, ele é dimensionado primeiro para as instâncias pré-configuradas. As instâncias adicionais continuam a ficar em buffer e ficam quentes imediatamente em preparação para a próxima operação de escala. Tendo um buffer de instâncias pré-configuradas, você pode evitar latências de início frio.  As instâncias pré-configuradas são um recurso do plano Premium, e você precisa manter pelo menos uma instância em execução e disponível sempre que o plano estiver ativo.

Você pode configurar o número de instâncias pré-configuradas no portal do Azure selecionando sua **aplicativo de funções**, acessando a guia recursos da **plataforma** e selecionando as opções de **scale out** . Na janela Editar do aplicativo de funções, as instâncias pré-configuradas são específicas para esse aplicativo, mas as instâncias mínima e máxima se aplicam ao seu plano inteiro.

![Configurações de escala elástica](./media/functions-premium-plan/scale-out.png)

Você também pode configurar instâncias pré-configuradas para um aplicativo com o CLI do Azure.

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.preWarmedInstanceCount=<desired_prewarmed_count> --resource-type Microsoft.Web/sites
```

### <a name="private-network-connectivity"></a>Conectividade de rede privada

Azure Functions implantadas em um plano Premium aproveita a [nova integração de VNet para aplicativos Web](../app-service/web-sites-integrate-with-vnet.md).  Quando configurado, seu aplicativo pode se comunicar com recursos em sua VNet ou protegidos por meio de pontos de extremidade de serviço.  As restrições de IP também estão disponíveis no aplicativo para restringir o tráfego de entrada.

Ao atribuir uma sub-rede ao seu aplicativo de funções em um plano Premium, você precisa de uma sub-rede com endereços IP suficientes para cada instância em potencial. Exigimos um bloco de IP com pelo menos 100 endereços disponíveis.

Para obter mais informações, consulte [integrar seu aplicativo de funções a uma VNet](functions-create-vnet.md).

### <a name="rapid-elastic-scale"></a>Escala elástica rápida

Instâncias de computação adicionais são adicionadas automaticamente para seu aplicativo usando a mesma lógica de dimensionamento rápido que o plano de consumo.  Para saber mais sobre como o dimensionamento funciona, consulte [escala de funções e hospedagem](./functions-scale.md#how-the-consumption-and-premium-plans-work).

### <a name="longer-run-duration"></a>Duração de execução mais longa

Azure Functions em um plano de consumo são limitados a 10 minutos para uma única execução.  No plano Premium, o padrão de duração da execução é de 30 minutos para evitar execuções de fuga. No entanto, você pode [Modificar a configuração de host. JSON](./functions-host-json.md#functiontimeout) para torná-la desassociada para aplicativos de plano Premium (garantido 60 minutos).

## <a name="plan-and-sku-settings"></a>Configurações de plano e SKU

Ao criar o plano, você define duas configurações: o número mínimo de instâncias (ou o tamanho do plano) e o limite máximo de intermitência.  As instâncias mínimas são reservadas e sempre em execução.

> [!IMPORTANT]
> Você é cobrado por cada instância alocada na contagem mínima de instâncias, independentemente de as funções serem executadas ou não.

Se seu aplicativo exigir instâncias além do tamanho do plano, ele poderá continuar a escalar horizontalmente até que o número de instâncias atinja o limite máximo de intermitência.  Você será cobrado por instâncias além do tamanho do plano somente enquanto eles estiverem em execução e alugados para você.  Faremos um melhor esforço em dimensionar seu aplicativo para o limite máximo definido, enquanto que as instâncias de plano mínimas são garantidas para seu aplicativo.

Você pode configurar o tamanho do plano e os máximos no portal do Azure selecionando as opções de **scale out** no plano ou um aplicativo de funções implantado nesse plano (em **recursos da plataforma**).

Você também pode aumentar o limite máximo de intermitência do CLI do Azure:

```azurecli-interactive
az resource update -g <resource_group> -n <premium_plan_name> --set properties.maximumElasticWorkerCount=<desired_max_burst> --resource-type Microsoft.Web/serverfarms 
```

### <a name="available-instance-skus"></a>SKUs da instância disponível

Ao criar ou dimensionar seu plano, você pode escolher entre três tamanhos de instância.  Você será cobrado pelo número total de núcleos e memória consumida por segundo.  Seu aplicativo pode ser dimensionado automaticamente para várias instâncias, conforme necessário.  

|SKU|Núcleos|Memória|Armazenamento|
|--|--|--|--|
|EP1|1|3,5 GB|250GB|
|EP2|2|7 GB|250GB|
|EP3|4|14 GB|250GB|

### <a name="memory-utilization-considerations"></a>Considerações sobre utilização de memória
A execução em um computador com mais memória nem sempre significa que seu aplicativo de funções usará toda a memória disponível.

Por exemplo, um aplicativo de funções JavaScript é restrito pelo limite de memória padrão no node. js. Para aumentar esse limite de memória fixa, adicione a configuração `languageWorkers:node:arguments` do aplicativo com um `--max-old-space-size=<max memory in MB>`valor de.

## <a name="region-max-scale-out"></a>Scale Out máxima da região

Abaixo estão os valores máximos de escala horizontal atualmente com suporte para um único plano em cada região e configuração de so. Para solicitar um aumento, abra um tíquete de suporte.

Veja a disponibilidade regional completa das funções aqui: [Azure.com](https://azure.microsoft.com/global-infrastructure/services/?products=functions)

|Região| Windows | Linux |
|--| -- | -- |
|Austrália Central| 20 | Não disponível |
|Austrália Central 2| 20 | Não disponível |
|Leste da Austrália| 100 | 20 |
|Sudeste da Austrália | 100 | 20 |
|Sul do Brasil| 60 | 20 |
|Canadá Central| 100 | 20 |
|Centro dos EUA| 100 | 20 |
|Leste da Ásia| 100 | 20 |
|Leste dos EUA | 100 | 20 |
|Leste dos EUA 2| 100 | 20 |
|França Central| 100 | 20 |
|Centro-oeste da Alemanha| 100 | Não disponível |
|Leste do Japão| 100 | 20 |
|Oeste do Japão| 100 | 20 |
|Coreia Central| 100 | 20 |
|Centro-Norte dos EUA| 100 | 20 |
|Norte da Europa| 100 | 20 |
|Leste da Noruega| 20 | 20 |
|Centro-Sul dos Estados Unidos| 100 | 20 |
|Sul da Índia | 100 | Não disponível |
|Sudeste Asiático| 100 | 20 |
|Sul do Reino Unido| 100 | 20 |
|Oeste do Reino Unido| 100 | 20 |
|Europa Ocidental| 100 | 20 |
|Oeste da Índia| 100 | 20 |
|Centro-Oeste dos EUA| 20 | 20 |
|Oeste dos EUA| 100 | 20 |
|Oeste dos EUA 2| 100 | 20 |

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Entender Azure Functions escala e opções de hospedagem](functions-scale.md)
