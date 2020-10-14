---
title: Criar espaços de trabalho no portal
titleSuffix: Azure Machine Learning
description: Saiba como criar, exibir e excluir espaços de trabalho do Azure Machine Learning no portal do Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sgilley
author: sdgilley
ms.date: 09/30/2020
ms.topic: conceptual
ms.custom: how-to, fasttrack-edit
ms.openlocfilehash: d0b5d3678c3d9c7e55eede13c630510df89d5128
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92045560"
---
# <a name="create-and-manage-azure-machine-learning-workspaces-in-the-azure-portal"></a>Criar e gerenciar workspaces do Azure Machine Learning no portal do Azure


Neste artigo, você criará, exibirá e excluirá [**espaços de trabalho Azure Machine Learning**](concept-workspace.md) no portal do Azure para [Azure Machine Learning](overview-what-is-azure-ml.md).  O portal é a maneira mais fácil de começar com espaços de trabalho, mas conforme suas necessidades mudam ou requisitos para aumentar a automação, você também pode criar e excluir espaços de trabalho [usando a CLI](reference-azure-machine-learning-cli.md), [com código Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true) ou [por meio da extensão vs Code](tutorial-setup-vscode-extension.md).

## <a name="create-a-workspace"></a>Criar um workspace

Para criar um workspace, você precisa de uma assinatura do Azure. Caso não tenha uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente hoje mesmo a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree).

