---
title: Como conceder permissões para identidade gerenciada no espaço de trabalho Do Azure Synapse
description: Um artigo que explica como configurar permissões para identidade gerenciada no espaço de trabalho Do Azure Synapse.
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 9f519022fffe98c565c3b2d30f6578b9ebb70c57
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428011"
---
# <a name="grant-permissions-to-workspace-managed-identity-preview"></a>Conceder permissões para a identidade gerenciada do espaço de trabalho (visualização)

Este artigo ensina como conceder permissões à identidade gerenciada no espaço de trabalho sinapse azure. As permissões, por sua vez, permitem o acesso a pools SQL no espaço de trabalho e na conta de armazenamento ADLS gen2 através do portal Azure.

>[!NOTE]
>Esta identidade gerenciada do espaço de trabalho será referida como identidade gerenciada através do resto deste documento.

## <a name="grant-the-managed-identity--permissions-to-the-sql-pool"></a>Conceda as permissões de identidade gerenciadas ao pool SQL

A identidade gerenciada concede permissões aos pools SQL no espaço de trabalho. Com as permissões concedidas, você pode orquestrar pipelines que executam atividades relacionadas ao pool SQL. Quando você cria um espaço de trabalho Azure Synapse usando o portal Azure, você pode conceder as permissões de CONTROLE de identidade gerenciadas em pools SQL.

Selecione **Segurança + rede** quando estiver criando seu espaço de trabalho Azure Synapse. Em seguida, **selecione Grant CONTROL para a identidade gerenciada do espaço de trabalho em pools SQL**.

![Permissão CONTROL em pools SQL](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-16.png)

## <a name="grant-the-managed-identity-permissions-to-adls-gen2-storage-account"></a>Conceda as permissões de identidade gerenciadas à conta de armazenamento ADLS gen2

Uma conta de armazenamento ADLS gen2 é necessária para criar um espaço de trabalho Do Azure Synapse. Para lançar com sucesso os pools Spark no espaço de trabalho Do Azure Synapse, a identidade gerenciada do Azure Synapse precisa da função *de contribuinte de dados do Blob de armazenamento* nesta conta de armazenamento . A orquestração de pipeline no Azure Synapse também se beneficia desse papel.

### <a name="grant-permissions-to-managed-identity-during-workspace-creation"></a>Conceder permissões para identidade gerenciada durante a criação do espaço de trabalho

O Azure Synapse tentará conceder a função de Contribuinte de Dados blob de armazenamento à identidade gerenciada depois de criar o espaço de trabalho Azure Synapse usando o portal Azure. Você fornece os detalhes da conta de armazenamento ADLS gen2 na guia **Basics.**

![Guia básica no fluxo de criação de espaço de trabalho](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-1.png)

Escolha a conta de armazenamento e o sistema de arquivos ADLS gen2 no **nome da conta** e nome do sistema de **arquivo**.

![Fornecendo detalhes da conta de armazenamento ADLS gen2](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-2.png)

Se o criador do espaço de trabalho também **for proprietário da** conta de armazenamento ADLS gen2, o Azure Synapse atribuirá a função de Contribuinte de Dados *blob de armazenamento* à identidade gerenciada. Você verá a seguinte mensagem abaixo dos detalhes da conta de armazenamento que você inseriu.

![Atribuição bem-sucedida do contribuinte de dados blob de armazenamento](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-3.png)

Se o criador do espaço de trabalho não for o proprietário da conta de armazenamento ADLS gen2, então o Azure Synapse não atribui a função *de Contribuinte de Dados do Blob de Armazenamento* à identidade gerenciada. A mensagem que aparece abaixo dos detalhes da conta de armazenamento notifica o criador do espaço de trabalho de que eles não têm permissões suficientes para conceder a função *de Contribuinte de Dados blob de armazenamento* à identidade gerenciada.

![Atribuição de contribuinte de dados blob de armazenamento mal sucedida](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-4.png)

Como a mensagem afirma, você não pode criar pools Spark a menos que o *Contribuinte de Dados blob de armazenamento* seja atribuído à identidade gerenciada.

