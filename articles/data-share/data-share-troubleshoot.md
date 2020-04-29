---
title: Solucionar problemas do Azure Data Share
description: Saiba como solucionar problemas com convites e erros ao criar ou receber compartilhamentos de dados com o compartilhamento de dados do Azure.
services: data-share
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: troubleshooting
ms.date: 07/10/2019
ms.openlocfilehash: 901f2b56bc045dc9a9837dd18b2e6ce7169aa3b9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "76964219"
---
# <a name="troubleshoot-common-issues-in-azure-data-share"></a>Solucionar problemas comuns no compartilhamento de dados do Azure 

Este artigo mostra como solucionar problemas comuns do compartilhamento de dados do Azure. 

## <a name="azure-data-share-invitations"></a>Convites do Azure Data Share 

Em alguns casos, quando um novo usuário clica em **Aceitar Convite** no convite de email enviado, pode ser apresentada a ele uma lista de convites vazia. 

![Nenhum convite](media/no-invites.png)

Isso pode ser devido aos seguintes motivos:

* **O serviço de compartilhamento de dados do Azure não está registrado como um provedor de recursos de nenhuma assinatura do Azure no locatário do Azure.** Você passará por esse problema se não houver nenhum recurso de compartilhamento de dados em seu locatário do Azure. Quando você cria um recurso de compartilhamento de dados do Azure, ele registra automaticamente o provedor de recursos em sua assinatura do Azure. Você também pode registrar manualmente o serviço de compartilhamento de dados seguindo estas etapas. Você precisará ter a função de colaborador do Azure para concluir essas etapas.

    1. No portal do Azure, navegue até **Assinaturas**
    1. Selecione a assinatura que você deseja usar para criar o recurso de compartilhamento de dados do Azure
    1. Clique em **Provedores de Recursos**
    1. Procurar **Microsoft. DataShare**
    1. Clique em **Registrar** 

    Você precisará ter a [função de RBAC do Colaborador do Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) para concluir estas etapas. 

* **O convite é enviado para seu alias de email em vez de seu email de logon do Azure.** Se você registrou o serviço de compartilhamento de dados do Azure ou já criou um recurso de compartilhamento de dados no locatário do Azure, mas ainda não consegue ver o convite, talvez porque o provedor tenha inserido seu alias de email como destinatário, em vez de seu endereço de email de logon do Azure. Entre em contato com seu provedor de dados e verifique se eles enviaram o convite para seu endereço de email de logon do Azure e não seu alias de email.

* **O convite já foi aceito.** O link no email leva você para a página de convite de compartilhamento de dados no portal do Azure, que lista apenas os convites pendentes. Se você já tiver aceitado o convite, ele não aparecerá mais na página de convite de compartilhamento de dados. Prossiga para o recurso de compartilhamento de dados que você usou para aceitar o convite no para exibir os compartilhamentos recebidos e configurar sua configuração de cluster de Data Explorer do Azure de destino.

## <a name="error-when-creating-or-receiving-a-new-share"></a>Erro ao criar ou receber um novo compartilhamento

"Falha ao adicionar conjuntos de itens"

"Falha ao mapear conjuntos de itens"

"Não é possível conceder ao recurso de compartilhamento de dados o acesso x para y"

"Você não tem as permissões adequadas para x"

"Não foi possível adicionar permissões de gravação para a conta do compartilhamento de dados do Azure a um ou mais dos seus recursos selecionados"

Se você receber qualquer um dos erros acima ao criar um novo compartilhamento ou mapear conjuntos de dados, isso pode ser devido a permissões insuficientes para o armazenamento do Azure Data. Consulte [funções e requisitos](concepts-roles-permissions.md) para obter as permissões necessárias. 

Você precisa de permissão de gravação para compartilhar ou receber dados de um armazenamento de dados do Azure, que normalmente existe na função colaborador. 

Se esta for a primeira vez que você está compartilhando ou recebendo dados do armazenamento de dados do Azure, você também precisa da permissão *Microsoft. Authorization/role/Write* , que normalmente existe na função Owner. Mesmo que você tenha criado o recurso de armazenamento de dados do Azure, ele não o torna automaticamente o proprietário do recurso. Com a permissão adequada, o serviço de compartilhamento de dados do Azure concede automaticamente ao armazenamento de dados acesso de identidade gerenciada do recurso de compartilhamento de dados. Esse processo pode levar alguns minutos para entrar em vigor. Se você tiver uma falha devido a esse atraso, tente novamente em alguns minutos.

O compartilhamento baseado em SQL requer permissões adicionais. Consulte Solucionando problemas de compartilhamento baseado em SQL para obter detalhes.

## <a name="troubleshooting-sql-based-sharing"></a>Solucionando problemas de compartilhamento baseado em SQL

"O usuário x não existe no banco de dados SQL"

Se você receber esse erro ao adicionar um conjunto de dados a partir de uma fonte baseada em SQL, talvez seja porque você não criou um usuário para a identidade gerenciada do Azure data share em seu SQL Server.  Para resolver esse problema, execute o seguinte script:

```sql
    create user "<share_acct_name>" from external provider; 
    exec sp_addrolemember db_datareader, "<share_acct_name>";
```      
Se você receber esse erro ao mapear o conjunto de dados para um destino baseado em SQL, talvez seja porque você não criou um usuário para a identidade gerenciada do compartilhamento de data do Azure em seu SQL Server.  Para resolver esse problema, execute o seguinte script:

```sql
    create user "<share_acc_name>" from external provider; 
    exec sp_addrolemember db_datareader, "<share_acc_name>"; 
    exec sp_addrolemember db_datawriter, "<share_acc_name>"; 
    exec sp_addrolemember db_ddladmin, "<share_acc_name>";
```
Observe que *<share_acc_name>* é o nome do seu recurso do Data Share.      

Verifique se você seguiu todos os pré-requisitos listados no tutorial [compartilhar seus dados](share-your-data.md) e [aceitar e receber dados](subscribe-to-data-share.md) .

## <a name="snapshot-failed"></a>Falha no instantâneo
O instantâneo pode falhar por vários motivos. Você pode encontrar uma mensagem de erro detalhada clicando na hora de início do instantâneo e no status de cada conjunto de informações. 

Se a mensagem de erro estiver relacionada à permissão, verifique se o serviço de compartilhamento de dados tem a permissão necessária. Consulte [funções e requisitos](concepts-roles-permissions.md) para obter detalhes. Se esta for a primeira vez que você está fazendo um instantâneo, pode levar alguns minutos para que o recurso de compartilhamento de dados receba acesso ao armazenamento de dados do Azure. Aguarde alguns minutos e tente novamente.

## <a name="next-steps"></a>Próximas etapas

Para saber como iniciar o compartilhamento de dados, continue com o tutorial sobre como [compartilhar seus dados](share-your-data.md). 

Para saber como receber dados, continue no tutorial [aceitar e receber dados](subscribe-to-data-share.md) .

