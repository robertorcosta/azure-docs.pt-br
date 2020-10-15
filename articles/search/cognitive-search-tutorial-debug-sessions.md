---
title: 'Tutorial: Usar Sessões de depuração para diagnosticar, corrigir e confirmar alterações eu seu conjunto de habilidades'
titleSuffix: Azure Cognitive Search
description: As Sessões de depuração (versão prévia) fornecem uma interface baseada em portal para avaliar e reparar problemas/erros em seu conjunto de habilidades
author: HeidiSteen
ms.author: heidist
manager: nitinme
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 09/25/2020
ms.openlocfilehash: 33c26af86bfcf2f748a0fa68ee4f3d0da1f132e1
ms.sourcegitcommit: 1b47921ae4298e7992c856b82cb8263470e9e6f9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92057545"
---
# <a name="tutorial-diagnose-repair-and-commit-changes-to-your-skillset"></a>Tutorial: Diagnosticar, reparar e confirmar alterações no conjunto de habilidades

Neste artigo, você usará o portal do Azure para acessar as Sessões de depuração para reparar problemas com o conjunto de habilidades fornecido. O conjunto de habilidades tem alguns erros que precisam ser corrigidos. Este tutorial orientará você em uma sessão de depuração para identificar e resolver problemas com entradas e saídas de habilidades.

> [!Important]
> As sessões de depuração são uma versão prévia do recurso fornecida sem um contrato de nível de serviço e não são recomendadas para cargas de trabalho de produção. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

