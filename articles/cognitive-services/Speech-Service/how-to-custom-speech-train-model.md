---
title: Treinar e implantar um Fala Personalizada modelo-serviço de fala
titleSuffix: Azure Cognitive Services
description: Neste artigo, você aprenderá a treinar e implantar modelos de Fala Personalizada. Treinar um modelo de fala a texto pode melhorar a precisão do reconhecimento do modelo de linha de base da Microsoft ou de um modelo personalizado. Um modelo é treinado usando transcrições de rótulo humano e texto relacionado.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/11/2020
ms.author: trbye
ms.openlocfilehash: 9193da2f3841bb94aa395399c31d7fe826c395e0
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/21/2020
ms.locfileid: "95025592"
---
# <a name="train-and-deploy-a-custom-speech-model"></a>Treinar e implantar um modelo de Fala Personalizada

Neste artigo, você aprenderá a treinar e implantar modelos de Fala Personalizada. Treinar um modelo de fala a texto pode melhorar a precisão do reconhecimento para o modelo de linha de base da Microsoft. Um modelo é treinado usando transcrições de rótulo humano e texto relacionado. Esses conjuntos de dados, juntamente com os de áudio carregados anteriormente, são usados para refinar e treinar o modelo de fala a texto.

## <a name="use-training-to-resolve-accuracy-issues"></a>Use o treinamento para resolver problemas de precisão

Se você estiver encontrando problemas de reconhecimento com um modelo base, o uso de transcrições com rótulo humano e dados relacionados para treinar um modelo personalizado pode ajudar a melhorar a precisão. Use esta tabela para determinar qual conjunto de de que será usado para resolver seus problemas:

| Caso de uso | Tipo de dados |
| -------- | --------- |
| Melhore a precisão do reconhecimento no vocabulário e na gramática específicos do setor, como terminologia médica ou jargão de ti. | Texto relacionado (sentenças/declarações) |
| Defina a forma fonética e exibida de uma palavra ou termo que tenha pronúncia não padrão, como nomes de produtos ou acrônimos. | Texto relacionado (pronúncia) |
| Melhore a precisão do reconhecimento em estilos de fala, ênfases ou ruídos de fundo específicos. | Áudio + transcrições com rótulo humano |

## <a name="train-and-evaluate-a-model"></a>Treinar e avaliar um modelo

A primeira etapa para treinar um modelo é carregar dados de treinamento. Use [preparar e testar seus dados](./how-to-custom-speech-test-and-train.md) para obter instruções passo a passo para preparar transcrições com rótulo humano e texto relacionado (declarações e pronúncias). Depois de carregar os dados de treinamento, siga estas instruções para começar a treinar seu modelo:

1. Entre no portal de [fala personalizada](https://speech.microsoft.com/customspeech).
2. Navegue até **conversão de fala em texto > Fala Personalizada > [nome do projeto] > treinamento**.
3. Clique em **treinar modelo**.
4. Em seguida, dê um **nome** e uma **Descrição** ao seu treinamento.
5. No menu suspenso **cenário e modelo de linha de base** , selecione o cenário que melhor se adapta ao seu domínio. Se você não tiver certeza de qual cenário escolher, selecione **geral**. O modelo de linha de base é o ponto de partida para treinamento. O modelo mais recente geralmente é a melhor opção.
6. Na página **selecionar dados de treinamento** , escolha um ou vários conjuntos de itens de áudio e de transcrição com rótulo humano que você gostaria de usar para treinamento.
7. Quando o treinamento for concluído, você poderá optar por executar o teste de exatidão no modelo treinado recentemente. Esta etapa é opcional.
8. Selecione **criar** para criar seu modelo personalizado.

A tabela de treinamento exibe uma nova entrada que corresponde a esse modelo recém-criado. A tabela também exibe o status: processamento, com êxito, com falha.

Veja [como](how-to-custom-speech-evaluate-data.md) avaliar e melhorar a precisão do modelo de fala personalizada. Se você optar por testar a precisão, é importante selecionar um conjunto de um banco de uma diferente do que você usou com seu modelo para obter uma noção realista do desempenho do modelo.

## <a name="deploy-a-custom-model"></a>Implantar um modelo personalizado

Depois de carregar e inspecionar dados, avaliar a precisão e treinar um modelo personalizado, você pode implantar um ponto de extremidade personalizado para usar com seus aplicativos, ferramentas e produtos. 

Para criar um novo ponto de extremidade personalizado, entre no [portal de fala personalizada](https://speech.microsoft.com/customspeech) e selecione **implantação** no menu fala personalizada na parte superior da página. Se esta for sua primeira execução, você observará que não há nenhum ponto de extremidade listado na tabela. Depois de criar um ponto de extremidade, use essa página para acompanhar cada ponto de extremidade implantado.

Em seguida, selecione **Adicionar ponto de extremidade** e insira um **nome** e uma **Descrição** para o ponto de extremidade personalizado. Em seguida, selecione o modelo personalizado que você deseja associar a esse ponto de extremidade. Nessa página, você também pode habilitar o registro em log. O registro em log permite monitorar o tráfego do ponto de extremidade. Se desabilitado, o tráfego não será armazenado.

![Como implantar um modelo](./media/custom-speech/custom-speech-deploy-model.png)

> [!NOTE]
> Não se esqueça de aceitar os termos de uso e os detalhes de preços.

Em seguida, selecione **Criar**. Essa ação retorna para a página de **implantação** . A tabela agora inclui uma entrada que corresponde ao seu ponto de extremidade personalizado. O status do ponto de extremidade mostra seu estado atual. Pode levar até 30 minutos para criar uma instância de um novo ponto de extremidade usando seus modelos personalizados. Quando o status da implantação for alterado para **concluído**, o ponto de extremidade estará pronto para uso.

Depois que o ponto de extremidade for implantado, o nome do ponto de extremidade aparecerá como um link. Clique no link para exibir informações específicas para seu ponto de extremidade, como a chave do ponto de extremidade, a URL do ponto de extremidade e o código de exemplo.

## <a name="view-logging-data"></a>Exibir dados de log

Os dados de log estão disponíveis para download em **detalhes do ponto de extremidade >**.
> [!NOTE]
>Os dados de log estão disponíveis por 30 dias no armazenamento de propriedade da Microsoft e serão removidos posteriormente. Caso uma conta de armazenamento de cliente seja vinculada à assinatura de serviços cognitivas, os dados de log não serão excluídos automaticamente.

## <a name="next-steps"></a>Próximas etapas

* Saiba [como usar seu modelo personalizado](how-to-specify-source-language.md).

## <a name="additional-resources"></a>Recursos adicionais

- [Preparar e testar seus dados](./how-to-custom-speech-test-and-train.md)
- [Inspecione seus dados](how-to-custom-speech-inspect-data.md)
- [Avalie seus dados](how-to-custom-speech-evaluate-data.md)
