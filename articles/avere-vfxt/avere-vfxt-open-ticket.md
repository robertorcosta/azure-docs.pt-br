---
title: Como obter suporte para o Avere vFXT para Azure
description: Explicação de como abrir tíquetes de suporte do Avere vFXT para Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: b3c604003f78a150067875fb2064ab5eca0600db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252552"
---
# <a name="get-help-with-your-system"></a>Obter ajuda com o sistema

Para obter ajuda com o sistema Avere vFXT for Azure, aqui estão as maneiras de obter suporte:

* **Problema com o Avere vFXT** – use o portal do Azure para abrir um tíquete de suporte para seu Avere vFXT conforme descrito [abaixo](#open-a-support-ticket-for-your-avere-vfxt).
* **Cota** – se tiver um problema relacionado à cota, [solicite um aumento de cota](#request-a-quota-increase)
* **Documentação e exemplos** – se tiver problemas com esta documentação ou exemplos, role até a parte inferior da página com problema e use a seção de **Comentários** para pesquisar entre os problemas existentes ou registrar um novo, se necessário.

## <a name="open-a-support-ticket-for-your-avere-vfxt"></a>Abrir um tíquete de suporte para o Avere vFXT

Se tiver problemas para implantar ou usar o Avere vFXT, solicite ajuda por meio do portal do Azure.

Siga estas etapas para garantir que o tíquete de suporte seja marcado com um recurso de seu cluster. Marcar o tíquete nos ajuda a encaminhá-lo para o recurso de suporte correto.

1. De [https://portal.azure.com](https://portal.azure.com), selecione **Grupos de recursos**. Navegue até o grupo de recursos que contém o cluster vFXT onde o problema ocorreu e clique em uma das máquinas virtuais do cluster Avere.

    ![captura de tela do painel "visão geral" do grupo de recursos do portal do Azure com uma VM específica circulada](media/avere-vfxt-ticket-vm.png)

1. Na página da VM, role para baixo até a parte inferior do painel esquerdo e clique em **Nova solicitação de suporte**.

    ![Captura de tela da página da VM do portal do Azure com a VM da captura de tela anterior. O menu esquerdo é rolado para baixo e 'Nova solicitação de suporte' é circulada.](media/avere-vfxt-ticket-request.png)

1. Na primeira página da solicitação de suporte, escolha o tipo de emissão e certifique-se de que a assinatura correta seja selecionada.

   Em **Serviço,** clique em **Todos os Serviços** e procure em **Armazenamento** para escolher **Avere vFXT**.

   Adicione um resumo curto e selecione o tipo de problema.

    ![captura de tela de uma nova tela de solicitação de suporte no portal Azure. A guia Básico é selecionada. Os itens de tela incluem tipo de problema, assinatura, serviço, resumo e tipo problema.](media/ticket-basics.png)

   Clique em **Próximo** para continuar.

1. A segunda página do formulário de suporte contém sugestões para corrigir o problema com base na descrição do resumo. Clique no botão **Next** na parte inferior se você ainda precisar criar um bilhete de suporte.

   ![captura de tela da nova tela de solicitação de suporte com a guia Soluções selecionada. Um campo de texto no meio tem o título 'Solução recomendada' e explica possíveis remédios.](media/ticket-solutions.png)

1. Na terceira página, forneça detalhes - isso inclui informações sobre o seu cluster, a hora do problema ocorreu, a gravidade e como entrar em contato com você. Preencha as informações e clique no botão **Seguinte** na parte inferior.

   ![captura de tela da nova tela de solicitação de suporte com a guia Detalhes selecionado. Os campos de informações são organizados nas categorias 'Detalhes do problema', 'Método de suporte' e "Informações de contato'.](media/ticket-details.png)

1. Revise as informações na página final e clique em **Criar**. Uma confirmação e um número de tíquete serão enviados para seu endereço de email e um membro da equipe de suporte entrará em contato com você.

## <a name="request-a-quota-increase"></a>Solicitar um aumento de cota

Leia [Cota para o cluster vFXT](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster) para saber quais componentes são necessários para implantar o Avere vFXT para Azure. Você pode [solicitar um aumento de cota](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) no portal do Azure.
