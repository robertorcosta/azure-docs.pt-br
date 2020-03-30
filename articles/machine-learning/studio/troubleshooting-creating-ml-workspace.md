---
title: Solução de problemas de um espaço de trabalho
titleSuffix: ML Studio (classic) - Azure
description: Este guia fornece soluções para alguns desafios freqüentemente encontrados quando você está configurando espaços de trabalho (clássicos) do Azure Machine Learning Studio.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/20/2017
ms.openlocfilehash: 58ccd63e16382aca8e16eb67efba951a055eb254
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79217830"
---
# <a name="troubleshooting-guide-create-and-connect-to-an-azure-machine-learning-studio-classic-workspace"></a>Guia de solução de problemas: Crie e conecte-se a um espaço de trabalho (clássico) do Azure Machine Learning Studio

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Este guia fornece soluções para alguns desafios freqüentemente encontrados quando você está configurando espaços de trabalho (clássicos) do Azure Machine Learning Studio.

## <a name="workspace-owner"></a>Proprietário do workspace
Para abrir um espaço de trabalho no Machine Learning Studio (clássico), você deve estar conectado à Conta Microsoft que você usou para criar o espaço de trabalho, ou você precisa receber um convite do proprietário para participar do espaço de trabalho. No portal do Azure você pode gerenciar o workspace, que inclui a capacidade de alterar o proprietário e configurar o acesso.

Para obter mais informações sobre como gerenciar um espaço de trabalho, consulte [Gerenciar um espaço de trabalho (clássico) do Azure Machine Learning Studio].

[Gerencie um espaço de trabalho azure Machine Learning Studio (clássico)]: manage-workspace.md

## <a name="allowed-regions"></a>Regiões permitidas
No momento, o Machine Learning está disponível em um número limitado de regiões. Se sua assinatura não incluir uma dessas regiões, você poderá ver a mensagem de erro: "Você não tem assinaturas nas regiões permitidas.".

Para solicitar a adição de uma região à sua assinatura, crie uma nova solicitação de suporte da Microsoft no portal do Azure, escolha o tipo de problema **Cobrança** e siga os prompts para enviar sua solicitação.

## <a name="storage-account"></a>Conta de armazenamento
O serviço de Machine Learning precisa de uma conta de armazenamento para armazenar dados. Você pode usar uma conta de armazenamento existente ou criar uma nova conta de armazenamento quando criar o novo espaço de trabalho do Machine Learning Studio (clássico) (se você tiver cota para criar uma nova conta de armazenamento).

Depois que o novo espaço de trabalho do Machine Learning Studio (clássico) for criado, você pode entrar no Machine Learning Studio (clássico) usando a conta microsoft que você usou para criar o espaço de trabalho. Se você encontrar a mensagem de erro , "Workspace Not Found" (semelhante à captura de tela a seguir), use as seguintes etapas para excluir os cookies do seu navegador.

![Workspace não encontrado](media/troubleshooting-creating-ml-workspace/screen3.png)

**Para excluir cookies do navegador**

1. Se você usa o Internet Explorer, clique no botão **Ferramentas** no canto superior direito e selecione **Opções da Internet**.  

   ![Opções da Internet](media/troubleshooting-creating-ml-workspace/screen4.png)

2. Na guia **Geral**, clique em **Excluir…**

   ![Guia Geral](media/troubleshooting-creating-ml-workspace/screen5.png)

3. Na caixa de diálogo **Excluir Histórico de Navegação**, selecione **Cookies e dados de sites** e clique em **Excluir**.

   ![Excluir cookies](media/troubleshooting-creating-ml-workspace/screen6.png)

Depois que os cookies forem excluídos, reinicie o navegador e vá para a página [do Microsoft Azure Machine Learning Studio (clássico).](https://studio.azureml.net) Quando forem solicitados nome de usuário e senha, insira os dados da mesma conta da Microsoft usada para criar o workspace.

## <a name="comments"></a>Comentários

Nosso objetivo é tornar a experiência do Machine Learning o mais simples possível. Poste comentários e problemas no [Fórum do Azure Machine Learning](https://social.msdn.microsoft.com/Forums/windowsazure/home?forum=MachineLearning) para nos ajudar a atendê-lo melhor.
