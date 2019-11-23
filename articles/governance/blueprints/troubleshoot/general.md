---
title: Solução de problemas comuns
description: Learn how to troubleshoot issues creating, assigning, and removing blueprints such as policy violations and blueprint parameter functions.
ms.date: 11/22/2019
ms.topic: troubleshooting
ms.openlocfilehash: 4e7ea1760e000a167c4329d6f12f3acc18d18f7c
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406605"
---
# <a name="troubleshoot-errors-using-azure-blueprints"></a>Solucionar problemas usando o Azure Blueprint

Você pode executar erros ao criar ou atribuir blueprints. Este artigo descreve os vários erros que podem ocorrer e como resolvê-los.

## <a name="finding-error-details"></a>Encontrar detalhes do erro

Muitos erros serão o resultado da atribuição de um blueprint para um escopo. Quando uma atribuição falha, o blueprint fornece detalhes sobre a implantação com falha. Essa informação indica o problema para que ele possa ser corrigido e a próxima implantação for bem-sucedida.

1. Selecione **Todos os serviços** no painel esquerdo. Pesquise e selecione **Blueprints**.

1. Select **Assigned blueprints** from the page on the left and use the search box to filter the blueprint assignments to find the failed assignment. Você também pode classificar a tabela de atribuições de acordo com a coluna **Estado de Provisionamento** para ver todas as atribuições com falha agrupadas.

1. Left-click on the blueprint with the _Failed_ status or right-click and select **View assignment details**.

1. Uma faixa vermelha que falhou a atribuição de aviso está na parte superior da página de atribuição de plano gráfico. Clique em qualquer lugar na faixa para obter mais detalhes.

É comum o erro a ser causado por um artefato e não o plano gráfico como um todo. Se um artefato cria um cofre de chaves e a Azure Policy impede a criação de Cofre de chaves, a atribuição inteira falhará.

## <a name="general-errors"></a>Erros gerais

### <a name="policy-violation"></a>Cenário: violação de política

#### <a name="issue"></a>Problema

A implantação de modelo falhou por causa da violação de política.

#### <a name="cause"></a>Causa

Uma política pode entrar em conflito com a implantação por vários motivos:

- O recurso que está sendo criado é restrito pela política (normalmente restrições SKU ou local)
- A implantação está definindo campos que são configurados pela política (comum com marcas)

#### <a name="resolution"></a>Resolução

Altere o plano gráfico para que ele não entre em conflito com as políticas nos detalhes do erro. Se isso não for possível, uma opção alternativa será alterar o escopo da atribuição de política para que o blueprint não esteja mais em conflito com a política.

### <a name="escape-function-parameter"></a>Scenario: Blueprint parameter is a function

#### <a name="issue"></a>Problema

Parâmetros de blueprint que são funções são processados antes de serem passados para artefatos.

#### <a name="cause"></a>Causa

Passar um parâmetro de blueprint que usa uma função, como `[resourceGroup().tags.myTag]`, para um artefato faz com que o resultado processado da função seja definido no artefato em vez da função dinâmica.

#### <a name="resolution"></a>Resolução

Para passar uma função como um parâmetro, faça o escape de toda a cadeia de caracteres com `[`, de modo que o parâmetro de blueprint se pareça com `[[resourceGroup().tags.myTag]`. O caractere de escape faz com que o Blueprints trate o valor como uma cadeia de caracteres ao processar o blueprint. O Blueprints, em seguida, coloca a função no artefato, permitindo que ela seja dinâmica conforme o esperado. For more information, see [Syntax and expressions in Azure Resource Manager templates](../../../azure-resource-manager/template-expressions.md).

## <a name="next-steps"></a>Próximos passos

Se você não encontrou seu problema ou não conseguiu resolver seu problema, visite um dos seguintes canais para obter mais suporte:

- Get answers from Azure experts through [Azure Forums](https://azure.microsoft.com/support/forums/).
- Conecte-se a [@AzureSupport](https://twitter.com/azuresupport) – a conta oficial do Microsoft Azure para melhorar a experiência do cliente conectando-se à comunidade do Azure para os recursos certos: respostas, suporte e especialistas.
- Se precisar de mais ajuda, você pode registrar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione **Obter Suporte**.