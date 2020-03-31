---
title: Adicionar um repositório Git a um laboratório no Azure DevTest Labs | Microsoft Docs
description: Saiba como adicionar um repositório Git do GitHub ou do Azure DevOps Services à sua fonte de artefatos personalizados no Azure DevTest Labs.
services: devtest-lab,virtual-machines,visual-studio-online
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: 01b459f7-eaf2-45a8-b4b5-2c0a821b33c8
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 1e7587c60e180fb35e1a2bed735b053b6b0c388a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294602"
---
# <a name="add-a-git-repository-to-store-custom-artifacts-and-resource-manager-templates"></a>Adicionar um repositório Git para armazenar os artefatos personalizados e modelos do Resource Manager

Você pode [criar artefatos personalizados](devtest-lab-artifact-author.md) para as VMs no laboratório ou [usar modelos do Azure Resource Manager para criar um ambiente de teste personalizado](devtest-lab-create-environment-from-arm.md). Você deve adicionar um repositório Git particular para os artefatos ou modelos do Resource Manager que sua equipe cria. O repositório pode ser hospedado no [GitHub](https://github.com) ou no [Azure DevOps Services](https://visualstudio.com).

Nós oferecemos um [repositório GitHub de artefatos](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) que você pode implantar no estado em que se encontram ou personalizá-los para seus laboratórios. Quando você personaliza ou cria um artefato, você não pode armazenar o artefato no repositório público. Você deverá criar seu próprio repositório privado para artefatos personalizados e artefatos que você criar. 

Quando você cria uma VM, você pode salvar o modelo do Resource Manager, personalizá-lo, caso queira, e usá-lo posteriormente para criar mais VMs. Você deve criar seu próprio repositório particular para armazenar seus modelos personalizados do Resource Manager.  

* Para saber como criar um repositório no GitHub, confira o [GitHub Bootcamp](https://help.github.com/categories/bootcamp/).
* Para saber como criar um projeto azure DevOps Services que tenha um repositório Git, consulte [Conecte-se aos Serviços DevOps do Azure](https://www.visualstudio.com/get-started/setup/connect-to-visual-studio-online).

A figura a seguir mostra um exemplo da aparência de um repositório que tem artefatos no GitHub:  

![Repositório de artefatos de exemplo no GitHub](./media/devtest-lab-add-repo/devtestlab-github-artifact-repo-home.png)

## <a name="get-the-repository-information-and-credentials"></a>Obter as credenciais e informações de repositório
Para adicionar um repositório de artefatos ao laboratório, você deve primeiro obter as informações de chave do seu repositório. As seções a seguir descrevem como obter essas informações para repositórios hospedados no GitHub ou no Azure DevOps Services.

### <a name="get-the-github-repository-clone-url-and-personal-access-token"></a>Obter a URL de clone do repositório GitHub e token de acesso pessoal

1. Vá para a página inicial do repositório GitHub que contém o artefato ou definições de modelo do Resource Manager.
2. Selecione **Clonar ou baixar**.
3. Para copiar a URL para a área de transferência, selecione o botão **URL de clone HTTPS**. Salve a URL para uso posterior.
4. Selecione a imagem de perfil no canto superior direito do GitHub e, em seguida, selecione **Configurações**.
5. No menu **Configurações pessoais** à esquerda, selecione **Tokens de acesso pessoal**.
6. Selecione **Gerar novo token**.
7. No **Novo token de acesso pessoal**, insira uma **Descrição do token**. Aceite os itens padrão em **Selecionar escopos** e, em seguida, selecione **Gerar Token**.
8. Salve o token gerado. Você usará o token posteriormente.
9. Feche o GitHub.   
10. Continue para a seção [Conectar seu laboratório ao repositório](#connect-your-lab-to-the-repository).

### <a name="get-the-azure-repos-clone-url-and-personal-access-token"></a>Obter a URL de clone do Azure Repos e o token de acesso pessoal

1. Vá para a página inicial da coleção `https://contoso-web-team.visualstudio.com`de sua equipe (por exemplo, ) e, em seguida, selecione seu projeto.
2. Na home page do projeto, selecione **Código**.
3. Para exibir a URL de clone, na página **Código** do projeto, selecione **Clone**.
4. Salve a URL. Você usará a URL posteriormente.
5. Para criar um token de acesso pessoal, selecione **Meu perfil** no menu suspenso da conta de usuário.
6. Na página de informações de perfil, selecione **Segurança**.
7. Na guia **Segurança**, selecione **Adicionar**.
8. Na página **Criar um token de acesso pessoal**:
   1. Insira uma **Descrição** para o token.
   2. Na lista **Expira em**, selecione **180 dias**.
   3. Na lista **Contas**, selecione **Todas as contas acessíveis**.
   4. Selecione a **opção Somente leitura.**
   5. Selecione **Criar Token**.
9. O novo token será exibido na lista de **Tokens de Acesso Pessoal**. Selecione **Copiar Token**e salve o valor do token para uso posterior.
10. Continue para a seção [Conectar seu laboratório ao repositório](#connect-your-lab-to-the-repository).

## <a name="connect-your-lab-to-the-repository"></a>Conecte seu laboratório ao repositório
1. Faça login no [portal Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Selecione **Mais Serviços** e, em seguida, selecione **DevTest Labs** na lista de serviços.
3. Na lista de laboratórios, selecione o laboratório. 
4. Selecione **Configuração e políticas** > **Repositórios** > **+ Adicionar**.

    ![O botão Adicionar repositório](./media/devtest-lab-add-repo/devtestlab-add-repo.png)
5. Na segunda página **Repositórios**, especifique as seguintes informações:
   1. **Nome**. Insira um nome para o repositório.
   2. **URL de clone do Git**. Insira a URL HTTPS de clone de Git que você copiou anteriormente do GitHub ou do Azure DevOps Services.
   3. **Ramo.** Insira a ramificação para obter as suas definições.
   4. **Token de acesso pessoal**. Insira o token de acesso pessoal obtido anteriormente do GitHub ou do Azure DevOps Services.
   5. **Caminhos de pasta**. Insira, pelo menos, um caminho de pasta em relação a URL de clone que contém o artefato ou definições de modelo do Resource Manager. Ao especificar um subdiretório, verifique se você incluiu a barra "/" no caminho da pasta.

      ![Área de repositórios](./media/devtest-lab-add-repo/devtestlab-repo-blade.png)
6. Selecione **Salvar**.

### <a name="related-blog-posts"></a>Postagens de blogs relacionadas
* [Solucionando artefatos com falha no DevTest Labs](devtest-lab-troubleshoot-artifact-failure.md)
* [Ingresse uma VM em um domínio do Active Directory existente usando um modelo do Resource Manager no DevTest Labs](https://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Próximas etapas
Depois que você criou seu repositório Git, você pode fazer um ou ambos estes procedimentos, dependendo das suas necessidades:
* Armazene seus [artefatos personalizados](devtest-lab-artifact-author.md). Você pode usá-los posteriormente para criar novas VMs.
* [Crie ambientes de várias VMs e recursos de PaaS usando modelos do Resource Manager](devtest-lab-create-environment-from-arm.md). Em seguida, você pode armazenar os modelos em seu repositório privado.

Quando você criar uma VM, poderá verificar se os artefatos ou os modelos são adicionados ao seu repositório Git. Eles estão imediatamente disponíveis na lista de artefatos ou modelos. O nome do seu repositório privado é mostrado na coluna que especifica a origem. 
