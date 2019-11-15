---
title: Visão geral do Azure FarmBeats
description: Fornece uma visão geral do Azure FarmBeats
author: uhabiba04
ms.topic: overview
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 12882860b8a9beadd4e56cbb151d670fac4da43b
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73797152"
---
# <a name="overview-of-azure-farmbeats"></a>Visão geral do Azure FarmBeats

O Azure FarmBeats é uma coleção de recursos e serviços do Azure, projetada para ajudá-lo a criar rapidamente soluções inteligentes controladas por dados de agricultura. O Azure FarmBeats é uma oferta do Azure Marketplace que permite adquirir, agregar e processar dados relacionados à agricultura a partir de várias fontes como sensores, drones, câmeras ou satélites, sem ter que investir em recursos de engenharia de dados detalhados.

> [!NOTE]
> O Azure FarmBeats está atualmente em versão prévia pública. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). O Azure FarmBeats é fornecido sem um contrato de nível de serviço. Use o [fórum do Azure FarmBeats](https://aka.ms/FarmBeatsMSDN ) para obter suporte.

Com o Azure FarmBeats, você pode obter dados de várias fontes, como sensores, satélite, drones, tudo no contexto de uma fazenda (uma área geográfica de interesse).

Você poderá:

- Agregar conjuntos de dados agrícolas de vários provedores
- Compilar rapidamente modelos de IA/ML (inteligência artificial/linguagem de máquina) ao unificar diferentes conjuntos de dados

O Azure FarmBeats proporciona uma maneira fácil e robusta de executar o seguinte:

- Criar um mapa da fazenda usando o arquivo GeoJSON.
- Avaliar a integridade da fazenda usando os índices de vegetação e de água baseados em imagens de satélite.
- Obter recomendações sobre quantos sensores devem ser usados e onde posicioná-los.
- Controlar a condição da fazenda ao visualizar os dados do terreno coletados de sensores de vários fornecedores.
- Obter o mapa de umidade do solo com base em dados unificados de sensores e satélites.
- Obter insights acionáveis ao complicar modelos de IA/ML usando conjuntos de dados agregados.
- Compilar ou aumentar a solução digital agrícola ao fornecer consultorias de integridade sobre a fazenda.

Os componentes do Azure FarmBeat são apresentados nas próximas seções deste artigo.

## <a name="data-hub"></a>Hub de dados

Uma camada de API que permite a agregação, normalização e contextualização de vários conjuntos de dados agrícolas entre provedores. Nesta versão prévia, é possível utilizar dois provedores de sensores, [Davis Instruments](https://www.davisinstruments.com/product/enviromonitor-gateway/) e [Teralytic](https://teralytic.com/), um provedor de imagens de satélite, [Sentinel-2](https://sentinel.esa.int/web/sentinel/home), e dois provedores de imagens de drones, [senseFly](https://www.sensefly.com/) e [SlantRange](https://slantrange.com/). O hub de dados foi desenvolvido como uma plataforma de API e estamos trabalhando com muitos outros provedores para integrar com o Azure FarmBeats, assim você terá mais opções ao compilar sua solução.

## <a name="accelerator"></a>Acelerador

Uma solução de exemplo, criada com base no hub de dados, que impulsiona a interface do usuário e o desenvolvimento de modelos. Esse aplicativo Web aproveita as APIs para demonstrar a visualização de dados de sensores ingeridos como gráficos e a visualização de saídas de modelos como mapas. Por exemplo, você pode usar o acelerador para criar rapidamente uma fazenda e obter com facilidade um mapa de índice da vegetação ou um mapa de posicionamento de sensores para essa fazenda.

## <a name="resources"></a>Recursos

Visite o [blog](https://aka.ms/AzureFarmBeats) e os [fóruns](https://aka.ms/FarmBeatsMSDN) do FarmBeats.

## <a name="next-steps"></a>Próximas etapas

Para começar a usar o Azure FarmBeats, visite o [Azure Marketplace](https://aka.ms/FarmBeatsMarketplace) para implantá-lo.
