---
title: Solucionar problemas do Azure Data Share
description: Saiba como solucionar problemas com convites e erros ao criar ou receber compartilhamentos de dados com o compartilhamento de dados do Azure.
services: data-share
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: troubleshooting
ms.date: 07/10/2019
ms.openlocfilehash: 6ad612d56b25da9e092070198e321e7fca8ad96b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73490556"
---
# <a name="troubleshoot-common-issues-in-azure-data-share"></a>Solucionar problemas comuns no compartilhamento de dados do Azure 

Este artigo mostra como solucionar problemas comuns do compartilhamento de dados do Azure. 

## <a name="azure-data-share-invitations"></a>Convites do Azure Data Share 

Em alguns casos, quando um novo usuário clica em **Aceitar Convite** no convite de email enviado, pode ser apresentada a ele uma lista de convites vazia. 

![Nenhum convite](media/no-invites.png)

O erro acima é um problema conhecido com o serviço e está sendo tratado. Como solução alternativa, siga as etapas abaixo. 

1. No portal do Azure, navegue até **Assinaturas**
1. Selecione a assinatura que você está usando para o Azure Data Share
1. Clique em **Provedores de Recursos**
1. Pesquise Microsoft.DataShare
1. Clique em **Registrar**

Você precisará ter a [função de RBAC do Colaborador do Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) para concluir estas etapas. 

Se ainda não vir um convite para compartilhamento de dados, entre em contato com seu provedor de dados e verifique se ele enviou o convite para seu endereço de email de logon do Azure, e *não* para seu alias de email. 

> [!IMPORTANT]
> Se você já tiver aceitado um convite do Azure Data Share e saído do serviço antes de configurar o armazenamento, siga as instruções detalhadas no guia de instruções [Configurar um mapeamento de conjunto de dados](how-to-configure-mapping.md) para saber como concluir a configuração de seu compartilhamento de dados recebido e começar a receber dados. 

## <a name="error-when-creating-or-receiving-a-new-data-share"></a>Erro ao criar ou receber um novo Data Share

"Erro: a operação retornou um código de status inválido ' BadRequest '"

"Erro: AuthorizationFailed"

"Erro: atribuição de função à conta de armazenamento"

![Erro de privilégio](media/error-write-privilege.png)

Se você receber qualquer um dos erros acima ao criar um novo compartilhamento de dados ou receber um novo compartilhamento de dados, haverá permissões insuficientes para a conta de armazenamento. A permissão necessária é *Microsoft.Authorization/atribuições de função/gravação*, que existe na função de proprietário de armazenamento ou pode ser atribuída a uma função personalizada. Mesmo que você tenha criou a conta de armazenamento, isso NÃO o torna automaticamente o proprietário dessa conta. Siga estas etapas para permitir a si mesmo a função de proprietário da conta de armazenamento. Como alternativa, uma função personalizada pode ser criada com essa permissão à qual você pode adicionar a si próprio.  

1. Vá até a conta de Armazenamento no portal do Azure
1. Selecione **Controle de Acesso (IAM)**
1. Clique em **Adicionar**
1. Adicione a si mesmo como proprietário.

## <a name="troubleshooting-sql-based-sharing"></a>Solucionando problemas de compartilhamento baseado em SQL

"Erro: os conjuntos de valores x não foram adicionados porque você não tem as permissões necessárias para compartilhar".

Se você receber esse erro ao adicionar um conjunto de dados de uma fonte baseada em SQL, talvez seja porque você não criou um usuário para o Azure data share MSI no seu SQL Server.  Para resolver esse problema, execute o seguinte script:

```sql
    create user <share_acct_name> from external provider;     
    exec sp_addrolemember db_owner, <share_acct_name>; 
```      
Observe que o *< share_acc_name >* é o nome da sua conta de compartilhamento de dados. Se você ainda não criou uma conta de compartilhamento de dados, poderá voltar para esse pré-requisito posteriormente.         

Verifique se você seguiu todos os pré-requisitos listados no tutorial [compartilhar seus dados](share-your-data.md) .

## <a name="next-steps"></a>Próximas etapas

Para saber como iniciar o compartilhamento de dados, continue com o tutorial sobre como [compartilhar seus dados](share-your-data.md).

