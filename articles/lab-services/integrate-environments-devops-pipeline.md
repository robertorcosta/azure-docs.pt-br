---
title: Integre ambientes em Pipelines Azure em Laboratórios Azure DevTest
description: Saiba como integrar os ambientes do Azure DevTest Labs em seus gasodutos de integração contínua (CI) e entrega contínua (CD) do Azure DevOps.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 3d7e481879326ac30093bd116222bddc28640398
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169419"
---
# <a name="integrate-environments-into-your-azure-devops-cicd-pipelines"></a>Integre ambientes em seus pipelines CI/CD do Azure DevOps
Você pode usar a extensão Azure DevTest Labs Tasks que está instalada no Azure DevOps Services (anteriormente conhecido como Visual Studio Team Services) para integrar facilmente seu pipeline de construção e lançamento contínuo de integração contínua (CI)/ entrega contínua (CD) com o Azure DevTest Labs. Essas extensões facilitam a implantação rápida de um [ambiente](devtest-lab-test-env.md) para uma tarefa de teste específica e, em seguida, excluí-lo quando o teste estiver concluído. 

Este artigo mostra como criar e implantar um ambiente, em seguida, excluir o ambiente, tudo em um pipeline completo. Você normalmente executaria cada uma dessas tarefas individualmente em seu próprio pipeline personalizado de implantação de teste de compilação. As extensões utilizadas neste artigo são adicionais a essas [tarefas de criar/excluir O VM dTL:](devtest-lab-integrate-ci-cd-vsts.md)

- Criar um ambiente
- Excluir um ambiente

## <a name="before-you-begin"></a>Antes de começar
Antes de integrar seu pipeline de CI/CD com o Azure DevTest Labs, instale a extensão [Azure DevTest Labs Tasks](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) do Visual Studio Marketplace. 

## <a name="create-and-configure-the-lab-for-environments"></a>Criar e configurar o laboratório para ambientes
Esta seção descreve como criar e configurar um laboratório para o qual o ambiente Azure será implantado.

