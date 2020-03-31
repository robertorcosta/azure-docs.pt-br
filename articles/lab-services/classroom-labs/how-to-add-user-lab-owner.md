---
title: Como adicionar proprietários adicionais a um laboratório no Azure Lab Services
description: Este artigo mostra como um administrador pode adicionar um usuário como proprietário a um laboratório no Azure Lab Services.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77443509"
---
# <a name="how-to-add-additional-owners-to-an-existing-lab-in-azure-lab-services"></a>Como adicionar proprietários adicionais a um laboratório existente no Azure Lab Services
Este artigo mostra como você, como administrador, pode adicionar proprietários adicionais a um laboratório existente.

## <a name="add-user-to-the-reader-role-for-the-lab-account"></a>Adicionar o usuário à função de leitor para a conta de laboratório
Para adicionar um usuário como proprietário adicional a um laboratório existente, você deve primeiro dar ao usuário permissões **de leitura** na conta do laboratório.

1. Faça login no [portal Azure](https://portal.azure.com).
2. Selecione **Todos os serviços** no menu esquerdo. Procure **serviços de laboratório**e selecione-o.
3. Selecione sua conta de **laboratório** na lista. 
2. Na **página Conta**do Laboratório, selecione **Controle de acesso (IAM)** no menu esquerdo. 
2. Na página **de controle de acesso (IAM),** **selecione Adicionar** na barra de ferramentas e a **atribuição de função adicionar seleto**.

    ![Atribuição de função para a conta do laboratório ](../media/how-to-add-user-lab-owner/lab-account-access-control-page.png)
3. Na **página Adicionar uma função de função,** faça as seguintes etapas: 
    1. Selecione **Leitor** para a **função**. 
    2. Selecione o usuário. 
    3. Selecione **Salvar**. 

        ![Adicionar o usuário à função de leitor para a conta de laboratório ](../media/how-to-add-user-lab-owner/reader-lab-account.png)

## <a name="add-user-to-the-owner-role-for-the-lab"></a>Adicione o usuário à função de proprietário para o laboratório

1. De volta à página conta do **laboratório,** selecione **Todos os laboratórios** no menu esquerdo.
2. Selecione o **laboratório** ao qual deseja adicionar o usuário como proprietário. 
    
    ![Selecione o laboratório ](../media/how-to-add-user-lab-owner/select-lab.png)    
3. Na página **do Laboratório,** selecione **Controle de acesso (IAM)** no menu esquerdo.
4. Na página **de controle de acesso (IAM),** **selecione Adicionar** na barra de ferramentas e a **atribuição de função adicionar seleto**.
5. Na **página Adicionar uma função de função,** faça as seguintes etapas: 
    1. Selecione **Proprietário** para a **função**. 
    2. Selecione o usuário. 
    3. Selecione **Salvar**. 

## <a name="next-steps"></a>Próximas etapas
Confirme se o usuário vê o laboratório ao entrar no [portal de Serviços de Laboratório.](https://labs.azure.com)