> [!div class="checklist"]
> * Uma assinatura do Azure. Crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) ou use sua assinatura atual
> * Uma instância de serviço do Azure Cognitive Search
> * Uma conta de armazenamento do Azure
> * [Aplicativo Postman para a área de trabalho](https://www.getpostman.com/)

## <a name="create-services-and-load-data"></a>Criar serviços e carregar dados

Este tutorial usa o Azure Cognitive Search e os serviços de Armazenamento do Azure.

* [Baixe dados de exemplo](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/clinical-trials-pdf-19) compostos por 19 arquivos.

* [Criar uma conta de armazenamento do Azure](../storage/common/storage-account-create.md?tabs=azure-portal) ou [localizar uma conta](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/). 

   Escolha a mesma região do Azure Cognitive Search para evitar preços de largura de banda.
   
   Escolha o tipo de conta StorageV2 (uso geral V2).

* Abra as páginas dos serviços de armazenamento e crie um contêiner. A melhor prática é especificar o nível de acesso "particular". Dê ao contêiner o nome `clinicaltrialdataset`.

* No contêiner, clique em **Carregar** para carregar os arquivos de exemplo baixados e descompactados na primeira etapa.

* [Crie um serviço do Azure Cognitive Search](search-create-service-portal.md) ou [localize um serviço existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). É possível usar um serviço gratuito para este início rápido.

## <a name="get-a-key-and-url"></a>Obter uma chave e uma URL

As chamadas REST exigem a URL do serviço e uma chave de acesso em cada solicitação. Um serviço de pesquisa é criado com ambos, portanto, se você adicionou a Pesquisa Cognitiva do Azure à sua assinatura, siga estas etapas para obter as informações necessárias:

1. [Entre no portal do Azure](https://portal.azure.com/) e, na página **Visão Geral** do serviço de pesquisa, obtenha a URL. Um ponto de extremidade de exemplo pode parecer com `https://mydemo.search.windows.net`.

1. Em **Configurações** > **Chaves**, obtenha uma chave de administração para adquirir todos os direitos sobre o serviço. Há duas chaves de administração intercambiáveis, fornecidas para a continuidade dos negócios, caso seja necessário sobrepor uma. É possível usar a chave primária ou secundária em solicitações para adicionar, modificar e excluir objetos.

:::image type="content" source="media/search-get-started-postman/get-url-key.png" alt-text="Obter um ponto de extremidade HTTP e uma chave de acesso" border="false":::

Todas as solicitações requerem uma chave de api em cada pedido enviado ao serviço. Ter uma chave válida estabelece a relação de confiança, para cada solicitação, entre o aplicativo que envia a solicitação e o serviço que lida com ela.

## <a name="create-data-source-skillset-index-and-indexer"></a>Criar fonte de dados, conjunto de habilidades, índice e indexador

Nesta seção, o Postman e uma coleção fornecida são usados para criar a fonte de dados do serviço de pesquisa, o conjunto de habilidades, o índice e o indexador.

1. Se não tiver o Postman, você poderá [baixar o aplicativo da área de trabalho do Postman aqui](https://www.getpostman.com/).
1. [Baixar a coleção do Postman das Sessões de depuração](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Debug-sessions)
1. Inicie o Postman
1. Em **Arquivos** > **Novo**, selecione a coleção a ser importada.
1. Depois que a coleção for importada, expanda a lista de ações (...).
1. Clique em **Editar**.
1. Insira o nome de seu searchService (por exemplo, se o ponto de extremidade for `https://mydemo.search.windows.net`, o nome do serviço será "`mydemo`").
1. Insira a apiKey com a chave primária ou secundária do serviço de pesquisa.
1. Insira a storageConnectionString da página de chaves de sua conta de Armazenamento do Azure.
1. Insira o ContainerName para o contêiner criado na conta de armazenamento.

> :::image type="content" source="media/cognitive-search-debug/postman-enter-variables.png" alt-text="Obter um ponto de extremidade HTTP e uma chave de acesso":::

A coleção contém quatro chamadas REST diferentes usadas para concluir esta seção.

A primeira chamada cria a fonte de dados. `clinical-trials-ds`. A segunda chamada cria o conjunto de habilidades, `clinical-trials-ss`. A terceira chamada cria o índice, `clinical-trials`. A quarta e última chamada cria o indexador, `clinical-trials-idxr`. Depois que todas as chamadas na coleção tiverem sido concluídas, feche o Postman e volte para o portal do Azure.

> :::image type="content" source="media/cognitive-search-debug/postman-create-data-source.png" alt-text="Obter um ponto de extremidade HTTP e uma chave de acesso":::

## <a name="check-the-results"></a>Verificar os resultados

O conjunto de habilidades contém alguns erros comuns. Nesta seção, a execução de uma consulta vazia para retornar todos os documentos exibirá vários erros. Nas etapas posteriores, os problemas serão resolvidos usando uma sessão de depuração.

1. Vá até seu serviço de pesquisa no portal do Azure. 
1. Selecione a guia **Índices**. 
1. Selecione o índice `clinical-trials`
1. Clique em **Pesquisar** para executar uma consulta vazia. 

Após a conclusão da pesquisa, dois campos sem dados, "organizações" e "locais", são listados na janela. Siga as etapas para descobrir todos os problemas produzidos pelo conjunto de habilidades.

1. Volte para a página de visão geral do serviço de pesquisa.
1. Selecione a guia **Indexadores**. 
1. Clique em `clinical-trials-idxr` e selecione a notificação de avisos. 

Há muitos problemas com os mapeamentos de campo de saída da projeção e, na página três, há avisos porque uma ou mais entradas de habilidade são inválidas.

Volte para a tela de visão geral do serviço de pesquisa.

## <a name="start-your-debug-session"></a>Inicie a sessão de depuração

> :::image type="content" source="media/cognitive-search-debug/new-debug-session-screen-required.png" alt-text="Obter um ponto de extremidade HTTP e uma chave de acesso":::

1. Clique na guia Sessões de depuração (versão prévia).
1. Selecione +NewDebugSession
1. Dê um nome à sessão. 
1. Conecte a sessão à sua conta de armazenamento. 
1. Forneça o nome do indexador. O indexador tem referências à fonte de dados, ao conjunto de habilidades e ao índice.
1. Aceite a opção de documento padrão para o primeiro documento na coleção. 
1. **Salve** a sessão. Salvar a sessão iniciará o pipeline de enriquecimento de IA, conforme definido pelo conjunto de habilidades.

> [!Important]
> Uma sessão de depuração funciona apenas com um só documento. Um documento específico no conjunto de dados pode ser > selecionado ou a sessão usará como padrão o primeiro documento.

> :::image type="content" source="media/cognitive-search-debug/debug-execution-complete1.png" alt-text="Obter um ponto de extremidade HTTP e uma chave de acesso":::

Quando a sessão de depuração termina a execução, ela usa como padrão a guia Aprimoramentos de IA, destacando o Grafo de Habilidades.

+ O Grafo de Habilidades fornece uma hierarquia visual do conjunto de habilidades e sua ordem de execução de cima para baixo. As habilidades que estão lado a lado no grafo são executadas em paralelo. A codificação por cores das habilidades no grafo indica os tipos de habilidades executadas no conjunto de habilidades. No exemplo, as habilidades verdes são texto e a habilidade vermelha é a visão. Clicar em uma habilidade individual no grafo exibirá os detalhes dessa instância da habilidade no painel direito da janela da sessão. As configurações de habilidades, um editor de JSON, detalhes da execução e erros/avisos ficam disponíveis para revisão e edição.
+ A Estrutura de Dados Enriquecidos detalha os nós na árvore de enriquecimento gerada pelas habilidades do conteúdo do documento de origem.

A guia Erros/Avisos fornecerá uma lista muito menor do que aquela exibida anteriormente, pois essa lista só detalha os erros de um documento. Assim como a lista exibida pelo indexador, você pode clicar em uma mensagem de aviso e ver os detalhes desse aviso.

## <a name="fix-missing-skill-input-value"></a>Corrigir o valor de entrada da habilidade ausente

Na guia Erros/Avisos, há um erro para uma operação rotulada `Enrichment.NerSkillV2.#1`. O detalhe desse erro explica que há um problema com o valor de entrada da habilidade '/document/languageCode '. 

1. Retorne à guia Aprimoramentos de IA.
1. Clique no **Grafo de Habilidades**.
1. Clique na habilidade rotulada #1 para exibir seus detalhes no painel direito.
1. Localize a entrada de "languageCode".
1. Selecione o símbolo **</>** no início da linha e abra o Avaliador de Expressão.
1. Clique no botão **Avaliar** para confirmar que essa expressão está resultando em um erro. Ele confirmará que a propriedade "languageCode" não é uma entrada válida.

> :::image type="content" source="media/cognitive-search-debug/expression-evaluator-language.png" alt-text="Obter um ponto de extremidade HTTP e uma chave de acesso":::

Há duas maneiras de pesquisar esse erro na sessão. A primeira é examinar de onde a entrada é proveniente – qual habilidade na hierarquia deve produzir esse resultado? A guia Execuções no painel detalhes da habilidade deve exibir a origem da entrada. Se não houver uma origem, isso indicará um erro de mapeamento de campo.

1. Clique na guia **Execuções**.
1. Examine as ENTRADAS e localize "languageCode". Não há nenhuma origem para essa entrada listada. 
1. Alterne para o painel esquerdo para exibir a Estrutura de Dados Enriquecidos. Não há nenhum caminho mapeado correspondente a "languageCode".

> :::image type="content" source="media/cognitive-search-debug/enriched-data-structure-language.png" alt-text="Obter um ponto de extremidade HTTP e uma chave de acesso" desapareceu. No entanto, ainda há dois avisos de que o serviço não pôde mapear campos de saída de organizações e locais para o índice de pesquisa. Há valores ausentes: '/document/merged_content/organizations' e '/document/merged_content/locations'.

## <a name="fix-missing-skill-output-values"></a>Corrigir valores de saída da habilidade ausente

> :::image type="content" source="media/cognitive-search-debug/warnings-missing-value-locations-organizations.png" alt-text="Obter um ponto de extremidade HTTP e uma chave de acesso":::

Há valores de saída ausentes de uma habilidade. Para identificar a habilidade com erro, vá para a Estrutura de Dados Enriquecidos, localize o nome do valor e examine sua Fonte de Origem. No caso dos valores de organizações e locais ausentes, eles são saídas do da habilidade #1. Abrir o Avaliador de Expressão </> para cada caminho exibirá as expressões listadas como '/document/content/organizations' e '/document/content/locations', respectivamente.

> :::image type="content" source="media/cognitive-search-debug/expression-eval-missing-value-locations-organizations.png" alt-text="Obter um ponto de extremidade HTTP e uma chave de acesso":::

A saída dessas entidades está vazia e não deveria estar vazia. Quais são as entradas que produzem esse resultado?

1. Vá para **Grafo de Habilidades** e selecione a habilidade #1.
1. Selecione a guia **Execuções** no painel de detalhes da habilidade à direita.
1. Abra o Avaliador de Expressão **</>** para a ENTRADA "text".

> :::image type="content" source="media/cognitive-search-debug/input-skill-missing-value-locations-organizations.png" alt-text="Obter um ponto de extremidade HTTP e uma chave de acesso":::

O resultado exibido para essa entrada não se parece com uma entrada de texto. Ele parece uma imagem envolvida por novas linhas. A falta de texto significa que nenhuma entidade pode ser identificada. Examinar a hierarquia do conjunto de habilidades mostra que o conteúdo é processado primeiro pela habilidade #6 (OCR) e, em seguida, passado para a habilidade #5 (Merge). 

1. Selecione a habilidade #5 (Merge) no **Grafo de Habilidades**.
1. Selecione a guia **Execuções** no painel de detalhes da habilidade à direita e abra o Avaliador de Expressão **</>** para as SAÍDAS "mergedText".

> :::image type="content" source="media/cognitive-search-debug/merge-output-detail-missing-value-locations-organizations.png" alt-text="Obter um ponto de extremidade HTTP e uma chave de acesso" e feche a janela do Avaliador de Expressão.
1. Selecione a habilidade #1 no **Grafo de Habilidades**.
1. Selecione a guia **Configurações de Habilidade** no painel de detalhes da habilidade à direita.
1. Abra o Avaliador de Expressão **</>** para a entrada "text".
1. Cole a nova expressão na caixa. Clique em **Avaliar**.
1. A entrada correta com o texto adicionado deve ser exibida. Clique em **Aplicar** para atualizar as Configurações da Habilidade.
1. Clique em **Salvar** no painel direito de detalhes da habilidade.
1. Clique em **Executar** no menu da janela das sessões. Isso iniciará outra execução do conjunto de habilidades usando o documento.

Quando a execução do indexador for concluída, os erros ainda estarão lá. Volte para a habilidade #1 e investigue. A entrada da habilidade foi corrigida de 'content' para 'merger_content'. Quais são as saídas dessas entidades na habilidade?

1. Selecione a guia **Enriquecimentos de IA**.
1. Selecione o **Grafo de Habilidades** e clique na habilidade #1.
1. Navegue para as **Configurações de Habilidade** para encontrar "outputs".
1. Abra o Avaliador de Expressão **</>** para a entidade "organizations".

> :::image type="content" source="media/cognitive-search-debug/skill-output-detail-missing-value-locations-organizations.png" alt-text="Obter um ponto de extremidade HTTP e uma chave de acesso" e edite-a para ler '/document/merged_content'.
1. Clique em **Salvar** no painel direito de detalhes da habilidade.
1. Clique em **Executar** no menu da janela das sessões. Isso iniciará outra execução do conjunto de habilidades usando o documento.

> :::image type="content" source="media/cognitive-search-debug/skill-setting-context-correction-missing-value-locations-organizations.png" alt-text="Obter um ponto de extremidade HTTP e uma chave de acesso":::

Todos os erros foram resolvidos.

## <a name="commit-changes-to-the-skillset"></a>Confirmar alterações no conjunto de habilidades

Quando a sessão de depuração foi iniciada, o serviço de pesquisa criou uma cópia do conjunto de habilidades. Isso foi feito para que as alterações feitas não afetassem o sistema de produção. Agora que você concluiu a depuração de seu conjunto de habilidades, as correções podem ser confirmadas (substituindo o conjunto de habilidades original) no sistema de produção. Se você quiser continuar fazendo alterações no conjunto de habilidades sem afetar o sistema de produção, a sessão de depuração poderá ser salva e reaberta posteriormente.

1. Clique em **Confirmar alterações** no menu principal das Sessões de depuração.
1. Clique em **OK** para confirmar que você deseja atualizar o conjunto de habilidades.
1. Feche a Sessão de depuração e selecione a guia **Indexadores**.
1. Abra seu 'clinical-trials-idxr'.
1. Clique em **redefinir**.
1. Clique em **Executar**. Clique em **OK** para confirmar.

Quando o indexador terminar de ser executado, deverá haver uma marca de seleção verde e a palavra Êxito ao lado do carimbo de data/hora da última execução na guia de histórico de execuções. Para garantir que as alterações tenham sido aplicadas:

1. Saia **Indexador** e selecione a guia **Índice**.
1. Abra o índice 'clinical-trials' e, na guia do Gerenciador de Pesquisa, clique em **Pesquisar**.
1. A janela de resultado deve mostrar que as entidades de organizações e locais agora estão preenchidas com os valores esperados.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando você está trabalhando em sua própria assinatura, é uma boa ideia identificar, no final de um projeto, se você ainda precisa dos recursos criados. Recursos deixados em execução podem custar dinheiro. Você pode excluir os recursos individualmente ou excluir o grupo de recursos para excluir todo o conjunto de recursos.

Você pode localizar e gerenciar recursos no portal usando o link **Todos os recursos** ou **Grupos de recursos** no painel de navegação à esquerda.

Se você estiver usando um serviço gratuito, estará limitado a três índices, indexadores e fontes de dados. Você pode excluir itens individuais no portal para permanecer abaixo do limite. 

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba mais sobre os conjuntos de habilidades](./cognitive-search-working-with-skillsets.md)
> [Saiba mais sobre o enriquecimento e o cache incrementais](./cognitive-search-incremental-indexing-conceptual.md)