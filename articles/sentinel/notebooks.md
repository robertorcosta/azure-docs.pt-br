---
title: Use notebooks com o Azure Sentinel para caça à segurança
description: Este artigo descreve como usar notebooks com os recursos de caça do Azure Sentinel.
services: sentinel
author: yelevin
ms.author: yelevin
ms.assetid: 1721d0da-c91e-4c96-82de-5c7458df566b
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.custom: mvc
ms.date: 11/25/2019
ms.openlocfilehash: 84b72a71ed2de910bce44b0c3f3309782f096680
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77581830"
---
# <a name="use-jupyter-notebooks-to-hunt-for-security-threats"></a>Use cadernos Jupyter para caçar ameaças à segurança

A fundação do Azure Sentinel é o data store; combina consultas de alto desempenho, esquemadinâmico e escalas a volumes maciços de dados. O portal Azure e todas as ferramentas do Azure Sentinel usam uma API comum para acessar esse armazenamento de dados. A mesma API também está disponível para ferramentas externas, como notebooks [Jupyter](https://jupyter.org/) e Python. Embora muitas tarefas comuns possam ser realizadas no portal, jupyter amplia o escopo do que você pode fazer com esses dados. Combina programação completa com uma enorme coleção de bibliotecas para aprendizado de máquina, visualização e análise de dados. Esses atributos fazem de Jupyter uma ferramenta convincente para investigação de segurança e caça.

![exemplo notebook](./media/notebooks/sentinel-notebooks-map.png)

Integramos a experiência Jupyter no portal Azure, facilitando a criação e execução de notebooks para analisar seus dados. A biblioteca *Kqlmagic* fornece a cola que permite que você pegue consultas do Azure Sentinel e as execute diretamente dentro de um notebook. As consultas usam o [Kusto Query Language](https://kusto.azurewebsites.net/docs/query/index.html). Vários notebooks, desenvolvidos por alguns dos analistas de segurança da Microsoft, são embalados com o Azure Sentinel. Alguns desses notebooks são construídos para um cenário específico e podem ser usados como está. Outros são destinados como amostras para ilustrar técnicas e recursos que você pode copiar ou adaptar para uso em seus próprios cadernos. Outros notebooks também podem ser importados da comunidade Azure Sentinel GitHub.

A experiência jupyter integrada usa [notebooks Azure](https://notebooks.azure.com/) para armazenar, compartilhar e executar notebooks. Você também pode executar esses notebooks localmente se você tiver um ambiente Python e Jupyter em seu computador, ou em outros ambientes do JupterHub, como o Azure Databricks.

Os notebooks têm dois componentes:

- A interface baseada no navegador onde você insere e executa consultas e códigos, e onde os resultados da execução são exibidos.
- Um *kernel* que é responsável por analisar e executar o próprio código. 

Nos Notebooks Azure, por padrão, este kernel é executado no Azure *Free Cloud Compute and Storage*. Se seus notebooks incluem modelos ou visualizações complexas de aprendizado de máquina, considere usar recursos computacionais mais poderosos e dedicados, como [o Data Science Virtual Machines](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/) (DSVM). Os cadernos em sua conta são mantidos em sigilo, a menos que você escolha compartilhá-los.

Os notebooks Azure Sentinel usam muitas bibliotecas python populares, como pandas, matplotlib, bokeh, entre outros. Existem muitos outros pacotes Python para você escolher, cobrindo áreas como:

- Visualizações e gráficos
- Processamento e análise de dados
- Estatística e computação numérica
- Aprendizado de máquina e aprendizado profundo

Também lançamos algumas ferramentas de segurança jupyter de código aberto em um pacote chamado [msticpy](https://github.com/Microsoft/msticpy/). Este pacote é usado em muitos dos notebooks incluídos. As ferramentas Msticpy são projetadas especificamente para ajudar na criação de notebooks para caça e investigação e estamos trabalhando ativamente em novos recursos e melhorias.

Os cadernos iniciais incluem:

- **Investigação guiada - Alertas de processo**: Permite que você trieda rapidamente os alertas, analisando a atividade no host ou hosts afetados.
- **Caça guiada - Explorador de host do Windows**: Permite explorar atividades de conta, execuções de processos, atividade de rede e outros eventos em um host.
- **Caça guiada - Office365-Exploring**: Hunt for suspicious Office 365 activity in multiple Office 365 data sets.

O [repositório Azure Sentinel Community GitHub](https://github.com/Azure/Azure-Sentinel) é o local para quaisquer futuros notebooks Azure Sentinel construídos pela Microsoft ou contribuídos da comunidade.

Para usar os notebooks, você deve ter uma conta do Azure Notebooks. Para obter mais informações, consulte [Quickstart: Faça login e defina um ID](https://docs.microsoft.com/azure/notebooks/quickstart-sign-in-azure-notebooks) do usuário na documentação do Azure Notebooks. Para criar essa conta, você pode usar a opção **Assinar para Notebooks Azure** na barra de comando no **Azure Sentinel - Notebooks**:

> [!div class="mx-imgBorder"]
>![Inscreva-se na opção Notebooks Azure](./media/notebooks/sentinel-azure-sign-up-azure-notebooks.png)

Você pode executar um notebook diretamente do Azure Sentinel, ou clonar todos os notebooks do Azure Sentinel para um novo projeto de Notebooks Azure.

## <a name="run-a-notebook-from-azure-sentinel"></a>Execute um notebook do Azure Sentinel
 
1. Do portal Azure, navegue até**notebooks**de**gerenciamento de** > ameaças **do Azure Sentinel,** > onde você pode ver notebooks que o Azure Sentinel fornece. 

2. Selecione notebooks individuais para ler suas descrições, tipos de dados necessários e fontes de dados. Por exemplo: 
    
    > [!div class="mx-imgBorder"]
    > ![notebook de lançamento](./media/notebooks/sentinel-azure-notebooks-launch.png)

3. Selecione o notebook que deseja usar e selecione **Launch Notebook (Preview)** para clonar e configurar o notebook em um novo projeto de Notebooks Azure que se conecta ao seu espaço de trabalho do Azure Sentinel. Quando o processo estiver concluído, o notebook será aberto dentro dos Notebooks Azure para você executar.

## <a name="clone-azure-sentinel-notebooks-to-a-new-azure-notebooks-project"></a>Clone Notebooks Azure Sentinel para um novo projeto azure Notebooks

Este procedimento cria um projeto de Notebooks Azure para você, que contém os notebooks Azure Sentinel. Em seguida, você pode executar os notebooks como está, ou fazer alterações neles e, em seguida, executá-los.

1. No portal Azure, navegue até**notebooks** de**gerenciamento de** > ameaças do **Azure Sentinel** > e selecione **Notebooks clones** na barra de comando:
  
    > [!div class="mx-imgBorder"]
    >![Opção Clone Notebooks](./media/notebooks/sentinel-azure-clone-notebooks.png)

2. Quando a seguinte caixa de diálogo for exibida, **selecione Importar** para clonar o repo do GitHub no projeto Notebooks Azure. Se você não tiver uma conta existente do Azure Notebooks, você será solicitado a criar um e fazer login.

   ![Importar notebook](./media/notebooks/sentinel-notebooks-clone.png)

3. Na caixa de diálogo **Upload GitHub Repository,** não selecione **Clone recursivamente** porque essa opção se refere aos repos os do GitHub vinculados. Para o nome do projeto, use o nome padrão ou digite um novo. Em seguida, clique **em Importar** para começar a clonar o conteúdo do GitHub, o que pode levar alguns minutos para ser concluído.

   ![Importar notebook](./media/notebooks/sentinel-create-project.png)

4. Abra o projeto que acabou de criar e abra a pasta **Notebooks** para ver os cadernos. Por exemplo: 

   ![Repo de importação](./media/notebooks/sentinel-open-notebook1.png)

Em seguida, você pode executar os notebooks do Azure Notebooks. Para retornar a esses notebooks do Azure Sentinel, **selecione Ir para seus Notebooks** na barra de comando no **Azure Sentinel - Notebooks**:

> [!div class="mx-imgBorder"]
>![Vá para a opção Notebooks](./media/notebooks/sentinel-azure-to-go-notebooks.png)


## <a name="using-notebooks-to-hunt"></a>Usando cadernos para caçar

Cada caderno orienta você através das etapas para realizar uma caçada ou investigação. Bibliotecas e outras dependências necessárias pelo notebook podem ser instaladas a partir do próprio notebook ou através de um procedimento de configuração simples. A configuração que vincula seu projeto de notebook à assinatura do Azure Sentinel é automaticamente provisionada nas etapas anteriores.

1. Se você ainda não estiver nos Notebooks Azure, você pode usar a opção **Ir para seus Notebooks** na barra de comando no **Azure Sentinel - Notebooks**:
    
    > [!div class="mx-imgBorder"]
    >![Vá para a opção Notebooks](./media/notebooks/sentinel-azure-to-go-notebooks.png)
    
    Nos Notebooks Azure, selecione **Meus Projetos,** em seguida, o projeto que contém os notebooks Azure Sentinel e, finalmente, a pasta **Notebooks.**
    
2. Antes de abrir um notebook, saiba que, por padrão, o Free Compute é selecionado para executar os notebooks:
    
   ![selecionar notebook](./media/notebooks/sentinel-open-notebook2.png)
    
    Se você configurou uma Máquinavirtual de Data Science (DSVM) para usar conforme explicado na introdução, selecione o DSVM e autenticar antes de abrir o primeiro notebook. 

3. Selecione um caderno para abri-lo.
    
    A primeira vez que você abrir um notebook, você pode ser solicitado a selecionar uma versão do kernel. Se você não for solicitado, você pode selecionar a versão do kernel no**kernel** **Do kernel** >  e selecionar uma versão que seja pelo menos 3.6. A versão do kernel selecionada é exibida no canto superior direito da janela do notebook:
    
   ![selecionar notebook](./media/notebooks/sentinel-select-kernel.png)

4. Antes de fazer qualquer alteração no notebook que você baixou, é uma boa idéia fazer uma cópia do notebook original e trabalhar na cópia. Para fazer isso, **selecione 'Fazer** > **um arquivo'** Trabalhar em cópias permite que você atualize com segurança para versões futuras de notebooks sem substituir nenhum de seus dados.
    
    Agora você está pronto para executar ou editar o notebook selecionado.

Recomendações:

- Para obter uma rápida introdução aos dados de consulta no Azure Sentinel, procure o notebook [GetStarted](https://github.com/Azure/Azure-Sentinel/blob/master/Notebooks/Get%20Started.ipynb) na pasta **principal notebooks.** 

- Você encontrará cadernos de exemplo adicionais na subpasta **Sample-Notebooks.** Esses notebooks de amostra foram salvos com dados, de modo que é mais fácil ver a saída pretendida. Recomendamos a visualização desses notebooks no [nbviewer](https://nbviewer.jupyter.org/). 

- A pasta **HowTos** contém cadernos descrevendo, por exemplo: Definindo a versão padrão do Python, configurando um DSVM, criando marcadores do Azure Sentinel a partir de um notebook e outros assuntos.

Os cadernos fornecidos são destinados tanto como ferramentas úteis quanto como ilustrações e amostras de código que você pode usar no desenvolvimento de seus próprios notebooks.

Recebemos feedback, sejam sugestões, solicitações de recursos, notebooks contribuídos, relatórios de bugs ou melhorias e adições aos notebooks existentes. Vá ao [Azure Sentinel Community GitHub](https://github.com/Azure/Azure-Sentinel) para criar um problema ou garfo e carregar uma contribuição.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu como começar a usar notebooks Jupyter no Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:

- [Caça proativamente por ameaças](hunting.md)
- [Use marcadores para salvar informações interessantes durante a caça](bookmarks.md)
