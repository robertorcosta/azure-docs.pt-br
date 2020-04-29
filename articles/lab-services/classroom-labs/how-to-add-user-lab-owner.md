---
title: Como adicionar proprietários adicionais a um laboratório no Azure Lab Services
description: Este artigo mostra como um administrador pode adicionar um usuário como um proprietário a um laboratório no Azure Lab Services.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2020
ms.author: spelluru
ms.openlocfilehash: 9302cadf0b900c9faac382bad86b51cea7d828fb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "77443509"
---
# <a name="how-to-add-additional-owners-to-an-existing-lab-in-azure-lab-services"></a>Como adicionar proprietários adicionais a um laboratório existente no Azure Lab Services
Este artigo mostra como você, como administrador, pode adicionar outros proprietários a um laboratório existente.

## <a name="add-user-to-the-reader-role-for-the-lab-account"></a>Adicionar usuário à função leitor da conta do laboratório
Para adicionar um usuário como um proprietário adicional a um laboratório existente, primeiro você deve conceder permissões de **leitura** ao usuário na conta do laboratório.

1. Entre no [portal do Azure](https://portal.azure.com).
2. Selecione **Todos os serviços** no menu esquerdo. Procure **serviços de laboratório**e, em seguida, selecione-o.
3. Selecione sua **conta de laboratório** na lista. 
2. Na **página conta do laboratório**, selecione **controle de acesso (iam)** no menu à esquerda. 
2. Na página **controle de acesso (iam)** , selecione **Adicionar** na barra de ferramentas e selecione **Adicionar atribuição de função**.

    ![Atribuição de função para a conta do laboratório ](../media/how-to-add-user-lab-owner/lab-account-access-control-page.png)
3. Na página **Adicionar uma atribuição de função** , execute as seguintes etapas: 
    1. Selecione **leitor** para a **função**. 
    2. Selecione o usuário. 
    3. Selecione **Salvar**. 

        ![Adicionar usuário à função leitor da conta do laboratório ](../media/how-to-add-user-lab-owner/reader-lab-account.png)

## <a name="add-user-to-the-owner-role-for-the-lab"></a>Adicionar usuário à função de proprietário do laboratório

1. De volta à página **conta do laboratório** , selecione **todos os laboratórios** no menu à esquerda.
2. Selecione o **laboratório** ao qual você deseja adicionar o usuário como um proprietário. 
    
    ![Selecione o laboratório ](../media/how-to-add-user-lab-owner/select-lab.png)    
3. Na página do **laboratório** , selecione **controle de acesso (iam)** no menu à esquerda.
4. Na página **controle de acesso (iam)** , selecione **Adicionar** na barra de ferramentas e selecione **Adicionar atribuição de função**.
5. Na página **Adicionar uma atribuição de função** , execute as seguintes etapas: 
    1. Selecione **proprietário** para a **função**. 
    2. Selecione o usuário. 
    3. Selecione **Salvar**. 

## <a name="next-steps"></a>Próximas etapas
Confirme se o usuário vê o laboratório após fazer logon no [portal de serviços de laboratório](https://labs.azure.com).