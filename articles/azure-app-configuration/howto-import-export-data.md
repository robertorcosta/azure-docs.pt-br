---
title: Importar ou exportar dados com a configuração do aplicativo Azure
description: Saiba como importar ou exportar dados para ou a partir da configuração do aplicativo Azure
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: lcozzens
ms.openlocfilehash: 5b3d6d0561d7d6d6b23cb4f579b0988850da9771
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80056876"
---
# <a name="import-or-export-configuration-data"></a>Importar ou exportar dados de configuração

A configuração do aplicativo Azure suporta operações de importação e exportação de dados. Use essas operações para trabalhar com dados de configuração em massa e trocar dados entre o seu armazenamento de configuração de aplicativos e o projeto de código. Por exemplo, você pode configurar uma loja de configuração de aplicativos para testes e outra para produção. Você pode copiar as configurações do aplicativo entre eles para que você não precise inserir dados duas vezes.

Este artigo fornece um guia para importação e exportação de dados com configuração de aplicativos. Se você quiser configurar uma sincronização contínua com o seu repo GitHub, dê uma olhada no nosso [GitHub Action](https://aka.ms/azconfig-gha1).

## <a name="import-data"></a>Importar dados

A importação traz dados de configuração para uma loja de configuração de aplicativos a partir de uma fonte existente. Use a função de importação para migrar dados para um armazenamento de configuração de aplicativos ou agregar dados de várias fontes. A configuração do aplicativo suporta a importação de um arquivo JSON, YAML ou propriedades.

Importar dados usando o [portal Azure](https://portal.azure.com) ou o [Azure CLI](./scripts/cli-import.md). No portal do Azure, siga estas etapas:

1. Navegue até a loja de configuração do aplicativo e selecione **Importação/Exportação** no menu **Operações.**

1. Na guia **Importar,** selecione Arquivo**de configuração do** **serviço** > de origem .

1. Selecione **Para o idioma** e selecione o tipo de entrada desejado.

1. Selecione o ícone **Pasta** e navegue até o arquivo para importar.

    ![Arquivo de importação](./media/import-file.png)

1. Selecione um **Separador**e, opcionalmente, digite um **Prefixo** para usar para nomes de tecla importados.

1. Opcionalmente, selecione um **Rótulo**.

1. Selecione **Aplicar** para concluir a importação.

    ![Arquivo de importação concluído](./media/import-file-complete.png)

## <a name="export-data"></a>Exportar dados

Exportar grava dados de configuração armazenados na configuração do aplicativo para outro destino. Use a função de exportação, por exemplo, para salvar dados em uma loja de configuração de aplicativos para um arquivo incorporado com o código do aplicativo durante a implantação.

Exportar dados usando o [portal Azure](https://portal.azure.com) ou o [Azure CLI](./scripts/cli-export.md). No portal do Azure, siga estas etapas:

1. Navegue até a loja de configuração do aplicativo e selecione **Importação/Exportação**.

1. Na guia **Exportar,** selecione **Arquivo de** > **configuração de**serviço de destino .

1. Digite opcionalmente um **Prefixo** e selecione um **Rótulo** e um point-in-time para que as teclas sejam exportadas.

1. Selecione um **separador do tipo** > **de arquivo**.

1. Selecione **Aplicar** para concluir a exportação.

    ![Arquivo de exportação concluído](./media/export-file-complete.png)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Criar um aplicativo Web ASP.NET Core](./quickstart-aspnet-core-app.md)  
