---
title: Capacidades de caça no Azure Sentinel| Microsoft Docs
description: Este artigo descreve como usar as capacidades de caça do Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 6aa9dd27-6506-49c5-8e97-cc1aebecee87
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/10/2019
ms.author: yelevin
ms.openlocfilehash: 52af688917aa531d125f83844df29a988ed7cb7e
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686626"
---
# <a name="hunt-for-threats-with-azure-sentinel"></a>Busca por ameaças com o Azure Sentinel

Se você é um investigador que quer ser proativo em procurar ameaças à segurança, o Azure Sentinel poderosas ferramentas de busca e consulta de caça para caçar ameaças de segurança através das fontes de dados da sua organização. Mas seus sistemas e dispositivos de segurança geram montanhas de dados que podem ser difíceis de analisar e filtrar em eventos significativos. Para ajudar os analistas de segurança a procurar proativamente novas anomalias que não foram detectadas por seus aplicativos de segurança, as consultas de caça incorporadas do Azure Sentinel orientam você a fazer as perguntas certas para encontrar problemas nos dados que você já tem em sua rede. 

Por exemplo, uma consulta incorporada fornece dados sobre os processos mais incomuns em execução em sua infra-estrutura - você não gostaria de um alerta sobre cada vez que eles são executados, eles podem ser totalmente inocentes, mas você pode querer dar uma olhada na consulta de vez em quando para ver se há algo incomum. 



Com a caça do Azure Sentinel, você pode aproveitar as seguintes capacidades:

- Consultas incorporadas: Para que você comece, uma página inicial fornece exemplos de consulta pré-carregados projetados para que você comece e familiarize-o com as tabelas e o idioma de consulta. Essas consultas de caça incorporadas são desenvolvidas por pesquisadores de segurança da Microsoft de forma contínua, adicionando novas consultas e afinando as consultas existentes para fornecer-lhe um ponto de entrada para procurar novas detecções e descobrir por onde começar a caçar para o início de novos ataques. 

- Linguagem de consulta poderosa com o IntelliSense: Construído em cima de uma linguagem de consulta que lhe dá a flexibilidade necessária para levar a caça ao próximo nível.

- Crie seus próprios marcadores: Durante o processo de caça, você pode se deparar com partidas ou descobertas, dashboards ou atividades que pareçam incomuns ou suspeitas. Para marcar esses itens para que você possa voltar para eles no futuro, use a funcionalidade do marcador. Os marcadores permitem que você guarde itens para depois, para serem usados para criar um incidente para investigação. Para obter mais informações sobre marcadores, consulte [Usar marcadores na caça](hunting.md).
- Use cadernos para automatizar a investigação: Notebooks são como cartilhas passo a passo que você pode construir para passar pelos passos de uma investigação e caçar.  Os cadernos encapsulam todos os passos de caça em um livro de jogadas reutilizável que pode ser compartilhado com outros em sua organização. 
- Consulta os dados armazenados: Os dados estão acessíveis em tabelas para você consultar. Por exemplo, você pode consultar a criação de processos, eventos DNS e muitos outros tipos de eventos.

- Links para a comunidade: Aproveite o poder da comunidade maior para encontrar consultas adicionais e fontes de dados.
 
## <a name="get-started-hunting"></a>Comece a caçar

1. No portal Azure Sentinel, clique em **Hunting**.
  ![Azure Sentinel começa a caçar](media/tutorial-hunting/hunting-start.png)

2. Quando você abre a página **Caça,** todas as consultas de caça são exibidas em uma única tabela. A tabela lista todas as consultas escritas pela equipe de analistas de segurança da Microsoft, bem como qualquer consulta adicional que você criou ou modificou. Cada consulta fornece uma descrição do que ele caça, e que tipo de dados ele executa. Esses modelos são agrupados por suas várias táticas - os ícones à direita categorizam o tipo de ameaça, como acesso inicial, persistência e exfiltração. Você pode filtrar esses modelos de consulta de caça usando qualquer um dos campos. Você pode salvar qualquer consulta aos seus favoritos. Salvando uma consulta aos seus favoritos, a consulta é executada automaticamente cada vez que a página **Hunting** é acessada. Você pode criar sua própria consulta de caça ou clonar e personalizar um modelo de consulta de caça existente. 
 
2. Clique em **Executar consulta** na página de detalhes da consulta de caça para executar qualquer consulta sem sair da página de caça.  O número de correspondências é exibido dentro da tabela. Revise a lista de consultas de caça e seus fósforos. Confira em que estágio na cadeia de mortes a partida está associada.

