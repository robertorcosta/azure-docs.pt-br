---
title: Introdução ao suporte interno do Jupyter Notebooks no Azure Cosmos DB (versão prévia)
description: Saiba como usar o suporte interno para Jupyter Notebooks no Azure Cosmos DB para executar consultas interativamente.
ms.service: cosmos-db
ms.topic: overview
ms.date: 09/22/2019
author: markjbrown
ms.author: mjbrown
ms.openlocfilehash: 5b320485001d6cbc457d39ef193ed8c57f7161df
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2020
ms.locfileid: "76760276"
---
# <a name="built-in-jupyter-notebooks-support-in-azure-cosmos-db-preview"></a>Suporte interno para o Jupyter Notebooks no Azure Cosmos DB (versão prévia)

O Jupyter Notebook é um aplicativo Web open-source que permite que criar e compartilhar documentos que contêm código ao vivo, equações, visualizações e texto narrativo. O Azure Cosmos DB dá suporte a Blocos de notas do Jupyter internos para todas as APIs, como Cassandra, MongoDB, SQL, Gremlin e Table. O suporte interno para notebook para todas as APIs e modelos de dados do Azure Cosmos DB permite que você execute consultas de forma interativa. Os Jupyter Notebooks são executados nas contas do Azure Cosmos e permitem que os desenvolvedores realizem exploração de dados, limpeza de dados, transformações de dados, simulações numéricas, modelagem estatística, visualização de dados e aprendizado de máquina.

![Visualizações de Jupyter Notebooks no Azure Cosmos DB](./media/cosmosdb-jupyter-notebooks/cosmos-notebooks-overview.png)

O Jupyter Notebooks dá suporte a funções magic que ampliam as funcionalidades do kernel dando suporte a comandos adicionais. Cosmos magic é um comando que estende as funcionalidades do kernel do Python em um Jupyter Notebook, de modo que você possa executar consultas da API SQL do Azure Cosmos além do Apache Spark. Você pode combinar facilmente consultas à API SQL e do Python para consultar e visualizar dados usando bibliotecas de visualização avançadas integradas com comandos de renderização.
O portal do Azure integra nativamente a experiência do Jupyter Notebook às contas do Azure Cosmos, conforme mostrado na imagem a seguir:

![Suporte aos Jupyter notebooks no Azure Cosmos DB](./media/cosmosdb-jupyter-notebooks/jupyter-notebooks-portal.png)

## <a name="benefits-of-jupyter-notebooks"></a>Benefício do Jupyter Notebooks

O Jupyter Notebooks foi desenvolvido originalmente para aplicativos de ciência de dados escritos em Python, R. No entanto, eles podem ser usados de várias maneiras para diferentes tipos de projetos, como:

* ***Visualizações de dados:** o Jupyter Notebooks permite visualizar dados na forma de um notebook compartilhado que renderiza alguns conjuntos de dados como um gráfico. O Jupyter Notebook permite que você crie visualizações, compartilhe-as e permita alterações interativas no código compartilhado e no conjunto de dados.

* **Compartilhamento de código:** serviços como o GitHub fornecem maneiras de compartilhar código, mas são, na maior parte, não interativos. Com um Jupyter Notebook, você pode exibir o código, executá-lo e exibir os resultados diretamente no portal do Azure.

* **Interações dinâmicas com o código:** o código do Jupyter Notebook é dinâmico; ele pode ser editado e executado novamente de forma incremental em tempo real. Os Notebooks também podem inserir controles de usuário (por exemplo, controles deslizantes ou campos de entrada de texto) que podem ser usados como fontes de entrada para código, demonstrações ou POCs (provas de conceito).

* **Documentação de exemplos de código e resultados da exploração de dados:** se tiver um trecho de código e desejar explicar linha por linha como ele funciona em Azure Cosmos DB, com saída em tempo real, você poderá inseri-lo em um Jupyter Notebook. O código permanecerá totalmente funcional. Você pode adicionar interatividade com a documentação ao mesmo tempo.

* **Comandos Cosmos magic:** no Jupyter Notebooks, você pode usar comandos magic personalizados para o Azure Cosmos DB para facilitar a computação interativa. Por exemplo, o comando magic %%sql permite consultar um contêiner do Cosmos usando a API do SQL diretamente em um notebook.

* **Ambiente completo em um local:** o Jupyter Notebooks combina código, rich text, imagens, vídeos, animações, equações matemáticas, plotagens, mapas, figuras interativas, widgets e interfaces gráficas do usuário em um único documento.

## <a name="components-of-a-jupyter-notebook"></a>Componentes de um Jupyter Notebook

Os Jupyter Notebooks podem incluir vários tipos de componentes, cada um organizado em blocos discretos:

* **Texto e HTML:** texto sem formatação ou texto anotado na sintaxe de Markdown para gerar HTML, pode ser inserido no documento a qualquer momento. O estilo de CSS também pode ser incluído embutido ou adicionado ao modelo usado para gerar o notebook.

* **Código e saída:** Jupyter Notebooks têm suporte para código Python. Os resultados do código executado aparecem imediatamente após os blocos de código. Os blocos de código podem ser executados várias vezes em qualquer ordem desejada.

* **Visualizações:** grafos e gráficos podem ser gerados do código, usando módulos como Matplotlib, Plotly ou Bokeh. De forma semelhante à saída, essas visualizações aparecem embutidas ao lado do código que as gera.

* **Multimídia:** como o Jupyter Notebook é criado com base na tecnologia da Web, ele pode exibir todos os tipos de multimídia com suporte em uma página da Web. Você pode incluí-los em um notebook como elementos HTML ou pode gerá-los de forma programática usando o módulo `IPython.display`.

* **Dados:** os dados dos contêineres do Azure Cosmos e dos resultados das consultas podem ser importados em um Jupyter Notebook de forma programática. Por exemplo, incluindo o código no notebook para consultar os dados usando qualquer uma das APIs do Cosmos DB ou o Apache Spark interno nativo.

## <a name="next-steps"></a>Próximas etapas

Para começar a usar Jupyter Notebooks internos no Azure Cosmos DB, confira os seguintes artigos:

* [Habilitar notebooks em uma conta do Azure Cosmos](enable-notebooks.md)
* [Usar recursos e comandos de notebook](use-notebook-features-and-commands.md)



