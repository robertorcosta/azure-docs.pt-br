---
title: Importar e exportar dados com projetos no Azure Notebooks
description: Como trazer dados para um projeto do Azure Notebooks de fontes externas e como exportar dados de um projeto.
services: app-service
documentationcenter: ''
author: kraigb
manager: barbkess
ms.assetid: 586b423b-6668-4bdd-9592-4c237d7458fb
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: b33d5dbfa354061b9b25069b3b300010b7cd49bf
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2019
ms.locfileid: "71970204"
---
# <a name="work-with-data-files-in-azure-notebook-projects"></a>Trabalhar com arquivos de dados em projetos do Azure Notebook

Os dados são a força vital de muitos blocos de notas do Jupyter, especialmente os blocos de notas utilizados na ciência de dados. Com o Azure Notebooks, é fácil importar de várias fontes para um projeto e, em seguida, usar esses dados dos blocos de anotações. Você também pode fazer com que os blocos de anotações gerem dados que serão armazenados no projeto e que poderão ser baixados para serem usados em outro lugar.

O menu **Dados** dentro de um bloco de anotações em execução também disponibiliza comandos **Carregar** e **Fazer o download** que funcionam com arquivos no projeto e com arquivos temporários da sessão atual do bloco de anotações.

Você também pode usar código em um bloco de anotações para acessar uma variedade de fontes de dados diretamente, incluindo arquivos em um projeto. Você também pode acessar dados arbitrários usando comandos em uma célula de código. Como esses dados são armazenados em variáveis dentro da sessão do bloco de anotações, eles não serão salvos no projeto, a menos que você use código para gerar especificamente os arquivos do projeto.

