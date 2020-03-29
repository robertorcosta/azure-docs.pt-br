---
title: Configure SKUs para uma oferta de aplicativo Do Zure | Mercado Azure
description: Como configurar as SKUs para um aplicativo gerenciado do Azure e um modelo de solução do Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: dsindona
ms.openlocfilehash: 043394a1303456ce5b209bb84b5afaf09f6beba4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80289066"
---
# <a name="azure-application-skus-tab"></a>Guia de SKUs de aplicativo do Azure

Este artigo descreve como usar a guia SKUs a fim de criar SKUs para seu aplicativo do Azure. 

> [!IMPORTANT]
> As etapas para configurar uma SKU são diferentes para uma oferta de aplicativo gerenciado e uma oferta de modelo de solução. Essas diferenças estão documentadas neste artigo. 

## <a name="configure-azure-application-skus"></a>Configurar SKUs de aplicativo do Azure

### <a name="create-a-new-sku"></a>Criar nova SKU

Use estas etapas para criar uma nova SKU:

1. Selecione a guia **SKUs**.
2. Em SKUs, selecione **+ Nova SKU**.

    ![Prompt de Nova SKU](./media/azureapp-plus-sku.png)

3. Na janela pop-up da nova SKU, digite uma **ID de SKU**. Essa ID é limitada a 50 caracteres e deve consistir apenas de caracteres em minúsculas, alfanuméricos, traços ou sublinhados. O SKU ID não pode terminar em um traço.
4. A ID de SKU fica visível para os clientes em URLs de produto, modelos do Resource Manager (se aplicável) e relatórios de cobrança. Você não pode modificar este id após a publicação da oferta.

### <a name="sku-details-for-a-solution-template"></a>Detalhes da SKU para um modelo de solução

A próxima captura de tela mostra o formulário Detalhes SKU para um modelo de solução.

![Formulário de detalhes de SKU para o modelo de solução](./media/azureapp-sku-details-solutiontemplate.png)

Forneça os seguintes valores de SKU.  Os campos anexados com um asterisco são necessários.

