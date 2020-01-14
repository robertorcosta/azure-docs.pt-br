---
title: 'Tutorial: Usar a Configuração de Aplicativos do Azure para gerenciar sinalizadores de recursos'
titleSuffix: Azure App Configuration
description: Neste tutorial, você aprenderá a gerenciar sinalizadores de recursos separadamente do aplicativo usando a Configuração de Aplicativos do Azure.
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 04/19/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: 620cd6e1712a89e13bf876310c0d3248c4a4d1e7
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75414001"
---
# <a name="tutorial-manage-feature-flags-in-azure-app-configuration"></a>Tutorial: Gerenciar sinalizadores de recurso na Configuração de Aplicativos do Azure

Você pode armazenar todos os sinalizadores de recurso na Configuração de Aplicativos do Azure e administrá-los em um único local. A Configuração de Aplicativos tem uma interface do usuário do portal chamado **Gerenciador de Recursos** que foi desenvolvido especificamente para sinalizadores de recursos. A Configuração de Aplicativos também dá suporte nativamente ao esquema de dados do sinalizador de recursos do .NET Core.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Definir e gerenciar sinalizadores de recurso na Configuração de Aplicativos.
> * Acessar sinalizadores de recurso em seu aplicativo.

## <a name="create-feature-flags"></a>Criar sinalizadores de recurso

O Gerenciador de Recursos no portal do Azure para a Configuração de Aplicativos fornece uma interface do usuário para criar e gerenciar os sinalizadores de recursos usados nos aplicativos.

Para adicionar um novo sinalizador de recurso:

1. Selecione **Gerenciador de Recursos** >  **+Adicionar** para adicionar um sinalizador de recurso.

    ![Lista de sinalizadores de recurso](./media/azure-app-configuration-feature-flags.png)

1. Insira um nome de chave exclusivo para o sinalizador de recurso. Você precisa desse nome para referenciar o sinalizador no código.

1. Se desejar, forneça uma descrição para o sinalizador de recurso.

1. Defina o estado inicial do sinalizador de recurso. Esse estado é geralmente *Desativado* ou *Ativado*. O estado *Ativado* será alterado para *Condicional* se você adicionar um filtro ao sinalizador de recurso.

    ![Criação do sinalizador de recurso](./media/azure-app-configuration-feature-flag-create.png)

1. Quando o estado for *Ativado*, selecione **+Adicionar filtro** para especificar as condições adicionais para qualificar o estado. Insira uma chave de filtro interna ou personalizada e, em seguida, selecione **+Adicionar parâmetro** para associar um ou mais parâmetros ao filtro. Os filtros internos incluem:

    | Chave | Parâmetros JSON |
    |---|---|
    | Microsoft.Percentage | {"Value": 0-100%} |
    | Microsoft.TimeWindow | {"Start": horário UTC, "End": horário UTC} |

    ![Filtro de sinalizador de recurso](./media/azure-app-configuration-feature-flag-filter.png)

## <a name="update-feature-flag-states"></a>Atualizar os estados do sinalizador de recurso

Para alterar o valor de estado de um sinalizador de recursos:

1. Selecione **Gerenciador de Recursos**.

1. À direita de um sinalizador de recursos que você deseja modificar, selecione as reticências ( **...** ) e, em seguida, selecione **Editar**.

1. Defina um novo estado para o sinalizador de recurso.

## <a name="access-feature-flags"></a>Acessar sinalizadores de recurso

Os sinalizadores de recursos criados pelo Gerenciador de Recursos são armazenados e recuperados como valores de chave normais. Eles são mantidos em um prefixo de namespace especial `.appconfig.featureflag`. Para exibir os valores de chave subjacentes, use o Explorador de Configuração. O aplicativo pode recuperar esses valores usando provedores de configuração da Configuração de Aplicativos, SDKs, extensões de linha de comando e APIs REST.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a gerenciar sinalizadores de recursos e seus estados usando a Configuração de Aplicativos. Para obter mais informações sobre o suporte de gerenciamento de recursos na Configuração de Aplicativos e no ASP.NET Core, confira o seguinte artigo:

* [Usar sinalizadores de recurso em um aplicativo ASP.NET Core](./use-feature-flags-dotnet-core.md)