1. Entre no [portal do Microsoft Azure](https://portal.azure.com/) usando as credenciais de assinatura do Azure. 

1. No canto superior esquerdo do portal do Azure, selecione **+ Criar um recurso**.

      ![Criar um novo recurso](./media/how-to-manage-workspace/create-workspace.gif)

1. Use a barra de pesquisa para localizar **Machine Learning**.

1. Selecione **Machine Learning**.

1. No painel **Machine Learning**, selecione **Criar** para começar.

1. Forneça as informações a seguir para configurar o novo workspace:

   Campo|Descrição 
   ---|---
   Nome do workspace |Insira um nome único que identifique seu workspace. Para este exemplo, usamos **docs-ws**. Os nomes devem ser únicos em todo o grupo de recursos. Use um nome que seja fácil de lembrar e diferenciar de workspaces criados por outras pessoas. O nome do workspace não diferencia maiúsculas de minúsculas.
   Subscription |Selecione a assinatura do Azure que deseja usar.
   Resource group | Use um grupo de recursos existente na sua assinatura ou insira um nome para criar um grupo de recursos. Um grupo de recursos mantém os recursos relacionados a uma solução do Azure. Para este exemplo, usamos **docs-aml**. Você precisa de um *colaborador* ou função de *proprietário* para usar um grupo de recursos existente.  Para obter mais informações sobre o acesso, consulte [gerenciar o acesso a um espaço de trabalho do Azure Machine Learning](how-to-assign-roles.md).
   Região | Selecione a região do Azure mais próxima aos usuários e aos recursos de dados para criar seu espaço de trabalho.

1. Quando tiver terminado de configurar o espaço de trabalho, selecione **examinar + criar**. Opcionalmente, use as seções [rede](#networking) e [avançado](#advanced) para definir mais configurações para o espaço de trabalho.

1. Examine as configurações e faça quaisquer alterações ou correções adicionais. Quando estiver satisfeito com as configurações, selecione **criar**.

   > [!Warning] 
   > Pode levar vários minutos para criar seu workspace na nuvem.

   Quando o processo é finalizado, será exibida uma mensagem de êxito da implantação. 
 
 1. Para exibir o novo workspace, selecione **Ir para o recurso**.

### <a name="networking"></a>Rede  

> [!IMPORTANT]  
> Para obter mais informações sobre como usar um ponto de extremidade privado e uma rede virtual com seu espaço de trabalho, consulte [isolamento de rede e privacidade](how-to-enable-virtual-network.md).
    
1. A configuração de rede padrão é usar um __ponto de extremidade público__, que pode ser acessado na Internet pública. Para limitar o acesso ao seu espaço de trabalho a uma rede virtual do Azure que você criou, você pode selecionar __ponto de extremidade privado__ como o __método de conectividade__e, em seguida, usar __+ Adicionar__ para configurar o ponto de extremidade. 
    
   :::image type="content" source="media/how-to-manage-workspace/select-private-endpoint.png" alt-text="Seleção de ponto de extremidade particular":::  

1. No formulário __criar ponto de extremidade privado__ , defina o local, o nome e a rede virtual a ser usada. Se você quiser usar o ponto de extremidade com uma zona DNS privado, selecione __integrar com a zona DNS privada__ e selecione a zona usando o campo __zona de DNS privado__ . Selecione __OK__ para criar o ponto de extremidade.   

   :::image type="content" source="media/how-to-manage-workspace/create-private-endpoint.png" alt-text="Seleção de ponto de extremidade particular":::   

1. Quando terminar de configurar a rede, você poderá selecionar __revisar + criar__ou avançar para a configuração __avançada__ opcional. 

    > [!WARNING]    
    > Quando você cria um ponto de extremidade privado, uma nova zona de DNS privado chamada __privatelink.API.azureml.ms__ é criada. Ele contém um link para a rede virtual. Se você criar vários espaços de trabalho com pontos de extremidade privados no mesmo grupo de recursos, somente a rede virtual para o primeiro ponto final privado poderá ser adicionada à zona DNS. Para adicionar entradas para as redes virtuais usadas pelos pontos de extremidade de espaços de trabalho/particulares adicionais, use as seguintes etapas: 
    >   
    > 1. No [portal do Azure](https://portal.azure.com), selecione o grupo de recursos que contém o espaço de trabalho. Em seguida, selecione o DNS privado recurso de zona chamado __privatelink.API.azureml.ms__.    
    > 2. Nas __configurações__, selecione __links de rede virtual__. 
    > 3. Selecione __Adicionar__. Na página __Adicionar link de rede virtual__ , forneça um __nome de link__exclusivo e selecione a __rede virtual__ a ser adicionada. Selecione __OK__ para adicionar o link de rede.    
    >   
    > Para obter mais informações, consulte [configuração de DNS do ponto de extremidade privado do Azure](/azure/private-link/private-endpoint-dns).   

### <a name="vulnerability-scanning"></a>Verificação de vulnerabilidade

A Central de Segurança do Azure fornece um gerenciamento de segurança unificado e proteção avançada contra ameaças nas cargas de trabalho de nuvem híbrida. Você deve permitir que a central de segurança do Azure verifique seus recursos e siga suas recomendações. Para obter mais informações, consulte  [verificação de imagem do registro de contêiner do Azure por central de segurança](https://docs.microsoft.com/azure/security-center/azure-container-registry-integration) e [integração dos serviços do Azure kubernetes com a central de segurança](https://docs.microsoft.com/azure/security-center/azure-kubernetes-service-integration).

### <a name="advanced"></a>Avançado    

Por padrão, as métricas e os metadados para o espaço de trabalho são armazenados em uma instância Azure Cosmos DB que a Microsoft mantém. Esses dados são criptografados usando chaves gerenciadas pela Microsoft.  

Para limitar os dados que a Microsoft coleta em seu espaço de trabalho, selecione __alto espaço de trabalho de impacto nos negócios__. Para obter mais informações sobre essa configuração, consulte [criptografia em repouso](concept-enterprise-security.md#encryption-at-rest).

> [!IMPORTANT]  
> A seleção de alto impacto nos negócios só pode ser feita durante a criação de um espaço de trabalho. Você não pode alterar essa configuração após a criação do espaço de trabalho.   
Se você estiver usando a versão __empresarial__ do Azure Machine Learning, poderá fornecer sua própria chave. Isso cria a instância de Azure Cosmos DB que armazena métricas e metadados em sua assinatura do Azure. Use as seguintes etapas para usar sua própria chave:    

> [!IMPORTANT]  
> Antes de seguir essas etapas, você deve primeiro executar as seguintes ações:   
>   
> 1. Autorize o __aplicativo Machine Learning__ (no gerenciamento de identidade e acesso) com permissões de colaborador em sua assinatura.  
> 1. Siga as etapas em [Configurar chaves gerenciadas pelo cliente](/azure/cosmos-db/how-to-setup-cmk) para:   
>     * Registrar o provedor de Azure Cosmos DB   
>     * Criar e configurar um Azure Key Vault 
>     * Gerar uma chave  
>   
>     Você não precisa criar manualmente a instância de Azure Cosmos DB, uma será criada para você durante a criação do espaço de trabalho. Esta instância de Azure Cosmos DB será criada em um grupo de recursos separado usando um nome com base neste padrão: `<your-workspace-resource-name>_<GUID>` .   
>   
> Você não pode alterar essa configuração após a criação do espaço de trabalho. Se você excluir o Azure Cosmos DB usado pelo seu espaço de trabalho, também deverá excluir o espaço de trabalho que o está usando.

1. Selecione __chaves gerenciadas pelo cliente__e, em seguida, selecione __clique para selecionar chave__.   

    :::image type="content" source="media/how-to-manage-workspace/advanced-workspace.png" alt-text="Seleção de ponto de extremidade particular":::   

1. No formulário __selecionar chave de Azure Key Vault__ , selecione um Azure Key Vault existente, uma chave que ele contém e a versão da chave. Essa chave é usada para criptografar os dados armazenados no Azure Cosmos DB. Por fim, use o botão __selecionar__ para usar essa chave. 

   :::image type="content" source="media/how-to-manage-workspace/select-key-vault.png" alt-text="Seleção de ponto de extremidade particular":::

### <a name="download-a-configuration-file"></a>Baixar um arquivo de configuração

1. Se você for criar uma [instância de computação](tutorial-1st-experiment-sdk-setup.md#azure), ignore esta etapa.

1. Se você planeja usar código em seu ambiente local que referencia esse workspace, selecione **Baixar config.json** da seção **Visão geral** do workspace.  

   ![Baixe o config.json](./media/how-to-manage-workspace/configure.png)
   
   Coloque o arquivo na estrutura de diretório com seus scripts de Python ou Jupyter Notebooks. Ele pode estar no mesmo diretório, um subdiretório denominado *.azureml* ou em um diretório pai. Quando você cria uma instância de computação, esse arquivo é adicionado ao diretório correto na VM para você.
## <a name="find-a-workspace"></a><a name="view"></a>Localizar um espaço de trabalho

1. Entre no [portal do Azure](https://portal.azure.com/).

1. No campo de pesquisa superior, digite **Machine Learning**.  

1. Selecione **Machine Learning**.

   ![Procurar Azure Machine Learning espaço de trabalho](./media/how-to-manage-workspace/find-workspaces.png)

1. Examine a lista de workspaces encontrada. É possível filtrar com base na assinatura, grupos de recursos e locais.  

1. Selecione um espaço de trabalho para exibir suas propriedades.

## <a name="delete-a-workspace"></a>Excluir um workspace

Na [portal do Azure](https://portal.azure.com/), selecione **excluir**  na parte superior do espaço de trabalho que você deseja excluir.

:::image type="content" source="./media/how-to-manage-workspace/delete-workspace.png" alt-text="Seleção de ponto de extremidade particular":::

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="troubleshooting"></a>Solução de problemas

### <a name="resource-provider-errors"></a>Erros do provedor de recursos

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="moving-the-workspace"></a>Como mover o workspace

> [!WARNING]
> Não há suporte para a movimentação do workspace do Azure Machine Learning para outra assinatura nem para a movimentação da assinatura proprietária para um novo locatário. Se você fizer isso, poderá causar erros.

### <a name="deleting-the-azure-container-registry"></a>Como excluir o Registro de Contêiner do Azure

O workspace do Azure Machine Learning usa o ACR (Registro de Contêiner do Azure) para algumas operações. Ele criará automaticamente uma instância do ACR quando precisar de uma pela primeira vez.

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

## <a name="next-steps"></a>Próximas etapas

Siga o tutorial de tamanho completo para aprender a usar um espaço de trabalho para criar, treinar e implantar modelos com o Azure Machine Learning.

> [!div class="nextstepaction"]
> [Tutorial: Treinar modelos](tutorial-train-models-with-aml.md)
