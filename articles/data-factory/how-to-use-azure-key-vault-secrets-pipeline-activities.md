---
title: Usar segredos de Azure Key Vault em atividades de pipeline
description: Aprenda a buscar credenciais armazenadas no cofre de chaves do Azure e use-as durante as operações de pipeline de fábrica de dados.
services: data-factory
author: ChrisLound
manager: anandsub
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: chlound
ms.openlocfilehash: 09051ad3633ddc720cb34d3d145ccf649fa9cb08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77200105"
---
# <a name="use-azure-key-vault-secrets-in-pipeline-activities"></a>Usar segredos de Azure Key Vault em atividades de pipeline

Você pode armazenar credenciais ou valores secretos em um Cofre de Chaves Do Azure e usá-los durante a execução do pipeline para passar para suas atividades.

## <a name="prerequisites"></a>Pré-requisitos

Esse recurso se baseia na identidade gerenciada pela fábrica de dados.  Saiba como ele funciona a partir da [identidade gerenciada para fábrica de dados](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity) e certifique-se de que sua fábrica de dados tenha um associado.

## <a name="steps"></a>Etapas

1. Abra as propriedades da sua fábrica de dados e copie o valor de Identificação do aplicativo de identidade gerenciada.

    ![Valor de identidade gerenciado](media/how-to-use-azure-key-vault-secrets-pipeline-activities/managedidentity.png)

2. Abra as políticas de acesso do cofre de chaves e adicione as permissões de identidade gerenciadas para obter e listar segredos.

    ![Políticas de acesso ao cofre de chaves](media/how-to-use-azure-key-vault-secrets-pipeline-activities/akvaccesspolicies.png)

    ![Políticas de acesso ao cofre de chaves](media/how-to-use-azure-key-vault-secrets-pipeline-activities/akvaccesspolicies-2.png)

    Clique **em Adicionar**e clique em **Salvar**.

3. Navegue até o seu segredo do Key Vault e copie o identificador secreto.

    ![Identificador Secreto](media/how-to-use-azure-key-vault-secrets-pipeline-activities/secretidentifier.png)

    Anote seu URI secreto que deseja obter durante a sua execução de pipeline de fábrica de dados.

4. No pipeline da Fábrica de Dados, adicione uma nova atividade da Web e configure-a da seguinte forma.  

    |Propriedade  |Valor  |
    |---------|---------|
    |Saída segura     |True         |
    |URL     |[Seu valor de URI secreto]?api-version=7.0         |
    |Método     |GET         |
    |Autenticação     |MSI         |
    |Recurso        |https://vault.azure.net       |

    ![Atividade da Web](media/how-to-use-azure-key-vault-secrets-pipeline-activities/webactivity.png)

    > [!IMPORTANT]
    > Você deve adicionar **?api-version=7.0** ao final do seu URI secreto.  

    > [!CAUTION]
    > Defina a opção Saída segura como verdadeira para evitar que o valor secreto seja registrado em texto simples.  Quaisquer atividades adicionais que consumam esse valor devem ter a opção Entrada Segura definida como verdadeira.

5. Para usar o valor em outra atividade, use a seguinte expressão de ** @activitycódigo ('Web1').output.value**.

    ![Expressão de código](media/how-to-use-azure-key-vault-secrets-pipeline-activities/usewebactivity.png)

## <a name="next-steps"></a>Próximas etapas

Para saber como usar o Azure Key Vault para armazenar credenciais para armazenamento de dados e cálculos, consulte [As credenciais da loja no Azure Key Vault](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)
