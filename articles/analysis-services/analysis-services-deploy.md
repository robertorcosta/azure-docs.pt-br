---
title: Implantar no Azure Analysis Services usando o Visual Studio (SSDT) | Microsoft Docs
description: Saiba como implantar um modelo de tabela em um servidor do Azure Analysis Services usando SSDT.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 759d85db26ac7370c0b884d2e4839d3045384673
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/13/2019
ms.locfileid: "72301122"
---
# <a name="deploy-a-model-from-visual-studio"></a>Implantar um modelo no Visual Studio

Depois de criar um servidor em sua assinatura do Azure, você estará pronto para implantar nele um banco de dados de modelo de tabela. Você pode usar o SSDT (SQL Server Data Tools) para criar e implantar um projeto de modelo de tabela no qual você está trabalhando. 

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa do seguinte:

* **Servidor do Analysis Services** no Azure. Para saber mais, veja [Criar um servidor do Azure Analysis Services](analysis-services-create-server.md).
* **Projeto de modelo de tabela** no SSDT ou em um modelo de tabela existente no nível de compatibilidade 1200 ou superior. Nunca criou um? Experimente o [tutorial de modelagem de tabela de vendas na Internet do Adventure Works](https://docs.microsoft.com/analysis-services/tutorial-tabular-1400/as-adventure-works-tutorial).
* **Gateway local** - se uma ou mais fontes de dados estiverem no local na rede de sua organização, você precisará instalar um [Gateway de dados local](analysis-services-gateway.md). O gateway é necessário para que seu servidor na nuvem conecte-se às suas fontes de dados locais, a fim de processar e atualizar os dados no modelo.

> [!TIP]
> Antes de implantar, verifique se que você pode processar os dados nas tabelas. No SSDT, clique em **Modelo** > **Processo** > **Processar Tudo**. Se houver falha no processamento, você não poderá implantar com êxito.
> 
> 

## <a name="get-the-server-name"></a>Obter o nome do servidor

No **Portal do Azure** > servidor > **Visão geral** > **Nome do servidor**, copie o nome do servidor.
   
![Obter o nome do servidor no Azure](./media/analysis-services-deploy/aas-deploy-get-server-name.png)

## <a name="to-deploy-from-ssdt"></a>Para implantar de SSDT

1. No SSDT > **Gerenciador de Solução**, clique com o botão direito do mouse no projeto > **Propriedades**. Em seguida, em **Implantação** > **Servidor**, cole o nome do servidor.   
   
    ![Colar o nome do servidor na propriedade do servidor de implantação](./media/analysis-services-deploy/aas-deploy-deployment-server-property.png)
2. No **Gerenciador de Soluções**, clique com o botão direito do mouse em **Propriedades** e clique em **Implantar**. Talvez você receba uma solicitação para entrar no Azure.
   
    ![Implantar no servidor](./media/analysis-services-deploy/aas-deploy-deploy.png)
   
    O status da implantação aparecerá na janela Saída e em Implantar.
   
    ![Status da Implantação](./media/analysis-services-deploy/aas-deploy-status.png)

Isso é tudo!


## <a name="troubleshooting"></a>Solução de problemas

Se a implantação falhar durante a implantação dos metadados, provavelmente o SSDT não conseguiu se conectar ao servidor. Verifique se você consegue se conectar ao servidor usando o SSMS. Em seguida, verifique se a propriedade do Servidor de Implantação do projeto está correta.

Se a implantação falhar em uma tabela, provavelmente o servidor não conseguiu se conectar a uma fonte de dados. Se a sua fonte de dados for local, na rede da sua organização, instale um [Gateway de dados local](analysis-services-gateway.md).

## <a name="next-steps"></a>Próximas etapas

Agora que seu modelo de tabela foi implantado em seu servidor, você está pronto para se conectar a ele. Você pode [conectar-se a ele com SSMS](analysis-services-manage.md) para gerenciá-lo. E, você pode [conectar-se a ele usando uma ferramenta de cliente](analysis-services-connect.md) como o Power BI, o Power BI Desktop ou o Excel, e começar a criar relatórios.

