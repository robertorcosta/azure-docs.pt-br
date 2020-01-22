---
title: 'Tutorial: Migrar do Google Maps para o Azure Mapas | Microsoft Azure Mapas'
description: Um tutorial sobre como migrar do Google Maps para o Microsoft Azure Mapas. As diretrizes orientam você sobre como alternar para as APIs e SDKs do Azure Mapas.
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 0e841b1f386d45ddb4af8598855d8e739750307e
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/12/2020
ms.locfileid: "75910732"
---
# <a name="migrate-from-google-maps-to-azure-maps"></a>Migrar do Google Maps para o Azure Mapas

Este tutorial fornece informações sobre como migrar aplicativos Web, móveis e baseados em servidor do Google Maps para a plataforma do Microsoft Azure Mapas. Este tutorial inclui exemplos comparativos de código, sugestões de migração e práticas recomendadas para migração para o Azure Mapas.

## <a name="azure-maps-platform-overview"></a>Visão geral da plataforma do Azure Mapas

Os Azure Mapas fornecem funcionalidades geoespaciais avançadas, contendo dados de mapeamento atualizados com frequência para fornecer um contexto geográfico a aplicativos Web e móveis. O Azure Mapas tem um conjunto de APIs REST em conformidade com a API Azure One para mapas, pesquisa, roteiros, tráfego, mobilidade, clima, fusos horários, geolocalização, delimitação geográfica, dados de mapa e Operações Espaciais, acompanhado de SDKs Web e do Android para tornar o desenvolvimento fácil, flexível e portátil entre várias plataformas.

## <a name="high-level-platform-comparison"></a>Comparação de plataforma de alto nível

A tabela a seguir fornece uma lista de alto nível dos recursos do Google Maps e o suporte relativo a esses recursos no Azure Mapas. Essa lista não inclui recursos adicionais do Azure Mapas, como acessibilidade, APIs de isolamento geográfico, isócronos, operações espaciais, acesso direto a peças de mapa, serviços de Lote e comparações de cobertura de dados (ou seja, cobertura de imagens).

| Recurso do Google Maps         | Suporte no Azure Mapas                     |
|-----------------------------|:--------------------------------------:|
| SDK da Web                     | ✓                                      |
| SDK do Android                 | ✓                                      |
| SDK do iOS                     | Planejado                                |
| APIs de Serviço REST           | ✓                                      |
| Trajeto (roteiros)        | ✓                                      |
| Matriz de Distância             | ✓                                      |
| Elevação                   | Planejado                                |
| Geocódigo (avançar/recuar) | ✓                                      |
| Geolocalização                 | N/D                                    |
| Pesquisa de locais               | ✓                                      |
| Detalhes de locais              | N/A – site e número de telefone disponível |
| Fotos de locais               | N/D                                    |
| Preenchimento automático de locais          | ✓                                      |
| Mapas estáticos                 | ✓                                      |
| Exibição estática de rua          | N/D                                    |
| Fuso horário                   | ✓                                      |
| API inserida do Mapas           | N/D                                    |
| URLs de mapa                    | N/D                                    |

O Google Maps fornece autenticação básica baseada em chave. O Azure Mapas fornece a autenticação básica baseada em chave, bem como a autenticação altamente segura do Azure Active Directory.

## <a name="licensing-considerations"></a>Considerações sobre licenciamento

Ao migrar do Google Maps para o Azure Mapas, os pontos a seguir devem ser considerados em relação ao licenciamento.

