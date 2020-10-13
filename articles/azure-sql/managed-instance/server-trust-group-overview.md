---
title: Grupo de confiança do servidor
titleSuffix: Azure SQL Managed Instance
description: Saiba mais sobre o grupo de confiança do servidor e como gerenciar a confiança entre instâncias gerenciadas do SQL do Azure.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: sasapopo
ms.author: sasapopo
ms.reviewer: sstein, bonova
ms.date: 10/08/2020
ms.openlocfilehash: 911d7ffa2b1d313147ca73d0ceb285ea2e84b1f7
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/13/2020
ms.locfileid: "91979360"
---
# <a name="use-server-trust-groups-to-setup-and-manage-trust-between-sql-managed-instances"></a>Usar grupos de confiança do servidor para configurar e gerenciar a confiança entre instâncias gerenciadas do SQL
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

O grupo de confiança do servidor é um conceito usado para gerenciar a confiança entre instâncias gerenciadas do SQL do Azure. A criação de um grupo e a adição de instâncias à confiança baseada em certificado de ti são estabelecidas entre todos os membros e isso pode ser usado para diferentes cenários de instância cruzada. Remover servidores do grupo ou excluir os resultados do grupo na remoção da relação de confiança entre os servidores. Para criar ou excluir o usuário do grupo de confiança do servidor precisa ter permissões de gravação em Instância Gerenciada.
O [grupo de confiança do servidor](https://aka.ms/mi-server-trust-group-arm) é um objeto Azure Resource Manager que corresponde à entidade portal do Azure chamada **grupo de confiança do SQL**.

> [!NOTE]
> O grupo de confiança do servidor é introduzido na visualização pública de transações distribuídas entre instâncias gerenciadas do SQL do Azure e atualmente tem algumas limitações que serão descritas posteriormente neste artigo.

## <a name="server-trust-group-setup"></a>Configuração do grupo de confiabilidade do servidor

A seção a seguir descreve a instalação do grupo de confiança do servidor.

1. Vá para o [Portal do Azure](https://portal.azure.com/).

2. Navegue até o Azure SQL Instância Gerenciada que você planeja adicionar a um grupo de confiança do servidor recém-criado.

3. Nas configurações de **segurança** , selecione a guia **grupos de confiabilidade do SQL** .

   :::image type="content" source="./media/server-trust-group-overview/security-sql-trust-groups.png" alt-text="Grupos de confiança do servidor":::

4. Na página configuração do grupo de confiabilidade do servidor, selecione o ícone **novo grupo** .

   :::image type="content" source="./media/server-trust-group-overview/server-trust-group-create-new-group.png" alt-text="Grupos de confiança do servidor":::

5. Na folha criar **grupo de confiabilidade do SQL** , defina o nome do **grupo**. Ele precisa ser exclusivo em todas as regiões em que os membros do grupo residem. O **escopo de confiança** define o tipo de cenário de instância cruzada que é habilitado com o grupo de confiança do servidor e, na visualização, o único escopo de confiança aplicável é de **transações distribuídas**, portanto, ele é previamente selecionado e não pode ser alterado. Todos os **membros do grupo** devem pertencer à mesma **assinatura** , mas podem estar em grupos de recursos diferentes. Selecione o **grupo de recursos** e **SQL Server/instância** para escolher o instância gerenciada SQL do Azure que será membro do grupo.
   :::image type="content" source="./media/server-trust-group-overview/server-trust-group-create-blade.png" alt-text="Grupos de confiança do servidor":::

6. Depois que todos os campos obrigatórios forem preenchidos, clique em **salvar**.

## <a name="server-trust-group-maintenance-and-deletion"></a>Manutenção e exclusão do grupo de confiança do servidor

Não é possível editar o grupo de confiança do servidor. Para remover um Instância Gerenciada de um grupo, você precisa excluir o grupo e criar um novo.

A seção a seguir descreve o processo de exclusão de grupos de confiança do servidor. 
1. Acesse o portal do Azure.
2. Navegue até um Instância Gerenciada que pertence ao grupo de confiança.
3. Nas configurações de **segurança** , selecione a guia **grupos de confiabilidade do SQL** .
4. Selecione o grupo de confiança que você deseja excluir.
   :::image type="content" source="./media/server-trust-group-overview/server-trust-group-manage-select.png" alt-text="Grupos de confiança do servidor":::
5. Clique em **excluir grupo**.
   :::image type="content" source="./media/server-trust-group-overview/server-trust-group-manage-delete.png" alt-text="Grupos de confiança do servidor":::
6. Digite o nome do grupo de confiança do servidor para confirmar a exclusão e clique em **excluir**.
   :::image type="content" source="./media/server-trust-group-overview/server-trust-group-manage-delete-confirm.png" alt-text="Grupos de confiança do servidor":::

> [!NOTE]
> Excluir o grupo de confiança do servidor pode não remover imediatamente a relação de confiança entre as duas instâncias gerenciadas. A remoção de confiança pode ser imposta invocando um [failover](https://docs.microsoft.com/powershell/module/az.sql/Invoke-AzSqlInstanceFailover) de instâncias gerenciadas. Verifique os [problemas conhecidos](https://docs.microsoft.com/azure/azure-sql/database/doc-changes-updates-release-notes?tabs=managed-instance#known-issues) para as atualizações mais recentes sobre isso.

## <a name="limitations"></a>Limitações

Durante a visualização pública, as seguintes limitações se aplicam aos grupos de confiança do servidor.
 * O nome do grupo de confiança do servidor deve ser exclusivo em todas as regiões em que seus membros são.
 * O grupo pode conter apenas instâncias gerenciadas do SQL do Azure e deve estar na mesma assinatura do Azure.
 * O grupo pode ter exatamente duas instâncias gerenciadas. Se você precisar executar transações distribuídas em mais de duas instâncias gerenciadas que podem ser feitas com a criação de um grupo de confiança do servidor para cada par das instâncias gerenciadas.
 * As transações distribuídas são o único escopo aplicável para os grupos de confiança do servidor.
 * O grupo de confiança do servidor só pode ser gerenciado a partir de portal do Azure. O suporte ao PowerShell e à CLI será fornecido mais tarde.
 * O grupo de confiança do servidor não pode ser editado no portal do Azure. Ele só pode ser criado ou descartado.
 * Limitações adicionais de transações distribuídas podem estar relacionadas ao seu cenário. A mais notável é que as instâncias gerenciadas do SQL que participam do grupo de confiança do servidor devem ser acessíveis por meio de pontos de extremidade privados, ou em outra conectividade deve funcionar no nível da rede virtual. Verifique se você está ciente das limitações atuais de [transações distribuídas para o Azure SQL instância gerenciada](https://docs.microsoft.com/azure/azure-sql/database/elastic-transactions-overview#limitations).

## <a name="next-steps"></a>Próximas etapas

* Para obter mais informações sobre transações distribuídas no Azure SQL Instância Gerenciada, consulte [transações distribuídas](../database/elastic-transactions-overview.md).
* Para ver as atualizações de versão e o estado de problemas conhecidos, consulte [instância gerenciada notas de versão](../database/doc-changes-updates-release-notes.md).
* Para solicitações de recursos, adicione-as ao [Fórum de instância gerenciada](https://feedback.azure.com/forums/915676-sql-managed-instance).