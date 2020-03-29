---
title: Política de aprendizagem - Personalizador
description: As configurações de aprendizagem determinam os *hiperparâmetros* do treinamento do modelo. Dois modelos dos mesmos dados treinados em diferentes configurações de aprendizagem acabarão diferentes.
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: abe6a2a2ec9b9978230d894c69193469f6e932e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219341"
---
# <a name="learning-policy-and-settings"></a>Política de aprendizagem e configurações

As configurações de aprendizagem determinam os *hiperparâmetros* do treinamento do modelo. Dois modelos dos mesmos dados treinados em diferentes configurações de aprendizagem acabarão diferentes.

[A política de aprendizagem e as configurações](how-to-settings.md#configure-rewards-for-the-feedback-loop) são definidas no recurso Personalizador no portal Azure.

## <a name="import-and-export-learning-policies"></a>Políticas de aprendizagem de importação e exportação

Você pode importar e exportar arquivos de política de aprendizagem do portal Azure. Use este método para salvar as políticas existentes, testá-las, substituí-las e arquivá-las no controle de código-fonte como artefatos para futuras referências e auditorias.

Saiba [como](how-to-manage-model.md#import-a-new-learning-policy) importar e exportar uma política de aprendizado no portal Azure para o seu recurso Personalizador.

## <a name="understand-learning-policy-settings"></a>Entenda as configurações das políticas de aprendizagem

As configurações da política de aprendizagem não devem ser alteradas. Alterar as configurações somente se você entender como elas afetam o Personalr. Sem esse conhecimento, você pode causar problemas, incluindo a invalidação de modelos personalizadores.

O personalização usa [vowpalwabbit](https://github.com/VowpalWabbit) para treinar e marcar os eventos. Consulte a [documentação vowpalwabbit](https://github.com/VowpalWabbit/vowpal_wabbit/wiki/Command-line-arguments) sobre como editar as configurações de aprendizagem usando vowpalwabbit. Depois de ter os argumentos corretos da linha de comando, salve o comando em um arquivo com o seguinte formato (substitua o valor da propriedade de argumentos pelo comando desejado) e carregue o arquivo para importar configurações de aprendizado no painel **Modelo e Configurações de Aprendizagem** no portal Azure para o recurso Personalizador.

O `.json` seguinte é um exemplo de uma política de aprendizagem.

```json
{
  "name": "new learning settings",
  "arguments": " --cb_explore_adf --epsilon 0.2 --power_t 0 -l 0.001 --cb_type mtr -q ::"
}
```

## <a name="compare-learning-policies"></a>Compare as políticas de aprendizagem

Você pode comparar como diferentes políticas de aprendizagem se comportam com dados passados em logs do Personalr fazendo [avaliações offline](concepts-offline-evaluation.md).

[Carregue suas próprias políticas de aprendizagem](how-to-manage-model.md) para compará-las com a política de aprendizagem atual.

## <a name="optimize-learning-policies"></a>Otimizar políticas de aprendizagem

O personalização pode criar uma política de aprendizagem otimizada em uma [avaliação offline.](how-to-offline-evaluation.md) Uma política de aprendizagem otimizada que tenha melhores recompensas em uma avaliação offline produzirá melhores resultados quando for usada online no Personalr.

Depois de otimizar uma política de aprendizado, você pode aplicá-la diretamente ao Personalr para que substitua imediatamente a política atual. Ou você pode salvar a política otimizada para uma avaliação posterior e depois decidir se descarta, salva ou aplica.

## <a name="next-steps"></a>Próximas etapas

* Aprenda [eventos ativos e inativos.](concept-active-inactive-events.md)
