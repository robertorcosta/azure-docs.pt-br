---
title: Plano Premium de Funções do Azure
description: Detalhes e opções de configuração (VNet, sem início a frio, duração de execução ilimitada) para o plano Premium de Funções do Azure.
author: jeffhollan
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: jehollan
ms.openlocfilehash: dd7f6d0760f2b848435e7c77657e261517d29dd8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276901"
---
# <a name="azure-functions-premium-plan"></a>Plano Premium de Funções do Azure

O plano Azure Functions Premium (às vezes chamado de plano Elastic Premium) é uma opção de hospedagem para aplicativos de função. O plano Premium oferece recursos como conectividade VNet, sem partida a frio e hardware premium.  Vários aplicativos de função podem ser implantados no mesmo plano Premium, e o plano permite configurar o tamanho da instância da computação, o tamanho do plano base e o tamanho máximo do plano.  Para uma comparação do plano Premium e de outros tipos de plano e hospedagem, consulte [a escala de funções e as opções de hospedagem](functions-scale.md).

## <a name="create-a-premium-plan"></a>Criar um plano Premium

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]

Você também pode criar um plano Premium usando [o az functionapp plan create](/cli/azure/functionapp/plan#az-functionapp-plan-create) no Azure CLI. O exemplo a seguir cria um plano de nível _Elastic Premium 1:_

```azurecli-interactive
az functionapp plan create --resource-group <RESOURCE_GROUP> --name <PLAN_NAME> \
--location <REGION> --sku EP1
```

Neste exemplo, `<RESOURCE_GROUP>` substitua por `<PLAN_NAME>` seu grupo de recursos e por um nome para o seu plano que é único no grupo de recursos. Especifique um [suporte `<REGION>` ](https://azure.microsoft.com/global-infrastructure/services/?products=functions). Para criar um plano Premium que `--is-linux` suporte o Linux, inclua a opção.

Com o plano criado, você pode usar [az functionapp create](/cli/azure/functionapp#az-functionapp-create) para criar seu aplicativo de função. No portal, tanto o plano quanto o aplicativo são criados ao mesmo tempo. Para um exemplo de um script CLI completo do Azure, consulte [Criar um aplicativo de função em um plano Premium](scripts/functions-cli-create-premium-plan.md).

## <a name="features"></a>Recursos

Os seguintes recursos estão disponíveis para funcionar aplicativos implantados em um plano Premium.

### <a name="pre-warmed-instances"></a>Casos pré-aquecidos

Se não ocorrerem eventos e execuções hoje no plano Consumo, seu aplicativo pode chegar a zero instâncias. Quando novos eventos chegam, uma nova instância precisa ser especializada com o seu aplicativo rodando nele.  A especialização de novas instâncias pode levar algum tempo, dependendo do aplicativo.  Essa latência adicional na primeira chamada é muitas vezes chamada de início frio do aplicativo.

No plano Premium, você pode ter seu aplicativo pré-aquecido em um número especificado de instâncias, até o seu tamanho mínimo do plano.  As instâncias pré-aquecidas também permitem pré-dimensionar um aplicativo antes da alta carga. À medida que o aplicativo se escora, ele primeiro escala para as instâncias pré-aquecidas. As instâncias adicionais continuam a se proteger e aquecer imediatamente em preparação para a próxima operação de escala. Ao ter um buffer de instâncias pré-aquecidas, você pode efetivamente evitar latências de início frio.  As instâncias pré-aquecidas são uma característica do plano Premium, e você precisa manter pelo menos uma instância em execução e disponível em todos os momentos em que o plano estiver ativo.

Você pode configurar o número de instâncias pré-aquecidas no portal Azure selecionando seu **aplicativo de função,** indo para a guia **Recursos da plataforma** e selecionando as opções **'Dimensionar'.** Na janela de edição do aplicativo de função, as instâncias pré-aquecidas são específicas para esse aplicativo, mas as instâncias mínima e máxima se aplicam a todo o seu plano.

![Configurações da escala elástica](./media/functions-premium-plan/scale-out.png)

Você também pode configurar instâncias pré-aquecidas para um aplicativo com o Azure CLI.

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.preWarmedInstanceCount=<desired_prewarmed_count> --resource-type Microsoft.Web/sites
```

### <a name="private-network-connectivity"></a>Conectividade de rede privada

As funções do Azure implantadas em um plano Premium aproveitam [a nova integração do VNet para aplicativos web](../app-service/web-sites-integrate-with-vnet.md).  Quando configurado, seu aplicativo pode se comunicar com recursos dentro de seu VNet ou protegido através de pontos finais de serviço.  As restrições de IP também estão disponíveis no aplicativo para restringir o tráfego de entrada.

Ao atribuir uma sub-rede ao seu aplicativo de função em um plano Premium, você precisa de uma sub-rede com endereços IP suficientes para cada instância potencial. Precisamos de um bloco IP com pelo menos 100 endereços disponíveis.

Para obter mais informações, consulte [integrar seu aplicativo de função com um VNet](functions-create-vnet.md).

### <a name="rapid-elastic-scale"></a>Escala elástica rápida

Instâncias adicionais de computação são adicionadas automaticamente para o seu aplicativo usando a mesma lógica de dimensionamento rápido que o plano De consumo.  Para saber mais sobre como funciona o dimensionamento, consulte [escala de função e hospedagem](./functions-scale.md#how-the-consumption-and-premium-plans-work).

### <a name="longer-run-duration"></a>Maior duração do tempo de execução

As funções do Azure em um plano de consumo são limitadas a 10 minutos para uma única execução.  No plano Premium, a duração da execução é padrão de 30 minutos para evitar execuções descontroladas. No entanto, você pode [modificar a configuração host.json](./functions-host-json.md#functiontimeout) para tornar isso ilimitado para aplicativos de plano Premium (garantido 60 minutos).

## <a name="plan-and-sku-settings"></a>Configurações de plano e SKU

Ao criar o plano, você configura duas configurações: o número mínimo de instâncias (ou tamanho do plano) e o limite máximo de estouro.  As instâncias mínimas são reservadas e sempre em execução.

> [!IMPORTANT]
> Você é cobrado por cada instância alocada na contagem mínima de instâncias, independentemente de as funções estarem sendo executadas ou não.

Se o seu aplicativo exigir instâncias além do tamanho do seu plano, ele pode continuar a dimensionar até que o número de instâncias atinja o limite máximo de estouro.  Você é cobrado por casos além do tamanho do seu plano apenas enquanto eles estão correndo e alugados para você.  Faremos um esforço melhor para escalar seu aplicativo para o limite máximo definido, enquanto as instâncias mínimas do plano são garantidas para o seu aplicativo.

Você pode configurar o tamanho e os máximos do plano no portal Azure selecionando as opções **Scale Out** no plano ou um aplicativo de função implantado nesse plano (em Recursos **de Plataforma**).

Você também pode aumentar o limite máximo de estouro da CLI do Azure:

```azurecli-interactive
az resource update -g <resource_group> -n <premium_plan_name> --set properties.maximumElasticWorkerCount=<desired_max_burst> --resource-type Microsoft.Web/serverfarms 
```

### <a name="available-instance-skus"></a>Exemplo disponível SKUs

Ao criar ou dimensionar seu plano, você pode escolher entre três tamanhos de instância.  Você será cobrado pelo número total de núcleos e memória consumidos por segundo.  Seu aplicativo pode dimensionar automaticamente para várias instâncias, conforme necessário.  

|SKU|Núcleos|Memória|Armazenamento|
|--|--|--|--|
|EP1|1|3,5 GB|250gb|
|EP2|2|7 GB|250gb|
|EP3|4|14 GB|250gb|

### <a name="memory-utilization-considerations"></a>Considerações de utilização de memória
Rodar em uma máquina com mais memória nem sempre significa que seu aplicativo de função usará toda a memória disponível.

Por exemplo, um aplicativo de função JavaScript é limitado pelo limite de memória padrão no Node.js. Para aumentar esse limite de memória `languageWorkers:node:arguments` fixa, `--max-old-space-size=<max memory in MB>`adicione a configuração do aplicativo com um valor de .

## <a name="region-max-scale-out"></a>Escala máxima da região

Abaixo estão os valores de escala máxima suportados atualmente para um único plano em cada região e configuração do Sistema Operacional. Para solicitar um aumento, abra um bilhete de suporte.

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
|Alemanha Centro-Oeste| 100 | Não disponível |
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
> [Entenda a escala de funções do Azure e as opções de hospedagem](functions-scale.md)