|    Campo         |       Descrição                                                            |
|  ---------       |     ---------------                                                          |
|  **Título\***     | Um título para o SKU. Esse título é mostrado na galeria para este item.   |
| **Resumo\***    | Uma breve descrição sumária do SKU. (O comprimento máximo é de 100 caracteres.)  |
| **Descrição\*** | Uma descrição detalhada do SKU. HTML básico é suportado.                 | 
| **Tipo SKU\***   | Tipo de solução de aplicativo Azure, selecione ***Modelo de solução** para este cenário. |
| **Disponibilidade na nuvem\*** | A localização do SKU. O padrão é **Public Azure**.  <b/>   **Public Azure** - App será implantável para clientes em todas as regiões públicas do Azure que tenham integração de marketplace.  <b/>   **Azure Government Cloud** - App será implantado na Nuvem do Governo do Azure. Antes de publicar para [o Governo Do Azure,](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners)a Microsoft recomenda que os editores testem e validem seus trabalhos de solução como esperado neste ambiente. Para preparação e teste, solicite [conta de avaliação](https://azure.microsoft.com/offers/ms-azr-usgov-0044p/).  |
| **É um SKU privado?\*** | Selecione **Sim** se este SKU estiver disponível apenas para um grupo seleto de clientes. |
|   |   |

  > [!NOTE] 
  > O Microsoft Azure Governamental é uma nuvem de comunidade do governo, com acesso controlado para clientes federais, estaduais, locais ou tribais dos EUA e parceiros qualificados para atender a essas entidades.


### <a name="sku-details-for-managed-application"></a>Detalhes de SKU do aplicativo gerenciado

A próxima captura de tela mostra o formulário de detalhes de SKU para um aplicativo gerenciado.

   ![Formulário de detalhes de SKU do aplicativo gerenciado](./media/azureapp-sku-details-managedapplication.png)

Configure as seguintes configurações de SKU. Os campos anexados com um asterisco são necessários.

|    Campo         |       Descrição                                                            |
|  ---------       |     ---------------                                                          |
|  **Título\***     | Um título para o SKU. Esse título é mostrado na galeria para este item.   |
| **Resumo\***    | Uma breve descrição sumária do SKU. (O comprimento máximo é de 100 caracteres.)  |
| **Descrição\*** | Uma descrição detalhada do SKU. HTML básico é suportado.                 | 
| **Tipo SKU\***   | Tipo de solução de aplicativo Azure, selecione ***Aplicativo gerenciado** para este cenário. 
| **Disponibilidade na nuvem\*** | A localização do SKU. O padrão é **Public Azure**.  <b/>   **Public Azure** - App será implantável para clientes em todas as regiões públicas do Azure que tenham integração de marketplace.  <b/>   **Azure Government Cloud** - App será implantado na Nuvem do Governo do Azure. Antes de publicar para [o Governo Do Azure,](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners)a Microsoft recomenda que os editores testem e validem seus trabalhos de solução como esperado neste ambiente. Para preparação e teste, solicite [conta de avaliação](https://azure.microsoft.com/offers/ms-azr-usgov-0044p/).   O Microsoft Azure Governamental é uma nuvem de comunidade do governo, com acesso controlado para clientes federais, estaduais, locais ou tribais dos EUA e parceiros qualificados para atender a essas entidades. |
| **É um SKU privado?\*** | Selecione **Sim** se este SKU estiver disponível apenas para um grupo seleto de clientes. |
| **Disponibilidade de país/região\*** | Use **regiões selecionadas** para visualizar a lista de países/regiões disponíveis. Verifique cada país/região e selecione **OK** para salvar suas escolhas.  <b/>   ![Lista de disponibilidade de país e região](./media/azure-app-select-country-region.png)  |
| **Preços antigos\*** | O preço do SKU, em USD por mês. Os preços são definidos em moeda local usando taxas de câmbio atuais após a configuração. Valide-os, já que essas configurações são suas. Para definir ou visualizar o preço de cada país/região individualmente, exporte a planilha de preços e importe com preços personalizados.  Você deve salvar suas alterações de preços para permitir a exportação/importação de dados de preços.  |
| **Preço em moeda simplificada\*** | O preço do SKU, em USD por mês. Deve ser igual ao Preço Antigo. Para obter mais informações, consulte [Preços simplificados da moeda](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-update-existing-offer). |
|  |  |


### <a name="package-details-for-solution-template"></a>Detalhes do pacote do modelo de solução

![Detalhes do pacote do modelo de solução](./media/azureapp-sku-pkgdetails-solutiontemplate.png)

Forneça os seguintes valores **de detalhes do pacote.**  Os campos anexados com um asterisco são necessários.

- **Versão\* ** - A versão do pacote que você vai carregar. As tags de versão devem ter o formato X.Y.Z, onde X, Y e Z são inteiros.
- **Arquivo de pacote\* (.zip)** - Este pacote contém os seguintes arquivos, salvos em um arquivo .zip.
  - **mainTemplate.json\* ** - O arquivo de modelo de implantação usado para implantar a solução/aplicativo e criar os recursos definidos para a solução. Para obter mais informações, confira [Como criar arquivos de modelo de implantação](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template).
  - **createUIDefinition.json\* ** - Este arquivo é usado pelo portal Azure para gerar a interface do usuário para o provisionamento desta solução/aplicativo. Para obter mais informações, confira [Criar uma interface do usuário do portal do Azure para seu aplicativo gerenciado](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview).
  - Scripts (se necessário) - Quaisquer scripts adicionais que possam `Microsoft.Compute/virtualMachines/extensions`ser necessários ao executar o modelo, por exemplo, .
  - Modelos aninhados (se necessário) - Quaisquer modelos aninhados adicionais.

  > [!IMPORTANT] 
  > Esse pacote deve conter outros modelos ou scripts aninhados que sejam necessários para provisionar o aplicativo. O arquivo principalTemplate.json e o arquivo createUIDefinition.json devem estar na pasta raiz. Para obter mais informações sobre artefatos de implantação, consulte [Azure Resource Manager Templates - Guia de Práticas Recomendadas](https://github.com/Azure/azure-quickstart-templates/blob/master/1-CONTRIBUTION-GUIDE/best-practices.md#deployment-artifacts-nested-templates-scripts).


### <a name="package-details-for-managed-application"></a>Detalhes de pacote do aplicativo gerenciado

   ![Detalhes de pacote do aplicativo gerenciado](./media/azureapp-sku-pkgdetails-managedapplication.png)

Forneça os seguintes detalhes do pacote.  Os campos anexados com um asterisco são necessários.

- **Versão\* ** - A versão do pacote que você vai carregar. As tags de versão devem ter o formato X.Y.Z, onde X, Y e Z são inteiros.
- **Arquivo de pacote\* (.zip)** - Este pacote contém os seguintes arquivos, salvos em um arquivo .zip.
  - applianceMainTemplate.json – o arquivo do modelo de implantação que é usado para implantar a solução/aplicativo e criar os recursos definidos. Para obter mais informações, consulte [Quickstart: Crie e implante modelos do Azure Resource Manager usando o portal Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal). 
  - applianceCreateUIDefinition.json – esse arquivo é usado pelo portal do Azure para gerar a interface do usuário para o provisionamento dessa solução/aplicativo. Para obter mais informações, confira [Criar uma interface do usuário do portal do Azure para seu aplicativo gerenciado](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview).
  - mainTemplate.json – o arquivo de modelo que contém somente o recurso Microsoft.Solution/appliances. Para obter mais informações, consulte [Entenda a estrutura e a sintaxe dos modelos do Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates). <br>
Observe as seguintes propriedades principais desse recurso:
    - "tipo" - O valor deve ser "Marketplace" no caso de aplicativo gerenciado por marketplace.
    - "ManagedResourceGroupId" - O grupo de recursos na assinatura do cliente onde todos os recursos definidos no aparelhoMainTemplate.json serão implantados.
    - “PublisherPackageId” – a cadeia de caracteres que identifica exclusivamente o pacote. Esse valor precisa ser construído da seguinte forma: é uma concatenação de [publisherId]. [OfferId]-preview[SKUID]. [PackageVersion].

  >[!IMPORTANT] 
  >Esse pacote deve conter outros modelos ou scripts aninhados que sejam necessários para provisionar o aplicativo. Esses arquivos devem estar na pasta raiz: MainTemplate.json, applianceMainTemplate.json e applianceCreateUIDefinition.json.

- **Id\* inquilino** - A id de inquilino do Diretório Ativo do Azure da sua organização.
- **Habilitar o JIT Access? \* ** - **Selecione Sim** para habilitar o acesso de gerenciamento Just-In-Time para implantações de clientes usando esta oferta.

  >[!NOTE] 
  >Se você habilitar o JIT, deverá atualizar o arquivo CreateUiDefinition.json para dar suporte a acesso JIT.

Para um aplicativo gerenciado, você deve definir as configurações de política e autorização.


#### <a name="authorization"></a>Autorização

Adicione o identificador do Azure Active Directory do usuário, grupo ou aplicativo ao qual você deseja conceder a permissão para o grupo de recursos gerenciado. A permissão concedida é indicada pela definição de função Id. Pode ser um Proprietário, Contribuinte ou qualquer função personalizada.


#### <a name="policy-settings"></a>Configurações de política

Adicione as políticas com as quais o aplicativo gerenciado está em conformidade. Saiba mais sobre políticas de recursos do Azure, confira [O que é o Azure Policy?](../../../governance/policy/overview.md)

   ![Configurações de autorização e política para um aplicativo gerenciado](./media/azureapp-sku-details-managedapp-auth-policy.png)

**Para criar uma nova regra de autorização:**

1. Em **Autorização**, selecione **+ Nova Autorização**.
2. Em **ID da entidade**, digite o identificador do Azure Active Directory do usuário, grupo ou aplicativo ao qual você deseja conceder a permissão para o grupo de recursos gerenciado. A permissão concedida é indicada pela Definição de Função.
3. Para **definição de**função, selecione uma dessas opções na lista de isto: Proprietário ou Contribuinte. Para obter mais informações, consulte [Funções internas dos recursos do Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).

>[!NOTE] 
>Várias autorizações podem ser adicionadas. No entanto, recomenda-se criar um grupo de usuários do Active Directory e especificar seu ID no "PrincipalId". Isso permitirá a adição de mais usuários ao grupo de usuários sem necessidade de atualizar a SKU.

**Para criar uma nova política:**

1. Em **Configurações de Política**, selecione **+ Nova Política**.
2. Em **Nome da Política**, insira um nome para a política. O nome pode ter no máximo 50 caracteres.
3. Em **Políticas**, selecione uma das opções na lista suspensa. Escolha a política que o provedor de dados deseja ter habilitada quando o aplicativo usar os dados. Para obter mais informações, confira os [Exemplos do Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/index).

    ![Configurações de política para um aplicativo gerenciado](./media/azureapp-sku-policy-settings.png)

4. Em **SKU de política**, selecione Gratuito ou Standard como o tipo de SKU de política. A SKU Standard é necessária para as políticas de auditoria.


## <a name="next-steps"></a>Próximas etapas

Você descreverá ainda mais sua oferta e fornecerá ativos de marketing na [guia Marketplace](./cpp-marketplace-tab.md). 