3. Realize uma revisão rápida da consulta subjacente no painel de detalhes da consulta ou clique **em Exibir o resultado da consulta** para abrir a consulta no Log Analytics. Na parte inferior, revise as correspondências para a consulta.

4.    Clique na linha e selecione **Adicionar marcador** para adicionar as linhas a serem investigadas - você pode fazer isso para qualquer coisa que pareça suspeita. 

5. Em seguida, volte para a página principal **de Caça** e clique na guia **Marcadores** para ver todas as atividades suspeitas. 

6. Selecione um marcador e clique em **Investigar** para abrir a experiência de investigação. Você pode filtrar os marcadores. Por exemplo, se você estiver investigando uma campanha, você pode criar uma tag para a campanha e, em seguida, filtrar todos os marcadores com base na campanha.

1. Depois que você descobriu qual consulta de caça fornece insights de alto valor sobre possíveis ataques, você também pode criar regras de detecção personalizadas com base em sua consulta e superfície esses insights como alertas para seus respondentes de incidentes de segurança.

 

## <a name="query-language"></a>Idioma de consulta 

Caçar no Azure Sentinel é baseado na linguagem de consulta kusto. Para obter mais informações sobre o idioma de consulta e operadores suportados, consulte [Consulta De Referência de Idioma](/azure/azure-monitor/log-query/get-started-queries).

## <a name="public-hunting-query-github-repository"></a>Repositório gitHub de consulta de caça pública

Confira o [repositório de consulta de caça](https://github.com/Azure/Orion). Contribua e use perguntas de exemplo compartilhadas por nossos clientes.

 

## <a name="sample-query"></a>Exemplo de consulta

Uma consulta comum começa com um nome de tabela, seguido por uma série de operadores separados por \|.

No exemplo acima, comece com o nome da tabela SecurityEvent e adicione elementos encanados conforme necessário.

1. Defina um filtro de tempo para revisar apenas os registros dos sete dias anteriores.

2. Adicione um filtro na consulta para mostrar apenas o ID de evento 4688.

3. Adicione um filtro na consulta na Linha de Comando para conter apenas instâncias de cscript.exe.

4. Projete apenas as colunas que você está interessado em explorar e limite os resultados para 1000 e clique **em Executar consulta**.
5. Clique no triângulo verde e execute a consulta. Você pode testar a consulta e executá-la para procurar comportamento anômalo.

## <a name="useful-operators"></a>Operadores úteis

A linguagem de consulta é poderosa e tem muitos operadores disponíveis, alguns operadores úteis estão listados aqui:

**onde** - Filtrar uma tabela para o subconjunto de linhas que satisfaçam um predicado.

**resumir** - Produzir uma tabela que agrega o conteúdo da tabela de entrada.

**juntar** - Mesclar as linhas de duas tabelas para formar uma nova tabela, combinando valores das colunas especificadas de cada tabela.

**contagem** - Retornar o número de registros no conjunto de registros de entrada.

**topo** - Retornar os primeiros registros N classificados pelas colunas especificadas.

**limite** - Retornar ao número especificado de linhas.

**projeto** - Selecione as colunas para incluir, renomear ou soltar e inserir novas colunas computadas.

**estender** - Criar colunas calculadas e aproverjá-las ao conjunto de resultados.

**makeset** - Retornar uma matriz dinâmica (JSON) do conjunto de valores distintos que a Expr leva no grupo

**encontrar** - Encontrar linhas que correspondam a um predicado em um conjunto de tabelas.

## <a name="save-a-query"></a>Salvar uma consulta

Você pode criar ou modificar uma consulta e salvá-la como sua própria consulta ou compartilhá-la com usuários que estão no mesmo inquilino.

   ![Salvar consulta](./media/tutorial-hunting/save-query.png)

Crie uma nova consulta de caça:

1. Clique **em Nova consulta** e selecione **Salvar**.
2. Preencha todos os campos em branco e selecione **Salvar**.

   ![Nova Consulta](./media/tutorial-hunting/new-query.png)

Clonar e modificar uma consulta de caça existente:

1. Selecione a consulta de caça na tabela que deseja modificar.
2. Selecione a elipse (...) na linha da consulta que deseja modificar e selecione **Consulta clone**.

   ![consulta de clone](./media/tutorial-hunting/clone-query.png)
 

3. Modifique a consulta e selecione **Criar**.

   ![consulta personalizada](./media/tutorial-hunting/custom-query.png)

## <a name="next-steps"></a>Próximas etapas
Neste artigo, você aprendeu a executar uma investigação de caça com o Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:


- [Use notebooks para executar campanhas de caça automatizadas](notebooks.md)
- [Use marcadores para salvar informações interessantes durante a caça](bookmarks.md)
