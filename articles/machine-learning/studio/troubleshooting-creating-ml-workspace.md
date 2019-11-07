---
title: Solucionando problemas de um espaço de trabalho
titleSuffix: ML Studio (classic) Azure
description: Este guia fornece soluções para alguns desafios frequentemente encontrados quando você está configurando a versão clássica dos espaços de trabalho do Azure Machine Learning Studio.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/20/2017
ms.openlocfilehash: 2f305f74a4bbf15fdda9a8801e73bda70fab6deb
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73622116"
---
# <a name="troubleshooting-guide-create-and-connect-to-an-azure-machine-learning-studio-classic-workspace"></a>Guia de solução de problemas: criar e conectar-se a um espaço de trabalho Azure Machine Learning Studio (clássico)
Este guia fornece soluções para alguns desafios frequentemente encontrados quando você está configurando espaços de trabalho do Azure Machine Learning Studio (clássico).



## <a name="workspace-owner"></a>Proprietário do workspace
Para abrir um espaço de trabalho no Machine Learning Studio (clássico), você deve estar conectado à conta da Microsoft usada para criar o espaço de trabalho ou você precisa receber um convite do proprietário para ingressar no espaço de trabalho. No portal do Azure você pode gerenciar o workspace, que inclui a capacidade de alterar o proprietário e configurar o acesso.

Para obter mais informações sobre como gerenciar um espaço de trabalho, consulte [gerenciar um espaço de trabalho Azure Machine Learning Studio (clássico)].

[Gerenciar um espaço de trabalho Azure Machine Learning Studio (clássico)]: manage-workspace.md

## <a name="allowed-regions"></a>Regiões permitidas
No momento, o Machine Learning está disponível em um número limitado de regiões. Se sua assinatura não incluir uma dessas regiões, talvez você receba a mensagem de erro "Você não tem assinaturas nas regiões permitidas".

Para solicitar a adição de uma região à sua assinatura, crie uma nova solicitação de suporte da Microsoft no portal do Azure, escolha o tipo de problema **Cobrança** e siga os prompts para enviar sua solicitação.

## <a name="storage-account"></a>Conta de armazenamento
O serviço de Machine Learning precisa de uma conta de armazenamento para armazenar dados. Você pode usar uma conta de armazenamento existente ou pode criar uma nova conta de armazenamento ao criar o novo espaço de trabalho Machine Learning Studio (clássico) (se você tiver uma cota para criar uma nova conta de armazenamento).

Depois que o novo espaço de trabalho de Machine Learning Studio (clássico) for criado, você poderá entrar no Machine Learning Studio (clássico) usando o conta Microsoft usado para criar o espaço de trabalho. Se você encontrar a mensagem de erro "Workspace Não Encontrado" (semelhante à captura de tela a seguir), use as etapas a seguir para excluir os cookies do navegador.

![Workspace não encontrado](media/troubleshooting-creating-ml-workspace/screen3.png)

**Para excluir cookies do navegador**

1. Se você usa o Internet Explorer, clique no botão **Ferramentas** no canto superior direito e selecione **Opções da Internet**.  

   ![Opções da Internet](media/troubleshooting-creating-ml-workspace/screen4.png)

2. Na guia **Geral**, clique em **Excluir…**

   ![Guia Geral](media/troubleshooting-creating-ml-workspace/screen5.png)

3. Na caixa de diálogo **Excluir Histórico de Navegação**, selecione **Cookies e dados de sites** e clique em **Excluir**.

   ![Excluir cookies](media/troubleshooting-creating-ml-workspace/screen6.png)

Depois que os cookies forem excluídos, reinicie o navegador e vá para a página [Microsoft Azure Machine Learning Studio (clássica)](https://studio.azureml.net) . Quando forem solicitados nome de usuário e senha, insira os dados da mesma conta da Microsoft usada para criar o workspace.

## <a name="comments"></a>Comentários

Nosso objetivo é tornar a experiência do Machine Learning o mais simples possível. Poste comentários e problemas no [Fórum do Azure Machine Learning](https://social.msdn.microsoft.com/Forums/windowsazure/home?forum=MachineLearning) para nos ajudar a atendê-lo melhor.
