---
title: Solução de problemas comuns
description: Aprenda a solucionar problemas criando, atribuindo e removendo projetos como violações de políticas e funções de parâmetro de projeto.
ms.date: 01/15/2020
ms.topic: troubleshooting
ms.openlocfilehash: 7306e344a479008a87164a954c4444d375950b0b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76157076"
---
# <a name="troubleshoot-errors-using-azure-blueprints"></a>Solucionar problemas usando o Azure Blueprint

Você pode encontrar erros ao criar, atribuir ou remover projetos. Este artigo descreve os vários erros que podem ocorrer e como resolvê-los.

## <a name="finding-error-details"></a>Encontrar detalhes do erro

Muitos erros serão o resultado da atribuição de um blueprint para um escopo. Quando uma atribuição falha, o blueprint fornece detalhes sobre a implantação com falha. Essa informação indica o problema para que ele possa ser corrigido e a próxima implantação for bem-sucedida.

1. Selecione **Todos os serviços** no painel esquerdo. Pesquise e selecione **Blueprints**.

1. Selecione **Asplantas atribuídas** na página à esquerda e use a caixa de pesquisa para filtrar as atribuições do projeto para encontrar a atribuição com falha. Você também pode classificar a tabela de atribuições de acordo com a coluna **Estado de Provisionamento** para ver todas as atribuições com falha agrupadas.

1. Clique à esquerda no projeto com o status _falho_ ou clique com o botão direito do mouse e selecione **Exibir detalhes da atribuição**.

1. Uma faixa vermelha que falhou a atribuição de aviso está na parte superior da página de atribuição de plano gráfico. Clique em qualquer lugar na faixa para obter mais detalhes.

É comum o erro a ser causado por um artefato e não o plano gráfico como um todo. Se um artefato cria um cofre de chaves e a Azure Policy impede a criação de Cofre de chaves, a atribuição inteira falhará.

## <a name="general-errors"></a>Erros gerais

### <a name="scenario-policy-violation"></a><a name="policy-violation"></a>Cenário: violação de política

#### <a name="issue"></a>Problema

A implantação de modelo falhou por causa da violação de política.

#### <a name="cause"></a>Causa

Uma política pode entrar em conflito com a implantação por vários motivos:

- O recurso que está sendo criado é restrito pela política (normalmente restrições SKU ou local)
- A implantação está definindo campos que são configurados pela política (comum com marcas)

#### <a name="resolution"></a>Resolução

Altere o plano gráfico para que ele não entre em conflito com as políticas nos detalhes do erro. Se isso não for possível, uma opção alternativa será alterar o escopo da atribuição de política para que o blueprint não esteja mais em conflito com a política.

### <a name="scenario-blueprint-parameter-is-a-function"></a><a name="escape-function-parameter"></a>Cenário: Parâmetro de projeto é uma função

#### <a name="issue"></a>Problema

Parâmetros de blueprint que são funções são processados antes de serem passados para artefatos.

#### <a name="cause"></a>Causa

Passar um parâmetro de blueprint que usa uma função, como `[resourceGroup().tags.myTag]`, para um artefato faz com que o resultado processado da função seja definido no artefato em vez da função dinâmica.

#### <a name="resolution"></a>Resolução

Para passar uma função como um parâmetro, faça o escape de toda a cadeia de caracteres com `[`, de modo que o parâmetro de blueprint se pareça com `[[resourceGroup().tags.myTag]`. O caractere de escape faz com que o Blueprints trate o valor como uma cadeia de caracteres ao processar o blueprint. O Blueprints, em seguida, coloca a função no artefato, permitindo que ela seja dinâmica conforme o esperado. Para obter mais informações, consulte [Sintaxe e expressões nos modelos do Azure Resource Manager](../../../azure-resource-manager/templates/template-expressions.md).

## <a name="delete-errors"></a>Excluir erros

### <a name="scenario-assignment-deletion-timeout"></a><a name="assign-delete-timeout"></a>Cenário: Tempo de exclusão de atribuição

#### <a name="issue"></a>Problema

A exclusão de uma atribuição de projeto não é concluída.

#### <a name="cause"></a>Causa

Uma atribuição de projeto pode ficar presa em um estado não terminal quando excluída. Esse estado é causado quando os recursos criados pela atribuição do projeto ainda estão pendentes de exclusão ou não retornam um código de status para o Azure Blueprints.

#### <a name="resolution"></a>Resolução

As atribuições do projeto em um estado não terminal são automaticamente marcadas **como falha** após um intervalo de _6 horas._ Uma vez que o tempo de tempo tenha ajustado o estado da atribuição do projeto, a exclusão pode ser repetida novamente.

## <a name="next-steps"></a>Próximas etapas

Se você não encontrou seu problema ou não conseguiu resolver seu problema, visite um dos seguintes canais para obter mais suporte:

- Obtenha respostas de especialistas do Azure através [do Azure Forums](https://azure.microsoft.com/support/forums/).
- Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) – a conta oficial do Microsoft Azure para melhorar a experiência do cliente conectando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.
- Se precisar de mais ajuda, você pode registrar um incidente de suporte do Azure. Vá ao site de suporte do [Azure](https://azure.microsoft.com/support/options/) e **selecione Obter suporte**.