A experiência de trabalhar com código nos dados funciona melhor no próprio bloco de anotações em execução: para isso, confira o [bloco de anotações de exemplo Ter acesso aos seus dados no Azure Notebooks](https://notebooks.azure.com/Microsoft/projects/samples/html/Getting%20to%20your%20Data%20in%20Azure%20Notebooks.ipynb).

O restante deste artigo fornece detalhes sobre operações de arquivo no nível do projeto.

## <a name="import-data"></a>Importar dados

Você pode trazer arquivos para um projeto no painel do projeto ou em um bloco de anotações em execução usando o menu **Dados** ou um comando como `curl`.

### <a name="import-files-from-the-project-dashboard"></a>Importar arquivos do painel do projeto

1. No projeto, navegue até a pasta para onde você deseja importar os arquivos.

1. Escolha o comando **Carregar** e **Da URL** ou **Do computador** e crie os detalhes necessários para os dados que você deseja importar:

   - **Da URL**: Insira o endereço de origem no campo **URL do Arquivo** e o nome do arquivo para atribuir ao bloco de anotações do projeto no campo **Nome do Arquivo**. Em seguida, escolha **+ Adicionar Arquivo** para adicionar a URL à lista de carregamento. Repita o processo para todas as URLs adicionais e escolha **Concluído**.

     ![Carregar de URL popup](media/quickstarts/upload-from-url-popup.png)

   - **Do computador**: Arraste e solte os arquivos no popup ou marque **Escolher Arquivos** e, em seguida, procure e escolha os arquivos de dados que você deseja importar. Você pode soltar ou escolher qualquer número de arquivos, de qualquer tipo e formato, já que é o código no bloco de anotações que abre o arquivo e analisa seus dados.

     ![Carregar do popup do computador](media/quickstarts/upload-from-computer-popup.png)

1. Uma vez importados, os arquivos são exibidos no painel do projeto e podem ser acessados dentro do código do bloco de anotações usando nomes de caminhos relativos para a pasta que os contêm.

### <a name="import-files-from-the-file-menu-in-a-notebook"></a>Importar arquivos do menu Arquivo em um bloco de anotações

1. Dentro de um bloco de anotações em execução, escolha o comando **Carregar** > **Arquivo**:

    ![Comando de menu Carregar Arquivo dentro de um bloco de anotações](media/file-menu-upload.png)

1. Na caixa de diálogo que é aberta, navegue até os arquivos e escolha aqueles que você deseja carregar. Você pode selecionar qualquer número de arquivos de qualquer tipo. Escolha **Abrir** quando tiver concluído.

1. No popup **Carregar status** exibido, escolha uma **Pasta de destino** na lista suspensa:

    - Pasta da sessão ( *~/* ): Carrega arquivos para a sessão atual do bloco de anotações, mas não cria arquivos no projeto. A pasta de sessão cria um par com a pasta do projeto, mas ela não persistirá após o término da sessão. Para acessar arquivos de sessão no código, use o prefixo *../* nos nomes de arquivo com o caminho relativo.

        O uso da pasta de sessão é útil para experimentações e evita a acumulação de arquivos que poderão ou não ser necessários a longo prazo no projeto. Você também pode carregar arquivos para a pasta de sessão que tenha nomes idênticos aos arquivos do projeto sem causar conflitos ou precisar renomear os arquivos. Por exemplo, digamos que você tenha uma versão de *data.csv* no projeto, mas deseja experimentar uma versão diferente de *data.csv*. Ao carregar o arquivo na pasta de sessão, você pode executar o bloco de anotações usando dados no arquivo carregado (referindo-se a ele no código usando *../data.csv*) em vez dos dados no arquivo do projeto.

    - Pasta do projeto ( */project*): carrega arquivos no projeto onde eles poderão ser acessados usando nomes de caminho relativos no código. Carregar um arquivo para essa pasta é o mesmo que carregar um arquivo no painel do projeto. O arquivo é salvo com o projeto e ficará disponível em sessões posteriores.

        O carregamento falhará se você tentar carregar um arquivo que tenha o mesmo nome de um arquivo existente no projeto. Para substituir um arquivo, carregue o novo arquivo no painel do projeto e, assim, terá a opção de substitui-lo.

1. Escolha **Iniciar carregamento** para concluir o processo.

### <a name="create-or-import-files-using-commands"></a>Criar ou importar arquivos usando comandos

Você pode usar comandos em um terminal ou em uma célula de código do Python para criar arquivos em pastas de projeto e de sessão. Por exemplo, comandos como `curl` e `wget` fazem o download de arquivos da Internet diretamente.

Para fazer o download de arquivos no terminal, escolha o comando **Terminal** no painel do projeto e, em seguida, digite os comandos adequados:

```bash
curl https://raw.githubusercontent.com/petroleum101/figures/db46e7f48b8aab67a0dfe31696f6071fb7a84f1e/oil_price/oil_price.csv -o oil_price.csv

wget https://raw.githubusercontent.com/petroleum101/figures/db46e7f48b8aab67a0dfe31696f6071fb7a84f1e/oil_price/oil_price.csv -o oil_price.csv
```

Ao usar uma célula de código do Python em um bloco de anotações, use o prefixo `!` nos comandos.

A pasta do projeto é a pasta padrão, portanto, especificar um nome de arquivo de destino, como *oil_price.csv* criará o arquivo no projeto. Para criar um arquivo de sessão, use prefixo *... /* no nome como em *... /oil_price.csv*.

### <a name="create-files-in-code"></a>Criar arquivos no código

Ao usar o código que cria um arquivo, como a função `write_csv` pandas, os nomes dos caminhos sempre serão relativos à pasta do projeto. Usar *../* cria um arquivo de sessão que é descartado quando o bloco de anotações é parado e fechado.

## <a name="export-files"></a>Exportar arquivos

Você pode exportar dados do painel do projeto ou de dentro de um bloco de anotações.

## <a name="export-files-from-the-project-dashboard"></a>Exportar arquivos do painel do projeto

No painel do projeto, clique com o botão direito do mouse em um arquivo e escolha **Fazer o download**:

![Faça o download do comando no menu de contexto do item do projeto](media/download-command.png)

Você também pode escolher um arquivo e usar o comando **Fazer o download** (atalho de teclado: d) no painel:

![Fazer o download do comando da barra de ferramentas no painel do projeto](media/download-command-toolbar.png)

## <a name="export-files-from-the-data-menu-in-a-notebook"></a>Exportar arquivos do menu Dados em um bloco de anotações

1. Escolha o comando de menu **Fazer o download do** > **arquivo**:

    ![Comando de menu Fazer o download dos dados dentro de um bloco de anotações](media/file-menu-download.png)

1. Um popup é exibido e mostra as pastas na sessão. A pasta do *projeto* contém os arquivos do projeto:

    ![O popup de comando Fazer o download de dados no qual você escolhe arquivos e pastas](media/file-menu-download-popup.png)

1. Marque as caixas à esquerda dos arquivos e pastas que deseja baixar e, em seguida, escolha **Baixar selecionados**.

1. O bloco de anotações prepara um único arquivo *.zip* que contém os arquivos escolhidos e eles são salvos como você normalmente faz em seu navegador. O bloco de anotações cria um arquivo *.zip* mesmo quando você baixa um único arquivo.

## <a name="next-steps"></a>Próximas etapas

- [Acessar dados de nuvem em um notebook](access-data-resources-jupyter-notebooks.md)
