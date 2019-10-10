---
title: Como obter suporte para o Avere vFXT para Azure
description: Explicação de como abrir tíquetes de suporte do Avere vFXT para Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: rohogue
ms.openlocfilehash: 068747fd51779bd8a20636355a9ce2b032f6151d
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72255369"
---
# <a name="get-help-with-your-system"></a>Obter ajuda com o sistema

Se precisar de ajuda com o Avere vFXT para Azure, estas serão as diferentes maneiras de obter suporte:

* **Problema com o Avere vFXT** – use o portal do Azure para abrir um tíquete de suporte para seu Avere vFXT conforme descrito [abaixo](#open-a-support-ticket-for-your-avere-vfxt).
* **Cota** – se tiver um problema relacionado à cota, [solicite um aumento de cota](#request-a-quota-increase)
* **Documentação e exemplos** – se tiver problemas com esta documentação ou exemplos, role até a parte inferior da página com problema e use a seção de **Comentários** para pesquisar entre os problemas existentes ou registrar um novo, se necessário.  

## <a name="open-a-support-ticket-for-your-avere-vfxt"></a>Abrir um tíquete de suporte para o Avere vFXT

Se tiver problemas para implantar ou usar o Avere vFXT, solicite ajuda por meio do portal do Azure.  

Siga estas etapas para garantir que o tíquete de suporte seja marcado com um recurso de seu cluster. Marcar o tíquete nos ajuda a encaminhá-lo para o recurso de suporte correto. 

1. Em [https://portal.azure.com](https://portal.azure.com), selecione **Grupos de Recursos**.

   ![captura de tela do menu esquerdo do portal do Azure com "Grupos de recursos" circulado](media/avere-vfxt-ticket-rg.png)

1. Navegue até o grupo de recursos que contém o cluster vFXT em que ocorreu o problema e clique em uma das máquinas virtuais do Avere.

    ![captura de tela do painel "visão geral" do grupo de recursos do portal do Azure com uma VM específica circulada](media/avere-vfxt-ticket-vm.png)

1. Na página da VM, role para baixo até a parte inferior do painel esquerdo e clique em **Nova solicitação de suporte**.

    ![Captura de tela da página da VM do portal do Azure com a VM da captura de tela anterior. O menu à esquerda foi rolado até a parte inferior e "Nova solicitação de suporte" foi circulado.](media/avere-vfxt-ticket-request.png)

1. Na primeira página da solicitação de suporte, clique em **Todos os Serviços** e, em **Armazenamento**, escolha **Avere vFXT**.

    ![captura da tela da nova solicitação de suporte no portal do Azure com o cabeçalho "Básico" e um círculo ao redor do item "Serviço". O botão "Todos os serviços" está selecionado e o campo do menu suspenso tem o valor "Avere vFXT"](media/avere-vfxt-ticket-service.png)

1. Na segunda página, escolha o tipo de problema e a categoria que melhor correspondem ao seu problema. Adicione um título curto e uma descrição que inclua a hora em que o problema ocorreu. 

   ![captura da tela da nova solicitação de suporte com o cabeçalho "Problema", que contém vários campos que precisam ser preenchidos](media/avere-vfxt-ticket-problem.png)

1. Na terceira página, preencha suas informações de contato e clique em **Criar**. Uma confirmação e um número de tíquete serão enviados para seu endereço de email e um membro da equipe de suporte entrará em contato com você.

## <a name="request-a-quota-increase"></a>Solicitar um aumento de cota

Leia [Cota para o cluster vFXT](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster) para saber quais componentes são necessários para implantar o Avere vFXT para Azure. Você pode [solicitar um aumento de cota](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) no portal do Azure.