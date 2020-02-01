---
title: Importar ou exportar dados com a configuração do Azure App | Microsoft Docs
description: Saiba como importar ou exportar dados de ou para a configuração do Azure App
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/24/2019
ms.author: lcozzens
ms.custom: mvc
ms.openlocfilehash: 64fcc8396fc1b771d0095ee595fd177d7fe99b58
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/31/2020
ms.locfileid: "76899520"
---
# <a name="import-or-export-configuration-data"></a>Importar ou exportar dados de configuração

Azure App configuração dá suporte a operações de importação e exportação de dados. Use essas operações para trabalhar com dados de configuração em massa e trocar dados entre o repositório de configuração do aplicativo e o projeto de código. Por exemplo, você pode configurar um repositório de configuração de aplicativo para teste e outro para produção. Em seguida, você pode copiar as configurações do aplicativo entre elas por meio de um arquivo para não precisar inserir dados duas vezes.

Este artigo fornece um guia para importar e exportar dados com a configuração do aplicativo.

## <a name="import-data"></a>Importar dados

A importação traz dados de configuração para um repositório de configurações de aplicativo de uma fonte existente, em vez de inseri-lo manualmente. Use a função de importação para migrar dados para um repositório de configuração de aplicativo ou agregar dados de várias fontes. A configuração de aplicativo dá suporte à importação de um arquivo JSON, YAML ou Properties.

Importe dados usando o [portal do Azure](https://portal.azure.com) ou o [CLI do Azure](./scripts/cli-import.md). No portal do Azure, siga estas etapas:

1. Navegue até o repositório de configuração do aplicativo e selecione **importar/exportar**.

2. Na guia **importar** , selecione **serviço de origem** > **arquivo de configuração**.

3. Selecione **for language** > **tipo de arquivo**.

4. Selecione o ícone de **pasta** e navegue até o arquivo a ser importado.

    ![Arquivo de importação](./media/import-file.png)

5. Selecione um **separador**e, opcionalmente, insira um **prefixo** a ser usado para nomes de chave importados.

6. Opcionalmente, selecione um **rótulo**.

7. Selecione **aplicar** para concluir a importação.

    ![Importação de arquivo concluída](./media/import-file-complete.png)

## <a name="export-data"></a>Exportar dados

Exportar grava os dados de configuração armazenados na configuração do aplicativo para outro destino. Use a função de exportação, por exemplo, para salvar dados em um repositório de configuração de aplicativo em um arquivo que é inserido com o código do aplicativo durante a implantação.

Exporte dados usando o [portal do Azure](https://portal.azure.com) ou o [CLI do Azure](./scripts/cli-export.md). No portal do Azure, siga estas etapas:

1. Navegue até o repositório de configuração do aplicativo e selecione **importar/exportar**.

2. Na guia **Exportar** , selecione **serviço de destino** > **arquivo de configuração**.

3. Opcionalmente, insira um **prefixo** e selecione um **rótulo** e um ponto no tempo para que as chaves sejam exportadas.

4. Selecione um **tipo de arquivo** > **separador**.

5. Selecione **aplicar** para concluir a exportação.

    ![Exportação de arquivo concluída](./media/export-file-complete.png)

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Criar um aplicativo Web ASP.NET Core](./quickstart-aspnet-core-app.md)  
