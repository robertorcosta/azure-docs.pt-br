---
title: Implantar um conjunto de escala de máquina virtual usando o Visual Studio
description: Implantar um conjunto de escala de máquina virtual usando o Visual Studio e um modelo do Resource Manager
ms.custom: vs-azure, H1Hack27Feb2017
ms.workload: azure-vs
author: mayanknayar
tags: azure-resource-manager
ms.assetid: ed0786b8-34b2-49a8-85b5-2a628128ead6
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: manayar
ms.openlocfilehash: c49b4f42bc726c68880bdd4d6f58956936e83177
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066968"
---
# <a name="how-to-create-a-virtual-machine-scale-set-with-visual-studio"></a>Como criar um Conjunto de Dimensionamento de Máquinas Virtuais com o Visual Studio

Este artigo mostra como implantar um Conjunto de Escala de Máquina Virtual do Azure usando uma implantação do Visual Studio Resource Group.

[O Azure Virtual Machine Scale Sets](https://azure.microsoft.com/blog/azure-vm-scale-sets-public-preview/) é um recurso do Azure Compute para implantar e gerenciar uma coleção de máquinas virtuais semelhantes com balanceamento automático e de carga. É possível provisionar e implantar os Conjuntos de Dimensionamento de Máquinas Virtuais usando os [Modelos do Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates). Os modelos do Azure Resource Manager podem ser implantados usando o Azure CLI, PowerShell, REST e também diretamente do Visual Studio. O Visual Studio fornece um conjunto de modelos de exemplo, que você pode implantar como parte de um projeto de implantação do Azure Resource Group.

As implantações de Grupo de Recursos do Azure são uma maneira de agrupar e publicar um conjunto de recursos relacionados do Azure em uma única operação de implantação. Para obter mais informações, consulte [Criar e implantar grupos de recursos do Azure através do Visual Studio](../vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar a implantar conjuntos de escala de máquinas virtuais no Visual Studio, você precisa dos seguintes pré-requisitos:

* Visual Studio 2013 ou posterior.
* SDK do Azure 2.7, 2.8 ou 2.9

>[!NOTE]
>Este artigo usa o Visual Studio 2019 com [o Azure SDK 2.8](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/).

## <a name="create-a-project"></a>Criar um projeto<a name="creating-a-project"></a> 

1. Abra o Visual Studio e selecione **Criar um projeto**.

1. Em **Criar um novo projeto,** escolha o Grupo de Recursos Do **Azure** para C# e, em seguida, selecione **Next**.

1. Em **Configurar seu novo projeto,** digite um nome e selecione **Criar**.

    ![Nomeie e crie seu projeto](media/virtual-machine-scale-sets-vs-create/configure-azure-resource-group.png)

1. Na lista de modelos, escolha o conjunto de **escalade máquina virtual** do Windows ou o modelo **de set de escala de máquina virtual do Linux.** Selecione **OK**.

   ![Selecione um modelo de máquina virtual](media/virtual-machine-scale-sets-vs-create/select-vm-template.png)

Depois de criar seu projeto, **o Solution Explorer** contém um script de implantação do PowerShell, um modelo do Azure Resource Manager e um arquivo de parâmetros para o Conjunto de Escala de Máquina Virtual.

## <a name="customize-your-project"></a>Personalizar seu projeto

Agora você pode editar o modelo para personalizá-lo para as necessidades do seu aplicativo. Você pode adicionar propriedades de extensão de máquina virtual ou editar regras de balanceamento de carga. Por padrão, os modelos de conjunto de escala de máquina virtual são configurados para implantar a extensão **AzureDiagnostics,** o que facilita a adição de regras de escala automática. Os modelos também implantam um balanceador de carga com um endereço IP público, configurado com regras NAT de entrada.

O balanceador de carga permite que você se conecte às instâncias da máquina virtual com SSH (Linux) ou RDP (Windows). O intervalo de portas de front-end começa em 50000. Para linux, se você SSH portar 50000, carregue rotas de balanceamento para porta 22 da primeira máquina virtual no Conjunto de Escala. A conexão à porta 50001 é encaminhada para a porta 22 da segunda máquina virtual, e assim por diante.

 Uma boa maneira de editar seus modelos com o Visual Studio é usar o **Contorno JSON**. Você pode organizar os parâmetros, variáveis e recursos. Com a compreensão do esquema, o Visual Studio pode apontar erros no seu modelo antes de implantá-lo.

![Gerenciador JSON](media/virtual-machine-scale-sets-vs-create/json-explorer.png)

## <a name="deploy-the-project"></a>Implantar o projeto

Implante o modelo do Azure Resource Manager para criar o recurso Virtual Machine Scale Set:

1. No **Solution Explorer,** clique com o botão direito do mouse no projeto e escolha **Implantar** > **novo**.

    ![Implante o seu projeto](media/virtual-machine-scale-sets-vs-create/deploy-new-project.png)

1. Em **Implantar para grupo de recursos,** escolha qual assinatura usar e selecione um grupo de recursos. Você pode criar um grupo de recursos, se necessário.

1. Em seguida, **selecione Editar parâmetros** para inserir parâmetros que são passados para o seu modelo.

   ![Insira o grupo de assinatura e recursos](media/virtual-machine-scale-sets-vs-create/deploy-to-resource-group.png)

1. Forneça o nome de usuário e a senha do sistema operacional. Esses valores são necessários para criar a implantação. Se você não tiver as ferramentas PowerShell para visual studio **instaladas, selecione Salvar senhas** para evitar um prompt de comando PowerShell oculto ou use [o suporte ao Key Vault](https://azure.microsoft.com/blog/keyvault-support-for-arm-templates/). Selecione **Salvar** para continuar.

    ![Editar parâmetros de implantação](media/virtual-machine-scale-sets-vs-create/edit-deployment-parameters.png)

1. Em **Implantar em Grupo de recursos,** **selecione Implantar**. A ação executa o script **Deploy-AzureResourceGroup.ps1.** A janela **Saída** mostra o progresso da implantação.

   ![Produção mostra resultados](media/virtual-machine-scale-sets-vs-create/deployment-output.png)

## <a name="explore-your-virtual-machine-scale-set"></a>Explore seu conjunto de escalas de máquina virtual<a name="exploring-your-virtual-machine-scale-set"></a>

Selecione **Exibir** > **o Cloud Explorer** para visualizar o novo conjunto de escala de máquina virtual. Use **Atualizar tudo,** se necessário.

![Gerenciador de Nuvem](media/virtual-machine-scale-sets-vs-create/cloud-explorer.png)

**O Cloud Explorer** permite gerenciar os recursos do Azure no Visual Studio enquanto desenvolve aplicativos. Você também pode exibir o Conjunto de Dimensionamento de Máquinas Virtuais no [portal do Azure](https://portal.azure.com) e no [Gerenciador de Recursos do Azure](https://resources.azure.com/).

 O portal fornece a melhor maneira de gerenciar sua infra-estrutura Do Azure com um navegador web. O Azure Resource Explorer fornece uma maneira fácil de explorar e depurar recursos do Azure. O Azure Resource Explorer oferece a exibição de instâncias e também mostra os comandos powershell para os recursos que você está olhando.

## <a name="next-steps"></a>Próximas etapas

Depois de ter implantado com sucesso conjuntos de escala de máquinas virtuais através do Visual Studio, você pode personalizar ainda mais seu projeto para atender aos requisitos do aplicativo. Por exemplo, configure a escala automática adicionando um recurso **Insights.** Você pode adicionar infra-estrutura ao seu modelo, como máquinas virtuais autônomas, ou implantar aplicativos usando a extensão de script personalizada. Bons modelos de exemplo podem ser encontrados no repositório [Azure Quickstart Templates](https://github.com/Azure/azure-quickstart-templates) GitHub. Pesquise `vmss`.
