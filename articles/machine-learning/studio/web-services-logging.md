---
title: Registro de serviços web - Azure Machine Learning Studio (clássico) | Microsoft Docs
description: Saiba como ativar o registro para serviços web do Machine Learning Studio (clássico). O registro em log fornece informações adicionais para ajudar a solucionar problemas com as APIs.
services: machine-learning
author: xiaoharper
ms.custom: seodec18
ms.author: amlstudiodocs
editor: cgronlun
ms.assetid: c54d41e1-0300-46ef-bbfc-d6f7dca85086
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
ms.date: 06/15/2017
ms.openlocfilehash: 90e7692fe0e254074d8176d719d0ca9abad54a9b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79217853"
---
# <a name="enable-logging-for-azure-machine-learning-studio-classic-web-services"></a>Habilite o registro para serviços web (clássicos) do Azure Machine Learning Studio (clássico)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Este documento fornece informações sobre o recurso de registro de serviços web do Machine Learning Studio (clássico). O registro fornece informações adicionais, além de apenas um número de erro e uma mensagem, que podem ajudá-lo a solucionar suas chamadas para as APIs do Machine Learning Studio (clássico).  

## <a name="how-to-enable-logging-for-a-web-service"></a>Como habilitar o registro em log para um serviço Web

Você habilita o login no portal [DeServiços Web (clássicos) do Azure Machine Learning Studio.](https://services.azureml.net) 

1. Faça login no portal de Serviços Web do [https://services.azureml.net](https://services.azureml.net)Azure Machine Learning Studio (clássico) em . Para um serviço web clássico, você também pode chegar ao portal clicando em **New Web Services Experience** na página de Serviços Web do Estúdio de Aprendizagem de Máquina (clássico) no Studio (clássico).

   ![Novo link de Experiência dos Serviços Web](./media/web-services-logging/new-web-services-experience-link.png)

2. Na barra de menus superior, clique em **Serviços Web** para um novo serviço Web ou clique em **Serviços Web Clássicos** para um serviço Web Clássico.

   ![Selecione serviços Web Novos ou Clássicos](./media/web-services-logging/select-web-service.png)

3. Para um novo serviço Web, clique no nome de serviço Web. Para um serviço Web Clássico, clique no nome do serviço Web e clique no ponto de extremidade apropriado na próxima página.

4. Na barra de menus superior, clique em **Configurar**.

5. Defina a opção **Habilitar Log** como *Erro* (para registrar somente erros) ou *Todos* (para registro em log completo).

   ![Selecionar o nível de log](./media/web-services-logging/enable-logging.png)

6. Clique em **Salvar**.

7. Para os serviços Web Clássicos, crie o contêiner **ml-diagnostics**.

   Todos os logs de serviço Web são mantidos em um contêiner de blob denominado **ml diagnóstico** na conta de armazenamento associada ao serviço Web. Para novos serviços Web, esse contêiner é criado na primeira vez que você acessa o serviço Web. Para serviços Web Clássico, você precisa criar o contêiner, se ele ainda não existe. 

   1. No [portal do Azure](https://portal.azure.com), vá para a conta de armazenamento associada ao serviço Web.

   2. Em **Serviço Blob**, clique em **Contêineres**.

   3. Se o contêiner **ml-diagnostics** não existir, clique em **+Contêiner**, dê ao contêiner o nome "ml-diagnostics" e selecione o **Tipo de acesso** como "Blob". Clique em **OK**.

      ![Crie um novo recipiente para armazenar seus registros de diagnóstico](./media/web-services-logging/create-ml-diagnostics-container.png)

> [!TIP]
>
> Para um serviço web clássico, o Painel de Serviços Web no Machine Learning Studio (clássico) também tem um switch para ativar o registro. No entanto, como os logs são gerenciados por meio do portal de serviços Web, você precisa habilitar os logs por meio do portal, conforme descrito neste artigo. Se você já habilitou o login no Studio (clássico), então no Portal de Serviços Da Web, desative o registro e habilite-o novamente.


## <a name="the-effects-of-enabling-logging"></a>Os efeitos de habilitar o registro em log
Quando o registro é ativado, os diagnósticos e erros do ponto final do serviço web são registrados no recipiente de blob **de diagnósticos ml** na Conta de armazenamento do Azure vinculada ao espaço de trabalho do usuário. Esse contêiner armazena todas as informações de diagnóstico para todos os pontos de extremidade do serviço da Web para todos os workspaces associados a esta conta de armazenamento.

Os logs podem ser exibidos usando qualquer uma das várias ferramentas disponíveis para explorar uma Conta de Armazenamento do Azure. A maneira mais fácil possível de navegar para a conta de armazenamento no portal do Azure: clique em **Contêineres** e clique no contêiner **ml diagnostics**.  

## <a name="log-blob-detail-information"></a>Informações detalhadas do log blob
Cada blob no contêiner contém as informações de diagnóstico para um das seguintes ações:

* Uma execução do método Batch-Execution  
* Uma execução do método Request-Response  
* Inicialização de um contêiner de Request-Response

O nome de cada blob tem um prefixo da seguinte forma: 


`{Workspace Id}-{Web service Id}-{Endpoint Id}/{Log type}`


Sendo que _Tipo de log_ assume um dos seguintes valores:  

* lote  
* pontuação/solicitações  
* pontuação/init  

