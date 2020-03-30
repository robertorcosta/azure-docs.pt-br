---
title: Query Azure Data Lake Analytics - Visual Studio
description: Saiba como instalar ferramentas do Data Lake para o Visual Studio e como desenvolver e testar scripts U-SQL.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: ad8a6992-02c7-47d4-a108-62fc5a0777a3
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: dacce0d4f40f077b5da6221000192a4398da99e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260339"
---
# <a name="develop-u-sql-scripts-by-using-data-lake-tools-for-visual-studio"></a>Desenvolvimento de scripts U-SQL usando as ferramentas do Data Lake para Visual Studio

[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

O Azure Data Lake Analytics e as Ferramentas do Stream Analytics incluem a funcionalidade relacionada a dois serviços do Azure, Azure Data Lake Analytics e Azure Stream Analytics. Para obter mais informações sobre os cenários do Azure Stream Analytics, consulte as [ferramentas do Azure Stream Analytics para o Visual Studio](../stream-analytics/stream-analytics-tools-for-visual-studio-install.md).

Este artigo descreve como usar o Visual Studio para criar contas do Azure Data Lake Analytics. Você pode definir empregos no [U-SQL](data-lake-analytics-u-sql-get-started.md)e enviar empregos para o serviço Data Lake Analytics. Para obter mais informações sobre o Data Lake Analytics, consulte [a visão geral do Azure Data Lake Analytics](data-lake-analytics-overview.md).

> [!IMPORTANT]
> Recomendamos que você atualize para o Azure Data Lake Tools para visual studio versão 2.3.3000.4 ou posterior. As versões anteriores não estão disponíveis para download e foram preteridas.
>
> 1. Verifique se você estiver usando uma versão anterior ao 2.3.3000.4 do Azure Data Lake Tools para Visual Studio.
>
>    ![Verifique a versão da ferramenta](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-about-data-lake.png)
>
> 1. Se sua versão for uma versão anterior do 2.3.3000.4, atualize o Azure Data Lake Tools para Visual Studio visitando o Centro de download:
>    - [Para Visual Studio 2017 e 2019](https://marketplace.visualstudio.com/items?itemName=ADLTools.AzureDataLakeandStreamAnalyticsTools)
>    - [Para Visual Studio 2013 e 2015](https://www.microsoft.com/en-us/download/details.aspx?id=49504)

## <a name="prerequisites"></a>Pré-requisitos

* **Visual Studio**: há suporte para todas as edições, exceto Express.

  * Visual Studio 2019
  * Visual Studio 2017
  * Visual Studio 2015
  * Visual Studio 2013

* **SDK do Microsoft Azure para .NET** versão 2.7.1 ou posterior. Instale-o usando o [Web Platform Installer](https://www.microsoft.com/web/downloads/platform.aspx).
* Uma conta do **Data Lake Analytics**. Para criar uma conta, confira [Introdução ao Azure Data Lake Analytics usando o portal do Azure](data-lake-analytics-get-started-portal.md).

## <a name="install-azure-data-lake-tools-for-visual-studio"></a>Instale as Ferramentas do Azure Data Lake para Visual Studio

Este tutorial requer que Ferramentas do Data Lake Tools para Visual Studio esteja instalado. Para obter mais informações, consulte [Install Data Lake Tools for Visual Studio](data-lake-analytics-data-lake-tools-install.md).

## <a name="connect-to-an-azure-data-lake-analytics-account"></a>Conecte-se com uma conta do Azure Data Lake Analytics

1. Abra o Visual Studio.

1. Abra **o Server Explorer** selecionando O **Visualização** > **Do Servidor Explorador**.

1. Clique com o botão direito do mouse **no Azure**e selecione **Conectar à assinatura do Microsoft Azure**. Em **Login em sua conta,** siga as instruções.

1. No **Server Explorer,** selecione **Azure** > **Data Lake Analytics**. Você verá uma lista das suas contas do Data Lake Analytics.

## <a name="write-your-first-u-sql-script"></a>Escreve seu primeiro script U-SQL

O texto a seguir é um script U-SQL simples. Ele define um pequeno conjunto de dados e grava o conjunto de dados no repositório padrão do Data Lake Store como um arquivo chamado `/data.csv`.

```sql
USE DATABASE master;
USE SCHEMA dbo;
@a  = 
    SELECT * FROM 
        (VALUES
            ("Contoso", 1500.0),
            ("Woodgrove", 2700.0)
        ) AS 
              D( customer, amount );
OUTPUT @a
    TO "/data.csv"
    USING Outputters.Csv();
```

## <a name="submit-a-data-lake-analytics-job"></a>Enviar um trabalho da Análise Data Lake

1. No Visual Studio, selecione **Arquivo** > **Novo** > **Projeto**.

1. Selecione o tipo **de projeto U-SQL** e selecione **Next**. Em **Configurar seu novo projeto,** selecione **Criar**.

   O Visual Studio cria uma solução que contém um arquivo **Script.usql.**

1. Cole o script de [Write seu primeiro script U-SQL](#write-your-first-u-sql-script) na janela **Script.usql.**

1. No **Solution Explorer,** clique com o botão direito do mouse **Script.usql**e selecione **Enviar script**.

1. Em **Enviar trabalho,** escolha sua conta Data Lake Analytics e selecione **Enviar**.

   ![Enviar projeto de U-SQL do Visual Studio](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-submit-job-vs2019.png)

Após o envio do trabalho, a guia **Exibição do trabalho** é aberta para mostrar o andamento do trabalho.

* **Resumo do trabalho** mostra o resumo do trabalho.
* **Grafo do Trabalho** visualiza o andamento do trabalho.
* **Operações de metadados** mostra todas as ações que foram executadas no catálogo de U-SQL.
* **Dados** mostra todas as entradas e saídas.
* **Histórico de estado** mostra os detalhes da linha do tempo e do estado.
* **A análise da UA** mostra quantas UA foram usadas no trabalho e explora simulações de diferentes estratégias de alocação de UA.
* **Diagnósticos** fornece uma análise avançada para otimização de desempenho e a execução do trabalho.

![Grafo de desempenho de trabalho do Data Lake Analytics do U-SQL do Visual Studio](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-performance-graph.png)

Para ver o status de trabalho mais recente e atualizar a tela, **selecione Atualizar**.

## <a name="check-job-status"></a>Verificar o status do trabalho

1. No **Server Explorer,** selecione **Azure** > **Data Lake Analytics**.

1. Expanda o nome da conta do Data Lake Analytics.

1. Clique duas vezes em **Trabalhos**.

1. Selecione o trabalho enviado anteriormente.

## <a name="see-the-job-output"></a>Veja a saída do trabalho

1. No **Server Explorer,** navegue até o trabalho que você enviou.

1. Clique na guia **Dados**.

1. Na guia **Saídas do trabalho**, selecione o arquivo `"/data.csv"`.

## <a name="next-steps"></a>Próximas etapas

* [Execute scripts U-SQL em sua estação de trabalho para teste e depuração](data-lake-analytics-data-lake-tools-local-run.md)
* [Depurar código C# em trabalhos U-SQL usando as Ferramentas do Azure Data Lake para Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md)
* [Usar as Ferramentas do Azure Data Lake para Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md)
