---
title: Usar notebooks com o Azure Sentinel para a busca de segurança
description: Este artigo descreve como usar notebooks com os recursos de busca do Azure Sentinel.
services: sentinel
author: yelevin
ms.author: yelevin
ms.assetid: 1721d0da-c91e-4c96-82de-5c7458df566b
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.custom: mvc
ms.date: 11/25/2019
ms.openlocfilehash: bf63d5c8cb46fd791508af40dcefd7b39d4ba9de
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83652020"
---
# <a name="use-jupyter-notebooks-to-hunt-for-security-threats"></a>Usar o Jupyter Notebooks para procurar ameaças à segurança

A base do Azure Sentinel é o armazenamento de dados; ele combina consultas de alto desempenho, esquema dinâmico e escalas para grandes volumes de dados. O portal do Azure e todas as ferramentas do Azure Sentinel usam uma API comum para acessar esse armazenamento de dados. A mesma API também está disponível para ferramentas externas, como notebooks [Jupyter](https://jupyter.org/) e Python. Embora muitas tarefas comuns possam ser executadas no portal, o Jupyter estende o escopo do que você pode fazer com esses dados. Ele combina programação completa com uma enorme coleção de bibliotecas para aprendizado de máquina, visualização e análise de dados. Esses atributos fazem do Jupyter uma ferramenta interessante para investigação de segurança e busca.

![notebook de exemplo](./media/notebooks/sentinel-notebooks-map.png)

Integramos a experiência do Jupyter ao portal do Azure, facilitando a criação e a execução de notebooks para analisar seus dados. A biblioteca *Kqlmagic* permite fazer consultas do Azure Sentinel e executá-las diretamente dentro de um notebook. As consultas usam a [linguagem de consulta Kusto](https://kusto.azurewebsites.net/docs/query/index.html). Vários notebooks, desenvolvidos por alguns analistas de segurança da Microsoft, são empacotados com o Azure Sentinel. Alguns desses notebooks são criados para um cenário específico e podem ser usados no estado em que se encontram. Outros são destinados como exemplos para ilustrar técnicas e recursos que você pode copiar ou adaptar para uso em seus notebooks. Outros notebooks também podem ser importados do GitHub da Comunidade do Azure Sentinel.

A experiência integrada do Jupyter usa [o Azure Notebooks](https://notebooks.azure.com/) para armazenar, compartilhar e executar notebooks. Você também pode executar esses notebooks localmente se tiver um ambiente de Python e Jupyter em seu computador ou em outros ambientes do JupterHub, como o Azure Databricks.

Os notebooks têm dois componentes:

- A interface baseada em navegador na qual você insere e executa consultas e código, e onde os resultados da execução são exibidos.
- Um *kernel* que é responsável pela análise e pela execução do próprio código. 

No Azure Notebooks, por padrão, esse kernel é executado na *Computação e armazenamento gratuito na nuvem* do Azure. Se os seus notebooks incluírem modelos ou visualizações complexos de aprendizado de máquina, use recursos de computação mais avançados e dedicados, como DSVM [(Máquinas Virtuais de Ciência de Dados)](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/). Os notebooks em sua conta são mantidos privados, a menos que você opte por compartilhá-los.

Os notebooks do Azure Sentinel usam muitas bibliotecas populares de Python, como pandas, matplotlib, bokeh e outros. Há muitos outros pacotes do Python para você escolher, cobrindo áreas como:

- Visualizações e gráficos
- Processamento de dados e análise
- Estatísticas e computação numérica
- Aprendizado de máquina e aprendizado profundo

Também lançamos algumas ferramentas de segurança de open-source do Jupyter em um pacote chamado [msticpy](https://github.com/Microsoft/msticpy/). Esse pacote é usado em muitos dos notebooks incluídos. As ferramentas Msticpy foram projetadas especificamente para ajudar na criação de notebooks para busca e investigação, e estamos trabalhando ativamente em novos recursos e aprimoramentos.

Os notebooks iniciais incluem:

- **Investigação guiada – Alertas de processo**: permitem que você faça a triagem rapidamente de alertas analisando a atividade no host ou hosts afetados.
- **Busca guiada – Explorador de host do Windows**: permite explorar atividade de conta, execuções de processo, atividade de rede e outros eventos em um host.
- **Busca guiada – Exploração do Office365**: procura uma atividade suspeita do Office 365 em vários conjuntos de dados do Office 365.

O [repositório GitHub da Comunidade do Azure Sentinel](https://github.com/Azure/Azure-Sentinel) é o local para os notebooks futuros do Azure Sentinel criados pela Microsoft ou contribuídos pela comunidade.

Para usar os notebooks, você deve ter uma conta do Azure Notebooks. Para saber mais, confira [Início Rápido: entrar e definir uma ID de usuário](https://docs.microsoft.com/azure/notebooks/quickstart-sign-in-azure-notebooks) da documentação do Azure Notebooks. Para criar essa conta, você poderá usar a opção **Inscrever-se no Azure Notebooks** na barra de comandos no **Azure Sentinel – Notebooks**:

> [!div class="mx-imgBorder"]
>![Opção Inscreva-se no Azure Notebooks](./media/notebooks/sentinel-azure-sign-up-azure-notebooks.png)

Você pode executar um notebook direto do Azure Sentinel ou clonar todos os notebooks do Azure Sentinel para um novo projeto do Azure Notebooks.

## <a name="run-a-notebook-from-azure-sentinel"></a>Executar um notebook do Azure Sentinel
 
1. No portal do Azure, navegue até **Azure Sentinel** > **Gerenciamento de ameaças** > **Notebooks**, em que você pode ver os notebooks que o Azure Sentinel fornece. 

2. Selecione notebooks individuais para ler suas descrições, tipos de dados necessários e fontes de dados. Por exemplo:
    
    > [!div class="mx-imgBorder"]
    > ![iniciar notebook](./media/notebooks/sentinel-azure-notebooks-launch.png)

3. Selecione o notebook que você deseja usar e, em seguida, selecione **Iniciar o notebook (versão prévia)** para clonar e configurar o notebook em um novo projeto do Azure Notebooks que se conecta ao seu workspace do Azure Sentinel. Quando o processo estiver concluído, o notebook será aberto dentro do Azure Notebooks para que você execute.

## <a name="clone-azure-sentinel-notebooks-to-a-new-azure-notebooks-project"></a>Clonar notebooks do Azure Sentinel para um novo projeto do Azure Notebooks

Este procedimento cria um projeto do Azure Notebooks para você, que contém os notebooks do Azure Sentinel. Em seguida, você pode executar os notebooks como estão, ou fazer alterações neles e executá-los.

1. No portal do Azure, navegue até **Azure Sentinel** > **Gerenciamento de ameaças** > **Notebooks** e selecione **Clonar Notebooks** na barra de comandos:
  
    > [!div class="mx-imgBorder"]
    >![Opção Clonar notebooks](./media/notebooks/sentinel-azure-clone-notebooks.png)

2. Quando a caixa de diálogo a seguir for exibida, selecione **Importar** para clonar o repositório GitHub em seu projeto do Azure Notebooks. Se você não tiver uma conta existente do Azure Notebooks, será solicitado a crie uma e entrar.

   ![Importar notebook](./media/notebooks/sentinel-notebooks-clone.png)

3. Na caixa de diálogo **Carregar Repositório GitHub**, não selecione **Clonar recursivamente** porque essa opção refere-se aos repositórios GitHub vinculados. Para o nome do projeto, use o nome padrão ou digite um novo. Em seguida, clique em **Importar** para iniciar a clonagem do conteúdo do GitHub, o que pode levar alguns minutos para ser concluído.

   ![Importar notebook](./media/notebooks/sentinel-create-project.png)

4. Abra o projeto que você acabou de criar e, em seguida, abra a pasta **Notebooks** para ver os notebooks. Por exemplo:

   ![Importar repositório](./media/notebooks/sentinel-open-notebook1.png)

Em seguida, você pode executar os notebooks do Azure Notebooks. Para retornar a esses notebooks do Azure Sentinel, selecione **Acessar seus Notebooks** na barra de comandos no **Azure Sentinel – Notebooks**:

> [!div class="mx-imgBorder"]
>![Opção Acessar seus Notebooks](./media/notebooks/sentinel-azure-to-go-notebooks.png)


## <a name="using-notebooks-to-hunt"></a>Como usar notebooks para buscar

Cada notebook orienta você pelas etapas para realizar uma busca ou investigação. As bibliotecas e outras dependências necessárias para o notebook podem ser instaladas no próprio notebook ou por meio de um procedimento de configuração simples. A configuração que vincula o seu projeto de notebook à sua assinatura do Azure Sentinel é automaticamente provisionada nas etapas anteriores.

1. Se você ainda não usar o Azure Notebooks, vá até a opção **Acessar seus Notebooks** na barra de comandos em **Azure Sentinel – Notebooks**:
    
    > [!div class="mx-imgBorder"]
    >![Opção Acessar seus Notebooks](./media/notebooks/sentinel-azure-to-go-notebooks.png)
    
    Em Azure Notebooks, selecione **Meus Projetos**, em seguida, o projeto que contém os notebooks do Azure Sentinel e, por fim, a pasta **Notebooks**.
    
2. Antes de abrir um notebook, lembre-se de que, por padrão, a computação gratuita é selecionada para executar os notebooks:
    
   ![selecionar notebook](./media/notebooks/sentinel-open-notebook2.png)
    
    Se você tiver configurado uma DSVM (Máquina Virtual de Ciência de Dados) para usar conforme explicado na introdução, selecione a DSVM e autentique antes de abrir o primeiro notebook. 

3. Selecione um notebook para abri-lo.
    
    Na primeira vez que você abrir um notebook, você deverá selecionar uma versão do kernel. Se não for solicitado, selecione a versão do kernel em **Kernel** >  **Alterar kernel** e, em seguida, selecione uma versão que seja pelo menos 3.6. A versão do kernel selecionada é exibida no canto superior direito da janela do notebook:
    
   ![selecionar notebook](./media/notebooks/sentinel-select-kernel.png)

4. Antes de fazer alterações no notebook que você baixou, é uma boa ideia fazer uma cópia do notebook original e trabalhar na cópia. Para fazer isso, selecione **Arquivo** > **Fazer uma Cópia**. Trabalhar em cópias permite que você atualize com segurança para versões futuras de notebooks sem substituir os seus dados.
    
    Agora você está pronto para executar ou editar o notebook selecionado.

Recomendações:

- Para obter uma breve introdução à consulta de dados no Azure Sentinel, confira o notebook [GetStarted](https://github.com/Azure/Azure-Sentinel-Notebooks/blob/345cf9f7c8f6137f5af4593a3f9d7568acd6cbc2/DeprecatedNotebooks/Get%20Started.ipynb) na pasta principal **Notebooks**. 

- Você encontrará notebooks de exemplo adicionais na subpasta **Exemplo-Notebooks**. Esses notebooks de exemplo foram salvos com dados, para que seja mais fácil ver a saída pretendida. É recomendável exibir esses notebooks em [nbviewer](https://nbviewer.jupyter.org/). 

- A pasta **Tutoriais** contém notebooks que descrevem, por exemplo: Como definir a versão padrão do Python, configurar uma DSVM, criar indicadores do Azure Sentinel de um notebook e outros assuntos.

Os notebooks fornecidos são destinados como ferramentas úteis, ilustrações e exemplos de código que você pode usar no desenvolvimento de seus notebooks.

Mande comentários, sugestões, solicitações de recursos, contribuições de notebooks, relatórios de bugs ou melhorias e adições a notebooks existentes. Acesse o [GitHub da Comunidade do Azure Sentinel](https://github.com/Azure/Azure-Sentinel) para criar uma questão ou fork e carregar uma contribuição.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a começar a usar os notebooks do Jupyter no Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:

- [Buscar ameaças proativamente](hunting.md)
- [Use indicadores para salvar informações interessantes durante a busca](bookmarks.md)
