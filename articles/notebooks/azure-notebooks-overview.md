---
title: Visão geral do Azure Notebooks
description: Execute Jupyter Notebooks na nuvem usando o serviço gratuito Azure Notebooks, em que não é necessária nenhuma instalação ou configuração.
services: app-service
documentationcenter: ''
author: kraigb
manager: barbkess
ms.assetid: 9cea5a8e-c52d-4bdc-9e4a-cecdc1ad02c1
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: overview
ms.date: 04/05/2019
ms.author: kraigb
ms.openlocfilehash: 12d9a1a92c0b008244c7db4ec23d5a28888b042a
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2019
ms.locfileid: "71973158"
---
# <a name="overview-of-azure-notebooks"></a>Visão geral do Azure Notebooks

O Azure Notebooks é um serviço hospedado gratuito para desenvolver e executar os notebooks do Jupyter na nuvem sem instalação. O [Jupyter](https://jupyter.org/) (anteriormente conhecido como IPython) é um projeto de código-fonte aberto que permite que você combine facilmente um texto de Markdown, um código executável, dados persistentes, gráficos e visualizações em uma única tela compartilhável chamada de *notebook* (imagem cortesia de jupyter.org):

[![Exemplos de Jupyter notebooks](https://jupyter.org/assets/jupyterpreview.png)](https://jupyter.org/assets/jupyterpreview.png#lightbox)

Devido a essa poderosa combinação de código, gráficos e texto explicativo, o Jupyter popularizou muitos usos, incluindo instruções de ciência de dados, limpeza de dados e transformações, simulações numéricas, modelagens estatísticas e o desenvolvimento de modelos de aprendizado de máquina.

## <a name="hassle-free-experience"></a>Experiência sem complicações

O Azure Notebooks ajuda você a começar a usar rapidamente a criação de protótipos, ciências de dados, pesquisas acadêmicas ou a aprender a programar o Python:

- Um cientista de dados tem acesso instantâneo a um ambiente Anaconda completo sem precisar instalar nada.
- Um professor pode fornecer um ambiente Python sem complicações para os alunos.
- Um apresentador pode fazer uma palestra ou um webinar sem que os participantes precisem esperar 45 minutos pela instalação de um software.
- Um desenvolvedor ou um entusiasta pode usar notebooks como blocos rápidos de rascunhos de códigos.

Os notebooks se tornam ainda mais poderosos quando as pessoas podem colaborar neles por meio um serviço de nuvem acessível pelo navegador, como o Azure Notebooks (em versão prévia). Na nuvem, os usuários não precisam instalar o Jupyter localmente nem se preocupar com a manutenção de um ambiente. A nuvem também simplifica o compartilhamento de notebooks (e arquivos de dados associados) com outros usuários autorizados, evitando as complicações do compartilhamento de notebooks por meios externos, como repositórios de controle do código-fonte. Com o Azure Notebooks, os usuários também podem copiar (ou “clonar”) notebooks em suas próprias contas para modificação ou experimentação, o que é bastante útil para fins de instrução.

Como o Azure Notebooks é um código geral de criação, execução e o compartilhamento de plataforma, você pode usá-lo para muitos cenários diferentes:

- Aprender uma nova linguagem de programação – experimente um dos [tutoriais do frontpage](https://notebooks.azure.com/Microsoft/projects/samples/html/Introduction%20to%20Python.ipynb)
- Aprenda sobre ciência de dados – confira o [Livro de Jake VanderPlas](https://notebooks.azure.com/jakevdp/projects/PythonDataScienceHandbook)
- [Ministre um curso](https://notebooks.azure.com/garth-wells/projects/CUED-IA-Computing-Michaelmas) para centenas de alunos
- Faça um webinar online ou uma conferência sem perder tempo com instalações 
- Permita que os usuários do GitHub carreguem e executem os notebooks diretamente [criando uma notificação de lançamento do GitHub](https://notebooks.azure.com/help/projects/sharing/create-a-github-badge)
- Crie [apresentações de slides no estilo PowerPoint](https://notebooks.azure.com/help/jupyter-notebooks/slides) onde é possível executar códigos em slides!

Em resumo, o Azure Notebooks ajuda a realizar seu trabalho com mais eficiência e, portanto, a realizar mais.

> [!Note]
> Mais informações sobre o Jupyter em si podem ser encontradas em [jupyter.org](https://jupyter.org/) e nas [documentações do Jupyter](https://jupyter-notebook.readthedocs.io/en/latest/).

## <a name="pricing-and-quotas"></a>Preços e cotas

O Azure Notebooks é um serviço gratuito, mas cada projeto é limitado a 4 GB de memória e 1 GB de dados para evitar abusos. Os usuários legítimos que excedem esses limites devem resolver um desafio Captcha para continuar a execução de notebooks.

Para liberar todos os limites, entre no Azure Notebooks com uma conta usando o Azure Active Directory (como uma conta corporativa). Se essa conta estiver associada a uma assinatura do Azure, você poderá conectar quaisquer instâncias da Máquina Virtual de Ciência de Dados do Azure dentro dessa assinatura. Para obter mais informações, consulte [Gerenciar e configurar projetos – nível de computação](configure-manage-azure-notebooks-projects.md#compute-tier).

Servidores de notebook com certeza existem por no máximo 8 horas. Na maioria dos casos, o contêiner não está sujeito a esse limite e continuará sendo executado após esse prazo, mas as sessões de longa duração ocasionalmente poderão ser desligadas para estabilidade do sistema.

## <a name="available-kernels-and-environments"></a>Ambientes e kernels disponíveis

Para cada notebook, selecione o kernel (ou seja, o ambiente de tempo de execução) usado para executar todas as células de código. O Azure Notebooks dá suporte aos seguintes kernels:

- Python 2.7 + Anaconda2-5.3.0
- Python 3.6 + Anaconda3-5.3.0
- Python 3.5 + Anaconda3-4.2.0 (serão preteridos)
- R 3.4.1 + Microsoft R Open 3.4.1
- F# 4.1.9

O Azure Notebooks também inclui pacotes extras, além de distribuições de base. Os kernels do Python, por exemplo, incluem as bibliotecas numpy, pandas, scikit-saber, matplotlib e bokeh.

Também é possível personalizar um projeto para criar um ambiente para todos os notebooks do projeto. Para saber mais, confira [Início Rápido: Criar um projeto com um ambiente personalizado](quickstart-create-jupyter-notebook-project-environment.md).

Além das distribuições de base, o Azure Notebooks vem pré-instalado com muitos pacotes extras que são úteis para os cientistas de dados. Também é possível instalar seus próprios pacotes usando o processo típico para cada linguagem.

## <a name="pre-configured-jupyter-extensions"></a>Extensões pré-configuradas do Jupyter

O Azure Notebooks é pré-configurado com as seguintes extensões de Jupyter:

- [RISE](https://github.com/damianavila/RISE): uma extensão de apresentação de slides do Jupyter (também conhecida como live_reveal). Para obter mais informações, confira [Executar uma apresentação de slides do notebook](present-jupyter-notebooks-slideshow.md).
- [JupyterLab](https://github.com/jupyterlab/jupyterlab): um ambiente computacional completo para trabalhar com notebooks Jupyter.
- [Altair](https://github.com/ellisonbg/altair): uma biblioteca de visualização de estatística declarativa para Python.
- [BQPlot](https://github.com/bloomberg/bqplot): uma estrutura de plotagem interativa para notebooks Jupyter.
- [IpyWidgets](https://github.com/jupyter-widgets/ipywidgets): widgets HTML interativos para notebooks Jupyter.

## <a name="issues-and-getting-help"></a>Problemas e como obter ajuda

Como o Azure Notebooks ainda está em versão prévia, o serviço pode sofrer interrupções temporárias que podem ser mais frequentes ou mais duradouras que outros serviços do Azure. Alguns recursos podem estar incompletos ou conter bugs.

No momento, não é recomendável usar a versão prévia do Azure Notebooks para aplicativos comercialmente críticos ou notebooks e dados confidenciais.

Para discutir suas dúvidas sobre o Azure Notebooks, registre um problema sobre o [repositório GitHub](https://github.com/Microsoft/AzureNotebooks/issues).

## <a name="next-steps"></a>Próximas etapas  

- [Explorar notebooks de exemplo](azure-notebooks-samples.md)

- Inícios Rápidos:

  - [Criar e compartilhar um notebook](quickstart-create-share-jupyter-notebook.md)
  - [Clonar um notebook](quickstart-clone-jupyter-notebook.md)
  - [Migrar um notebook local do Jupyter](quickstart-migrate-local-jupyter-notebook.md)
  - [Usar um ambiente personalizado](quickstart-create-jupyter-notebook-project-environment.md)
  - [Entrar e definir uma ID de usuário](quickstart-sign-in-azure-notebooks.md)

- Tutoriais:

  - [Criar e executar um notebook](tutorial-create-run-jupyter-notebook.md  )

- Artigos de instrução:
  
  - [Criar e clonar projetos](create-clone-jupyter-notebooks.md)
  - [Configurar e gerenciar dispositivos](configure-manage-azure-notebooks-projects.md)
  - [Instalar pacotes em um notebook](install-packages-jupyter-notebook.md)
  - [Exibir uma apresentação de slides](present-jupyter-notebooks-slideshow.md)
  - [Trabalhar com arquivos de dados](work-with-project-data-files.md)
  - [Acessar recursos de dados](access-data-resources-jupyter-notebooks.md)
  - [Usar os Serviços do Azure Machine Learning](use-machine-learning-services-jupyter-notebooks.md)
