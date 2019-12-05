---
title: Utilizar a referência de modelo
description: Utilize a referência de modelo do Azure Resource Manager para criar um modelo para a implantação de uma conta de armazenamento criptografada.
author: mumian
ms.date: 03/04/2019
ms.topic: tutorial
ms.author: jgao
ms.custom: seodec18
ms.openlocfilehash: 99ec64529b90c7a80aea62090f80c55cf4e23510
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74326491"
---
# <a name="tutorial-utilize-the-azure-resource-manager-template-reference"></a>Tutorial: Utilizar a referência de modelo do Azure Resource Manager

Saiba como localizar as informações de esquema de modelo e usar as informações para criar modelos do Azure Resource Manager.

Neste tutorial, você usará um modelo de base dos modelos de Início Rápido do Azure. Usando a documentação de referência do modelo, personalize o modelo para criar uma conta de armazenamento criptografada.

![Referência do modelo do Resource Manager – implantar conta de armazenamento criptografada](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-tutorial-deploy-encrypted-storage-account.png)

Este tutorial cobre as seguintes tarefas:

> [!div class="checklist"]
> * Abrir um modelo de Início Rápido
> * Entender o modelo
> * Encontrar a referência de modelo
> * Editar o modelo
> * Implantar o modelo

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este artigo, você precisa do seguinte:

* Visual Studio Code com a extensão de Ferramentas do Resource Manager. Confira [Usar o Visual Studio Code para criar modelos do Azure Resource Manager](./resource-manager-tools-vs-code.md).

## <a name="open-a-quickstart-template"></a>Abrir um modelo de Início Rápido

[Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/) é um repositório de modelos do Gerenciador de Recursos. Em vez de criar um modelo do zero, você pode encontrar um exemplo de modelo e personalizá-lo. O modelo usado neste início rápido é chamado [Criar uma conta de armazenamento padrão](https://azure.microsoft.com/resources/templates/101-storage-account-create/). O modelo define um recurso da conta de Armazenamento do Azure.

1. No Visual Studio Code, escolha **Arquivo**>**Abrir Arquivo**.
2. Em **Nome do arquivo**, cole a seguinte URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```
3. Escolha **Abrir** para abrir o arquivo.
4. Escolha **Arquivo**>**Salvar como** para salvar o arquivo como **azuredeploy.json** em seu computador local.

## <a name="understand-the-schema"></a>Entenda o esquema

1. No VS Code, recolha o modelo até o nível raiz. Você tem uma estrutura mais simples com os seguintes elementos:

    ![Estrutura mais simples do modelo do Resource Manager](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-simplest-structure.png)

    * **$schema**: especifique o local do arquivo de esquema JSON que descreve a versão da linguagem do modelo.
    * **contentVersion**: especifique algum valor para esse elemento a fim de documentar alterações significativas no modelo.
    * **parameters**: especifique os valores que são fornecidos quando a implantação é executada para personalizar a implantação dos recursos.
    * **variables**: especifique os valores que são usados como fragmentos JSON no modelo para simplificar expressões de linguagem do modelo.
    * **resources**: especifique os tipos de recursos que são implantados ou atualizados em um grupo de recursos.
    * **gera**: especifique os valores que serão retornados após a implantação.

2. Expanda os **recursos**. Há um recurso `Microsoft.Storage/storageAccounts` definido. O modelo cria uma conta de armazenamento não criptografada.

    ![Definição da conta de armazenamento do modelo do Resource Manager](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-encrypted-storage-resource.png)

## <a name="find-the-template-reference"></a>Encontrar a referência de modelo

1. Navegue até [Referência de modelo do Azure](https://docs.microsoft.com/azure/templates/).
2. Na caixa **Filtrar por título**, insira **contas de armazenamento**.
3. Selecione **Referência/Referência de modelo/Armazenamento/&lt;Versão>/Contas de Armazenamento** conforme mostrado na seguinte captura de tela:

    ![Conta de armazenamento de referência de modelo do Resource Manager](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-resources-reference-storage-accounts.png)

    Se você não souber qual versão escolher, use a versão mais recente.

4. Encontre as informações de definição relacionadas à criptografia.

    ```json
    "encryption": {
      "services": {
        "blob": {
          "enabled": boolean
        },
        "file": {
          "enabled": boolean
        }
      },
      "keySource": "string",
      "keyvaultproperties": {
        "keyname": "string",
        "keyversion": "string",
        "keyvaulturi": "string"
      }
    },
    ```

    Na mesma página da Web, a descrição a seguir confirma que o objeto `encryption` é usado para criar uma conta de armazenamento criptografada.

    ![Criptografia da conta de armazenamento de referência do modelo do Resource Manager](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-resources-reference-storage-accounts-encryption.png)

    E há duas maneiras de gerenciar a chave de criptografia. Use chaves de criptografia gerenciadas pela Microsoft com a Criptografia do Serviço de Armazenamento ou suas próprias chaves de criptografia. Para simplificar este tutorial, você usará a opção `Microsoft.Storage` para não precisar criar um Azure Key Vault.

    ![Objeto de criptografia da conta de armazenamento de referência do modelo do Resource Manager](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-resources-reference-storage-accounts-encryption-object.png)

    O objeto de criptografia deverá se parecer com:

    ```json
    "encryption": {
        "services": {
            "blob": {
                "enabled": true
            },
            "file": {
              "enabled": true
            }
        },
        "keySource": "Microsoft.Storage"
    }
    ```

## <a name="edit-the-template"></a>Editar o modelo

No Visual Studio Code, modifique o modelo para que o elemento resources se pareça com:

![Recursos da conta de armazenamento criptografada do modelo do Resource Manager](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-encrypted-storage-resources.png)

## <a name="deploy-the-template"></a>Implantar o modelo

Consulte a seção [Implantar o modelo](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#deploy-the-template) do guia de início rápido do Visual Studio Code para o procedimento de implantação.

A captura de tela a seguir mostra o comando da CLI para listar a conta de armazenamento recém-criada, que indica que a criptografia foi habilitada para o armazenamento de blobs.

![Conta de armazenamento criptografada do Azure Resource Manager](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-encrypted-storage-account.png)

## <a name="clean-up-resources"></a>Limpar recursos

Quando os recursos do Azure já não forem necessários, limpe os recursos implantados excluindo o grupo de recursos.

1. No portal do Azure, escolha **Grupos de recursos** do menu à esquerda.
2. No campo **Filtrar por nome**, insira o nome do grupo de recursos.
3. Escolha o nome do grupo de recursos.  Você deverá ver um total de seis recursos no grupo de recursos.
4. Escolha **Excluir grupo de recursos** no menu superior.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a usar a referência de modelo para personalizar um modelo existente. Para saber como criar várias instâncias da conta de armazenamento, veja:

> [!div class="nextstepaction"]
> [Criar múltiplas instâncias](./resource-manager-tutorial-create-multiple-instances.md)
