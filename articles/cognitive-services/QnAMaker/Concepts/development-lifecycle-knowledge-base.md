---
title: Ciclo de vida da base de dados de conhecimento – QnA Maker
description: O QnA Maker aprende melhor em um ciclo iterativo de alterações de modelo, exemplos de expressão, publicação e coleta de dados de consultas de ponto de extremidade.
ms.topic: conceptual
ms.date: 02/27/2020
ms.openlocfilehash: 98fbd81baa717c981486f33cfb2b3a608cec27c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77914945"
---
# <a name="knowledge-base-lifecycle-in-qna-maker"></a>Ciclo de vida da base de dados de conhecimento no QnA Maker
O QnA Maker aprende melhor em um ciclo iterativo de alterações de modelo, exemplos de expressão, publicação e coleta de dados de consultas de ponto de extremidade.

![Ciclo de criação](../media/qnamaker-concepts-lifecycle/kb-lifecycle.png)

## <a name="creating-a-qna-maker-knowledge-base"></a>Criar uma base de dados de conhecimento do QnA Maker
O ponto de extremidade de KB (base de dados de conhecimento) do QnA Maker fornece uma resposta de melhor correspondência a uma consulta do usuário com base no conteúdo da base de dados de conhecimento. Criar uma base de conhecimento é uma ação única para configurar um repositório de conteúdo de perguntas, respostas e metadados associados. Uma base de dados de conhecimento pode ser criada, rastreando conteúdo pré-existente como páginas de perguntas frequentes, manuais de produtos ou pares P-R estruturados. Saiba como [criar uma base de dados de conhecimento](../quickstarts/create-publish-knowledge-base.md).

## <a name="testing-and-updating-the-knowledge-base"></a>Testar e atualizar a base de dados de conhecimento

A base de dados de conhecimento está pronta para testes, uma vez que é preenchida com conteúdo, seja editorialmente ou através de extração automática. Testes interativos podem ser feitos no portal QnA Maker através do painel **Teste,** inserindo consultas comuns do usuário e verificando se as respostas retornaram com a resposta correta e pontuação de confiança suficiente.

* **Para corrigir baixos índices de confiança**: adicione perguntas alternativas.
* **Quando uma consulta retorna incorretamente a [resposta padrão](../How-to/change-default-answer.md)**: adicione novas respostas à pergunta correta.

Este loop estreito de atualização de teste continuará até que você esteja satisfeito com os resultados. Saiba como [testar a base de dados de conhecimento](../How-To/test-knowledge-base.md).

Para kbs grandes, use testes automatizados com `isTest` a [API gerarAnswer](../how-to/metadata-generateanswer-usage.md#get-answer-predictions-with-the-generateanswer-api) e a propriedade do corpo, que consulta a `test` base de conhecimento em vez da base de conhecimento publicada.

```json
{
  "question": "example question",
  "top": 3,
  "userId": "Default",
  "isTest": true
}
```

## <a name="publish-the-knowledge-base"></a>Publicar a base de dados de conhecimento
Quando terminar de testar a base de dados de conhecimento, você poderá publicá-la. Publish empurra a versão mais recente da base de conhecimento testada para um índice dedicado de Pesquisa Cognitiva do Azure representando a base de conhecimento **publicada.** Isso também cria um ponto de extremidade que pode ser chamado no aplicativo ou chat bot.

Dessa forma, quaisquer alterações feitas na versão de teste da base de dados de conhecimento não afetam a versão publicada que pode estar ativa em um aplicativo de produção.

Cada uma dessas bases de dados de conhecimento pode ser direcionada para testes separadamente. Usando as APIs, você pode segmentar a `isTest` versão de teste da base de conhecimento com propriedade do corpo na chamada gerar resposta.

Saiba como [publicar a base de dados de conhecimento](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base).

## <a name="monitor-usage"></a>Monitorar o uso
Para registrar os logs de chat do seu serviço, é necessário habilitar o Application Insights ao [criar o serviço do QnA Maker](../How-To/set-up-qnamaker-service-azure.md).

É possível obter várias análises do uso do serviço. Saiba mais sobre como usar o Application Insights para obter [análises do serviço do QnA Maker](../How-To/get-analytics-knowledge-base.md).

De acordo com o que aprende-se com as análises, faça as [atualizações da base de dados de conhecimento](../How-To/edit-knowledge-base.md) apropriadas.

## <a name="version-control-for-data-in-your-knowledge-base"></a>Controle de versão para dados em sua base de conhecimento

O controle de versão para dados é fornecido através dos recursos de importação/exportação na página **Configurações** no portal QnA Maker.

Você pode fazer backup de uma base de `.tsv` conhecimento `.xls` exportando a base de conhecimento, em um ou formato. Uma vez exportado, inclua este arquivo como parte de sua verificação regular de controle de origem.

Quando você precisa voltar para uma versão específica, você precisa importar esse arquivo do seu sistema local. Uma base de conhecimento exportada só **deve** ser usada através da importação na página **Configurações.** Ele não pode ser usado como uma fonte de dados de arquivo ou documento de URL. Isso substituirá perguntas e respostas atualmente na base de conhecimento pelo conteúdo do arquivo importado.

## <a name="test-and-production-knowledge-base"></a>Base de conhecimento de teste e produção
Uma base de conhecimento é o repositório de conjuntos de perguntas e respostas criados, mantidos e usados através do QnA Maker. Cada recurso do QnA Maker pode conter várias bases de conhecimento.

Uma base de conhecimento tem dois estados: *teste* e *publicado*.

### <a name="test-knowledge-base"></a>Base de conhecimento de teste

A *base de conhecimento* do teste é a versão atualmente editada, salva e testada para precisão e completitude das respostas. As alterações feitas na base de conhecimento do teste não afetam o usuário final do seu aplicativo ou bot de bate-papo. A base de conhecimento `test` do teste é conhecida como na solicitação HTTP. O `test` conhecimento está disponível com o painel de **teste** interativo do portal do Fabricante qnA.

### <a name="production-knowledge-base"></a>Base de conhecimento de produção

A base de *conhecimento publicada* é a versão usada no seu bot ou aplicativo de bate-papo. A ação de publicação de uma base de conhecimento coloca o conteúdo da base de conhecimento de teste na versão publicada da base de conhecimento. Como a base de conhecimento publicada é a versão que o aplicativo usa através do ponto final, certifique-se de que o conteúdo está correto e bem testado. A base de conhecimento `prod` publicada é conhecida como na solicitação HTTP.


## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Sugestões ativas de aprendizagem](./active-learning-suggestions.md)