1. [Crie um laboratório](devtest-lab-create-lab.md) se você ainda não tiver um. 
2. Configure o laboratório e crie um modelo de ambiente seguindo as instruções deste artigo: [Crie ambientes multi-VM e recursos PaaS com modelos do Azure Resource Manager](devtest-lab-create-environment-from-arm.md).
3. Para este exemplo, use um modelo de [https://azure.microsoft.com/resources/templates/201-web-app-redis-cache-sql-database/](https://azure.microsoft.com/resources/templates/201-web-app-redis-cache-sql-database/)inicialinicialdo Azure existente .
4. Copie a pasta **de banco de dados 201-web-app-redis-cache-sql** na pasta **ArmTemplate** no repositório configurado na etapa 2.

## <a name="create-a-release-definition"></a>Criar uma definição de versão
Para criar uma definição da versão, faça o seguinte:

1.  Na guia **Lançamentos** do **hub Build & Release**, selecione o botão sinal de mais **(+).**
2.  Na janela **Criar definição da versão**, selecione o modelo **Vazio** e selecione **Avançar**.
3.  Selecione **Escolher Depois** e selecione **Criar** para criar uma nova definição da versão com um ambiente padrão e nenhum artefato vinculado.
4.  Para abrir o menu de atalho, na nova definição de versão, selecione a **elipse (...)** ao lado do nome do ambiente e, em seguida, **selecione Configurar variáveis**.
5.  Na janela **Configurar – ambiente**, para as variáveis usadas nas tarefas de definição da versão, insira os seguintes valores:
1.  Para **administradorLogin**, digite o nome de login do administrador SQL.
2.  Para **administradorLoginPassword**, digite a senha a ser usada pelo login do Administrador SQL. Use o ícone de "cadeado" para ocultar e proteger a senha.
3.  Para **nome do banco de dados,** digite o nome do banco de dados SQL.
4.  Essas variáveis são específicas para os ambientes de exemplo, diferentes ambientes podem ter variáveis diferentes.

## <a name="create-an-environment"></a>Criar um ambiente
A próxima etapa da implantação é criar o ambiente a ser usado para fins de desenvolvimento ou teste.

1. Na definição da versão, selecione **Adicionar tarefas**.
2. Na guia **Tarefas,** adicione uma tarefa Azure DevTest Labs Create Environment( Criar laboratórios de devTest). Configure as tarefas da seguinte forma:
    1. Para **Assinatura do Azure RM**, selecione uma conexão na lista **Conexões de Serviço do Azure Disponíveis** ou crie uma conexão de permissões mais restritas para sua assinatura do Azure. Para saber mais, confira [Ponto de extremidade de serviço do Azure Resource Manager](/azure/devops/pipelines/library/service-endpoints).
2. Em **Nome do Laboratório,** selecione o nome da instância que você criou anteriormente*.
3. Para **Nome do Repositório,** selecione o repositório para onde o modelo gerenciador de recursos (201) foi empurrado para*.
4. Para **nome de modelo,** selecione o nome do ambiente que você salvou no repositório de código-fonte*. 
5. O **Nome do Laboratório**, Nome do **Repositório**e **O Nome do Modelo** são as representações amigáveis dos IDs de recursos do Azure. Digitar manualmente o nome amigável causará falhas, use as listas de parada para selecionar as informações.
6. Para **nome do ambiente,** digite um nome para identificar exclusivamente a instância do ambiente dentro do laboratório.  Deve ser único dentro do laboratório.
7. O **Arquivo parâmetro** e os **parâmetros**permitem que parâmetros personalizados sejam passados para o ambiente. Ambos podem ser usados para definir os valores dos parâmetros. Para este exemplo, a seção Parâmetros será usada. Use os nomes das variáveis definidas no ambiente, por exemplo:`-administratorLogin "$(administratorLogin)" -administratorLoginPassword "$(administratorLoginPassword)" -databaseName "$(databaseName)" -cacheSKUCapacity 1`
8. As informações dentro do modelo de ambiente podem ser passadas na seção de saída do modelo. Verifique **Criar variáveis de saída com base na saída do modelo** de ambiente para que outras tarefas possam usar os dados. `$(Reference name.Output Name)`é o padrão a seguir. Por exemplo, se o nome de referência fosse DTL e o `$(DTL.location)`nome de saída no modelo fosse localização, a variável seria .

## <a name="delete-the-environment"></a>Exclua o ambiente
O estágio final é excluir o ambiente que você implantou na instância do Azure DevTest Labs. Você normalmente excluiria o ambiente depois de executar as tarefas de dev ou executar os testes necessários nos recursos implantados.

Na definição de versão, **selecione Adicionar tarefas**e, em seguida, na guia **Implantar,** adicione uma tarefa **azure DevTest Labs Delete Environment.** Configure-o da seguinte maneira:

1. Para excluir a VM, consulte [Azure DevTest Labs Tasks](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks):
    1. Para **Assinatura do Azure RM**, selecione uma conexão na lista **Conexões de Serviço do Azure Disponíveis** ou crie uma conexão de permissões mais restritas para sua assinatura do Azure. Para saber mais, confira [Ponto de extremidade de serviço do Azure Resource Manager](/azure/devops/pipelines/library/service-endpoints).
    2. Para **nome de laboratório,** selecione o laboratório onde o ambiente existe.
    3. Para **nome do ambiente,** digite o nome do ambiente a ser removido.
2. Insira um nome para a definição da versão e, em seguida, salve-a.

## <a name="next-steps"></a>Próximas etapas
Veja os artigos a seguir: 
- [Crie ambientes multiVM com modelos do Gerenciador de recursos](devtest-lab-create-environment-from-arm.md).
- Modelos do Quickstart Resource Manager para automação do DevTest Labs a partir do [repositório DevTest Labs GitHub](https://github.com/Azure/azure-quickstart-templates).
- [Página de solução de problemas DO VSTS](/azure/devops/pipelines/troubleshooting)

