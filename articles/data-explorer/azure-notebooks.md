---
title: Use notebooks Azure para analisar dados no Azure Data Explorer
description: Este tópico mostra como criar uma consulta usando um Notebook Azure
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: 0f99e11be99f22feec73b72397b27522b90dbf49
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80522401"
---
# <a name="use-azure-notebooks-to-analyze-data-in-azure-data-explorer"></a>Use notebooks Azure para analisar dados no Azure Data Explorer

[O Azure Notebooks](https://notebooks.azure.com/) é um serviço de nuvem do Azure que simplifica a criação e o compartilhamento de [Notebooks Jupyter](https://jupyter.org/). O Azure Notebooks facilita a combinação de documentação, código e os resultados da execução do código.

> [!Note]
> * O procedimento a seguir usa o cliente Python no ambiente Azure Notebooks para consultar o Azure Data Explorer. No entanto, você também pode usar [o KQLmagic](https://docs.microsoft.com/azure/data-explorer/kqlmagic) para consultar o Azure Data Explorer.
> * Alguns usuários relataram problemas de autenticação usando o Edge; até que tais problemas sejam resolvidos, use um navegador diferente.

## <a name="create-a-project"></a>Criar um projeto

1. Abra [notebooks Azure](https://notebooks.azure.com/) no seu navegador e faça login.

1. Selecione a guia **Meus projetos** no cabeçalho. 

    [![](media/azurenotebooks/an-myprojects.png "My projects")](media/azurenotebooks/an-myprojects.png#lightbox)

1. Selecione **+ Novos Projetos**.
    
1. Na caixa de diálogo **Criar novo projeto:**
    1. Digite o nome do **projeto**.
    1. Limpe a caixa de seleção **pública.**
        >[!Important]
        > Se você não limpar a caixa de seleção pública, seu projeto será exposto na Internet aberta.
    1. Selecione **Criar**.
    
    ![Criar um novo projeto](media/azurenotebooks/an-create-new-project-blank.png)

    O ID do projeto é criado automaticamente.

## <a name="create-a-notebook"></a>Criar um notebook

1. Em seu novo projeto, crie um caderno. O notebook deve usar uma [linguagem suportada](https://github.com/Azure/azure-kusto-python#minimum-requirements).
Para criar um notebook, selecione **+ Novo** e selecione **Notebook**.

    ![Criar novo notebook](media/azurenotebooks/an-create-new-notebook-menu.png) 

1. Na caixa de diálogo **Criar novo notebook,** digite o nome do notebook.

1. Selecione **Python 3.6** e selecione **Novo**.
    
    ![Criar novo notebook](media/azurenotebooks/an-create-new-notebook.png) 
    
1. Em seu projeto, selecione seu novo notebook.

    ![Selecione novo notebook](media/azurenotebooks/an-select-notebook.png)

    Aguarde até que o kernel Python seja inicializado. Quando o ícone do círculo preenchido muda para um ícone de círculo oco, você pode prosseguir.

    ![Inicializa o kernel](media/azurenotebooks/an-python-init-icon.png)

1. Para importar o módulo do sistema, digite os seguintes comandos e selecione **Executar**:
    ```python
    import sys
    sys.version
    ```

    > [!Note]
    > Para executar um celular, você também pode pressionar Shift+Enter.

1.  Para importar classes SDK, digite os seguintes comandos e selecione **Executar**:
    ```python
    from azure.kusto.data.request import KustoClient, KustoConnectionStringBuilder
    ```

1.  Para construir a seqüência de conexões e iniciar o cliente Kusto, digite os seguintes comandos e selecione **Executar**:  
    ```python
    kcsb = KustoConnectionStringBuilder.with_aad_device_authentication("https://help.kusto.windows.net")
    kc = KustoClient(kcsb)
    kc.execute("Samples", ".show version")
    ```
1. No prompt, abra uma nova [https://aka.ms/devicelogin](https://aka.ms/devicelogin)guia do navegador para . 
   
1. Digite o código de autorização e faça@microsoft.comlogin em sua conta AAD ( ). O cliente `kc` Kusto agora pode autenticar o Azure Data Explorer usando sua identidade.

1. Retorne ao seu notebook para ver o resultado da autenticação. 

[![](media/azurenotebooks/an-python-commands.png "Python commands")](media/azurenotebooks/an-python-commands.png#lightbox)

## <a name="execute-a-kusto-query"></a>Executar uma consulta kusto

1. Digite sua consulta kusto e selecione **Executar**. Por exemplo: 

    ```python
    query= "StormEvents | project State, EventType | take 10"
    response = kc.execute("Samples", query)
    for row in response.primary_results[0]:
        print(", ".join(row))
    ```    

[![](media/azurenotebooks/an-commands.png "Python commands")](media/azurenotebooks/an-commands.png#lightbox)

## <a name="next-steps"></a>Próximas etapas

* [Repo Kusto-python GitHub](https://github.com/Azure/azure-kusto-python)