### <a name="grant-permissions-to-managed-identity-after-workspace-creation"></a>Conceder permissões para identidade gerenciada após a criação do espaço de trabalho

Durante a criação do espaço de trabalho, se você não atribuir o *contribuinte de dados blob de armazenamento* à identidade gerenciada, o **Proprietário** da conta de armazenamento ADLS gen2 atribui manualmente essa função à identidade. As etapas a seguir ajudarão você a realizar a tarefa manual.

#### <a name="step-1-navigate-to-the-adls-gen2-storage-account-in-azure-portal"></a>Passo 1: Navegue até a conta de armazenamento ADLS gen2 no portal Azure

No portal Azure, abra a conta de armazenamento ADLS gen2 e selecione **Visão geral** na navegação à esquerda. Você só precisará atribuir a função *de contribuinte de dados blob de armazenamento* no nível de contêiner ou sistema de arquivos. Selecione **Contêineres**.  
![Visão geral da conta de armazenamento ADLS gen2](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-5.png)

#### <a name="step-2-select-the-container"></a>Passo 2: Selecione o recipiente

A identidade gerenciada deve ter acesso aos dados ao contêiner (sistema de arquivos) que foi fornecido quando o espaço de trabalho foi criado. Você pode encontrar este contêiner ou sistema de arquivos no portal Azure. Abra o espaço de trabalho do Azure Synapse no portal Azure e selecione a guia **Visão geral** da navegação à esquerda.
![Contêiner de conta de armazenamento ADLS gen2](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-7.png)


Selecione o mesmo sistema de contêiner ou arquivo para conceder a função *de contribuinte de dados blob de armazenamento* à identidade gerenciada.
![Seleção de contêiner de armazenamento ADLS gen2](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-6.png)

#### <a name="step-3-navigate-to-access-control"></a>Passo 3: Navegar para o controle de acesso

Selecione **controle de acesso (IAM)**.

![Controle de acesso (IAM)](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-8.png)

#### <a name="step-4-add-a-new-role-assignment"></a>Passo 4: Adicionar uma nova atribuição de função

Selecione **+ Adicionar**.

![Adicionar nova atribuição de função](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-9.png)

#### <a name="step-5-select-the-rbac-role"></a>Passo 5: Selecione a função RBAC

Selecione a função **'Contribuinte de dados blob'** de armazenamento.

![Selecione a função RBAC](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-10.png)

#### <a name="step-6-select-the-azure-ad-security-principal"></a>Passo 6: Selecione o diretor de segurança do Azure AD

Selecione o usuário, grupo ou principal de **serviço do Azure AD** a partir do **acesso Atribuir a** ser derrubado.

![Selecione o principal de segurança AAD](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-11.png)

#### <a name="step-7-search-for-the-managed-identity"></a>Passo 7: Busca pela identidade gerenciada

O nome da identidade gerenciada também é o nome do espaço de trabalho. Procure sua identidade gerenciada inserindo o nome do espaço de trabalho Do Azure Synapse em **Select**. Você deve ver a identidade gerenciada listada.

![Encontre a identidade gerenciada](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-12.png)

#### <a name="step-8-select-the-managed-identity"></a>Passo 8: Selecione a identidade gerenciada

Selecione a identidade gerenciada para os **membros selecionados**. Selecione **Salvar** para adicionar a atribuição de função.

![Selecione a identidade gerenciada](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-13.png)

#### <a name="step-9-verify-that-the-storage-blob-data-contributor-role-is-assigned-to-the-managed-identity"></a>Passo 9: Verifique se a função de contribuinte de dados blob de armazenamento é atribuída à identidade gerenciada

Selecione **Control de acesso (IAM)** e selecione **As atribuições de função**.

![Verificar atribuição de função](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-14.png)

Você deve ver sua identidade gerenciada listada na seção **Armazenamento Blob Data Contributor** com a função de contribuinte de dados *blob* de armazenamento atribuído a ele. 
![Seleção de contêiner de armazenamento ADLS gen2](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-15.png)

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [a identidade gerenciada do Workspace](./synapse-workspace-managed-identity.md)
