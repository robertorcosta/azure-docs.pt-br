---
title: Implantar um modelo para os Serviços de Análise do Azure usando o Visual Studio | Microsoft Docs
description: Aprenda a implantar um modelo tabular em um servidor do Azure Analysis Services usando o Visual Studio.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 71b3b7815d2a4b0b4de3afdca9db93156f505445
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73572891"
---
# <a name="deploy-a-model-from-visual-studio"></a>Implantar um modelo no Visual Studio

Depois de criar um servidor em sua assinatura do Azure, você estará pronto para implantar nele um banco de dados de modelo de tabela. Você pode usar o Visual Studio com projetos de Serviços de Análise para construir e implantar um projeto de modelo tabular em que você está trabalhando. 

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa do seguinte:

* **Servidor do Analysis Services** no Azure. Para saber mais, veja [Criar um servidor do Azure Analysis Services](analysis-services-create-server.md).
* **Projeto de modelo tabular** no Visual Studio ou um modelo tabular existente no nível de compatibilidade de 1200 ou superior. Nunca criou um? Experimente o [tutorial de modelagem de tabela de vendas na Internet do Adventure Works](https://docs.microsoft.com/analysis-services/tutorial-tabular-1400/as-adventure-works-tutorial).
* **Gateway local** - se uma ou mais fontes de dados estiverem no local na rede de sua organização, você precisará instalar um [Gateway de dados local](analysis-services-gateway.md). O gateway é necessário para que seu servidor na nuvem conecte-se às suas fontes de dados locais, a fim de processar e atualizar os dados no modelo.

> [!TIP]
> Antes de implantar, verifique se que você pode processar os dados nas tabelas. No Visual Studio, clique **em Model** > **Process** > Process**All**. Se houver falha no processamento, você não poderá implantar com êxito.
> 
> 

## <a name="get-the-server-name"></a>Obter o nome do servidor

No **Portal do Azure** > servidor > **Visão geral** > **Nome do servidor**, copie o nome do servidor.
   
![Obter o nome do servidor no Azure](./media/analysis-services-deploy/aas-deploy-get-server-name.png)

## <a name="to-deploy-from-visual-studio"></a>Para implantar no Visual Studio

1. No Visual Studio > **Solution Explorer**, clique com o botão direito do mouse no projeto > **Properties**. Em **seguida,** > no**Servidor** de implantação cole o nome do servidor.   
   
    ![Colar o nome do servidor na propriedade do servidor de implantação](./media/analysis-services-deploy/aas-deploy-deployment-server-property.png)
2. No **Gerenciador de Soluções**, clique com o botão direito do mouse em **Propriedades** e clique em **Implantar**. Talvez você receba uma solicitação para entrar no Azure.
   
    ![Implantar no servidor](./media/analysis-services-deploy/aas-deploy-deploy.png)
   
    O status da implantação aparecerá na janela Saída e em Implantar.
   
    ![Status da Implantação](./media/analysis-services-deploy/aas-deploy-status.png)

Isso é tudo!


## <a name="troubleshooting"></a>Solução de problemas

Se a implantação falhar ao implantar metadados, é provável que o Visual Studio não pudesse se conectar ao servidor. Verifique se você consegue se conectar ao servidor usando o SSMS. Em seguida, verifique se a propriedade do Servidor de Implantação do projeto está correta.

Se a implantação falhar em uma tabela, provavelmente o servidor não conseguiu se conectar a uma fonte de dados. Se a sua fonte de dados for local, na rede da sua organização, instale um [Gateway de dados local](analysis-services-gateway.md).

## <a name="next-steps"></a>Próximas etapas

Agora que seu modelo de tabela foi implantado em seu servidor, você está pronto para se conectar a ele. Você pode [se conectar a ele com o SQL Server Management Studio (SSMS)](analysis-services-manage.md) para gerenciá-lo. E, você pode [conectar-se a ele usando uma ferramenta de cliente](analysis-services-connect.md) como o Power BI, o Power BI Desktop ou o Excel, e começar a criar relatórios.

