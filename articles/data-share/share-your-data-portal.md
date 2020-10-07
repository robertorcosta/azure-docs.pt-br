---
title: Compartilhamento fora da sua organização (portal do Azure) – Guia de início rápido do Azure Data Share
description: Saiba como compartilhar dados com clientes e parceiros usando o Azure Data Share neste guia de início rápido.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: quickstart
ms.date: 08/19/2020
ms.openlocfilehash: 41598c04af78d4366435259357d8f897ac178942
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2020
ms.locfileid: "89489928"
---
# <a name="quickstart-share-data-using-azure-data-share-in-the-azure-portal"></a>Início Rápido: Compartilhar dados usando o Azure Data Share no portal do Azure

Neste guia de início rápido, você aprenderá a configurar um novo Azure Data Share usando o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Assinatura do Azure: Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.


## <a name="create-a-data-share-account"></a>Criar uma conta do Data Share

Crie um recurso do Azure Data Share em um grupo de recursos do Azure.

1. Entre no [portal do Azure](https://portal.azure.com/).

1. Selecione o botão **Criar um recurso** (+) no canto superior esquerdo do portal.

1. Pesquise *Data Share*.

1. Selecione **Data Share** e **Criar**.

1. Preencha os detalhes básicos de seu recurso do Azure Data Share com as informações a seguir. 

   **Configuração** | **Valor sugerido** | **Descrição do campo**
   |---|---|---|
   | Subscription | Sua assinatura | Selecione a assinatura do Azure que deseja usar para sua conta de compartilhamento de dados.|
   | Grupo de recursos | *test-resource-group* | Use um grupo de recursos existente ou crie um novo. |
   | Localização | *Leste dos EUA 2* | Selecione uma região para sua conta de compartilhamento de dados.
   | Nome | *datashareaccount* | Especifique um nome para sua conta de compartilhamento de dados. |

1. Selecione **Examinar + criar** e **Criar** para provisionar sua conta de compartilhamento de dados. O provisionamento de uma nova conta de compartilhamento de dados normalmente leva cerca de 2 minutos ou menos.

1. Depois que a implantação estiver concluída, selecione **Ir para o recurso**.

## <a name="create-a-share"></a>Criar um compartilhamento

1. Navegue até sua página de Visão Geral do Data Share.

   ![Compartilhar seus dados](./media/share-receive-data.png "Compartilhar seus dados") 

1. Selecione **Começar a compartilhar seus dados**.

1. Selecione **Criar**.

1. Preencha os detalhes do seu compartilhamento. Especifique um nome, um tipo de compartilhamento, uma descrição do conteúdo de compartilhamento e os termos de uso (opcional). 

   ![EnterShareDetails](./media/enter-share-details.png "Inserir detalhes do Compartilhamento") 

1. Selecione **Continuar**.

1. Para adicionar conjuntos de dados ao compartilhamento, selecione **Adicionar Conjuntos de Dados**. 

   ![Adicionar conjuntos de dados ao compartilhamento](./media/datasets.png "Conjunto de dados")

1. Selecione o tipo de conjunto de dados que você deseja adicionar. Você verá uma lista diferente de tipos de conjuntos de dados, dependendo do tipo de compartilhamento (instantâneo ou in-loco) que você selecionou na etapa anterior. Se estiver compartilhando de um Banco de Dados SQL do Azure ou do Azure Synapse Analytics, você precisará fornecer credenciais do SQL. Autentique usando o usuário criado como parte dos pré-requisitos.

   ![AddDatasets](./media/add-datasets.png "Adicionar conjuntos de dados")    

1. Navegue até o objeto que você gostaria de compartilhar e selecione "Adicionar Conjuntos de Dados". 

   ![SelectDatasets](./media/select-datasets.png "Selecionar conjuntos de dados")    

1. Na guia Destinatários, insira os endereços de email de seu Consumidor de Dados selecionando "+ Adicionar Destinatário".

   ![AddRecipients](./media/add-recipient.png "Adicionar destinatários") 

1. Selecione **Continuar**.

1. Se você tiver selecionado o tipo de compartilhamento de instantâneo, poderá configurar a agenda de instantâneo para fornecer atualizações de seus dados para o consumidor de dados. 

   ![EnableSnapshots](./media/enable-snapshots.png "Habilitar instantâneos") 

1. Selecione uma hora de início e um intervalo de recorrência. 

1. Selecione **Continuar**.

1. Na guia Examinar + Criar, examine o Conteúdo do Pacote, Configurações, Destinatários e Configurações de Sincronização. Selecione **Criar**.

O Azure Data Share agora foi criado e o destinatário de seu Compartilhamento de Dados agora está pronto para aceitar seu convite.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando o recurso não for mais necessário, acesse a página **Visão Geral do Data Share** e selecione **Excluir** para removê-lo.

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você aprendeu a criar um Azure Data Share. Para saber mais sobre como um Consumidor de Dados pode aceitar e receber um compartilhamento de dados, prossiga para o tutorial [Aceitar e receber dados](subscribe-to-data-share.md). 