- O Azure Mapas cobra pelo uso de mapas interativos com base no número de peças de mapa carregadas, enquanto o Google Maps cobra pelo carregamento do controle de mapeamento. Nos SDKs interativos do Azure Mapas, as peças de mapa são armazenadas em cache automaticamente para reduzir o custo do desenvolvedor. Para cada 15 peças de mapa que são carregadas, uma transação do Azure Mapas é gerada. Os SDKs interativos do Azure Mapas usam peças de 512 pixels e, em média, geram uma transação ou menos por exibição de página.
- Geralmente, é muito mais econômico substituir as imagens de mapa estáticas dos serviços Web do Google Maps pelo SDK Web do Azure Mapas, pois ele usa peças de mapa e, a menos que os usuários apliquem panorâmica e zoom no mapa, muitas vezes eles gerarão apenas uma fração de uma transação por carregamento de mapa. O SDK Web do Azure Mapas tem opções para desabilitar a aplicação de panorâmica e de zoom. Além disso, o SDK Web do Azure Mapas fornece muito mais opções de visualização do que um serviço Web de mapa estático.
- O Azure Mapas permite que os dados de sua plataforma sejam armazenados no Azure. Eles também podem ser armazenados em cache em outro lugar por até seis meses, de acordo com os [Termos de uso](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=46).

Aqui estão alguns recursos relacionados para o Azure Mapas:

- [Página de preços do Azure Mapas](https://azure.microsoft.com/pricing/details/azure-maps/)
- [Calculadora de preços do Azure](https://azure.microsoft.com/pricing/calculator/?service=azure-maps)
- [Termo de uso do Azure Mapas](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=46) (incluído nos termos do Microsoft Online Services)
- [Escolha o tipo de preço certo no Azure Mapas](https://docs.microsoft.com/azure/azure-maps/choose-pricing-tier)

## <a name="suggested-migration-plan"></a>Plano de migração sugerido

O plano de migração a seguir é de alto nível.

1. Faça o inventário de quais SDKs e serviços do Google Maps o seu aplicativo está usando e verifique se o Azure Mapas fornece SDKs e serviços alternativos para os quais você possa migrar.
2. Crie uma assinatura do Azure (caso você ainda não tenha uma) em [https://azure.com](https://azure.com).
3. Crie uma conta ([documentação](https://docs.microsoft.com/azure/azure-maps/how-to-manage-account-keys)) e chave de autenticação do Azure Mapas ou do Azure Active Directory ([documentação](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication)).
4. Migre o código do aplicativo.
5. Teste seu aplicativo migrado.
6. Implante seu aplicativo migrado para produção.

## <a name="azure-maps-technical-resources"></a>Recursos técnicos do Azure Mapas

Aqui está uma lista de recursos técnicos úteis para o Azure Mapas.

- Visão geral: [https://azure.com/maps](https://azure.com/maps)
- Documentação: [https://aka.ms/AzureMapsDocs](https://aka.ms/AzureMapsDocs)
- Exemplos de código do SDK Web: [https://aka.ms/AzureMapsSamples](https://aka.ms/AzureMapsSamples)
- Fóruns de desenvolvedores: [https://aka.ms/AzureMapsForums](https://aka.ms/AzureMapsForums)
- Vídeos: [https://aka.ms/AzureMapsVideos](https://aka.ms/AzureMapsVideos)
- Blog: [https://aka.ms/AzureMapsBlog](https://aka.ms/AzureMapsBlog)
- Comentários do Azure Mapas (UserVoice): [https://aka.ms/AzureMapsFeedback](https://aka.ms/AzureMapsFeedback)

## <a name="migration-support"></a>Suporte à migração

Os desenvolvedores podem buscar suporte à migração por meio dos [fóruns](https://aka.ms/AzureMapsForums) ou por meio de uma das muitas opções de Suporte do Azure: [https://azure.microsoft.com/support/options](https://azure.microsoft.com/support/options)

## <a name="next-steps"></a>Próximas etapas

Saiba como migrar seu aplicativo do Google Maps com estes artigos:

> [!div class="nextstepaction"]
> [Migrar um aplicativo Web](migrate-from-google-maps-web-app.md)

> [!div class="nextstepaction"]
> [Migrar um aplicativo Android](migrate-from-google-maps-android-app.md)

> [!div class="nextstepaction"]
> [Migrar um serviço Web](migrate-from-google-maps-web-services.md)
