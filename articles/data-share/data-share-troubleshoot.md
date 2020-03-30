---
title: Solucionar problemas do Azure Data Share
description: Saiba como solucionar problemas com convites e erros ao criar ou receber compartilhamentos de dados com o Azure Data Share.
services: data-share
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: troubleshooting
ms.date: 07/10/2019
ms.openlocfilehash: 901f2b56bc045dc9a9837dd18b2e6ce7169aa3b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76964219"
---
# <a name="troubleshoot-common-issues-in-azure-data-share"></a>Solucionando problemas comuns no Compartilhamento de Dados do Azure 

Este artigo mostra como solucionar problemas comuns para o Azure Data Share. 

## <a name="azure-data-share-invitations"></a>Convites do Azure Data Share 

Em alguns casos, quando um novo usuário clica em **Aceitar Convite** no convite de email enviado, pode ser apresentada a ele uma lista de convites vazia. 

![Nenhum convite](media/no-invites.png)

Isso pode ser devido às seguintes razões:

* **O serviço Azure Data Share não é registrado como um provedor de recursos de qualquer assinatura do Azure no inquilino do Azure.** Você experimentará esse problema se não houver nenhum recurso de Compartilhamento de Dados no seu inquilino do Azure. Quando você cria um recurso do Azure Data Share, ele registra automaticamente o provedor de recursos em sua assinatura do Azure. Você também pode registrar manualmente o serviço Data Share seguindo essas etapas. Você precisará ter a função Contribuinte Azure para completar essas etapas.

    1. No portal do Azure, navegue até **Assinaturas**
    1. Selecione a assinatura que deseja usar para criar o recurso Azure Data Share
    1. Clique em **Provedores de Recursos**
    1. Pesquisar por **Microsoft.DataShare**
    1. Clique **em Registrar** 

    Você precisará ter a [função de RBAC do Colaborador do Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) para concluir estas etapas. 

* **O convite é enviado para o seu alias de e-mail em vez do seu e-mail de login do Azure.** Se você registrou o serviço Azure Data Share ou já criou um recurso de Compartilhamento de Dados no inquilino do Azure, mas ainda não pode ver o convite, talvez porque o provedor inseriu seu alias de e-mail como destinatário em vez do seu endereço de e-mail de login do Azure. Entre em contato com seu provedor de dados e certifique-se de que eles enviaram o convite para o seu endereço de e-mail de login do Azure e não para seu pseudônimo de e-mail.

* **O convite já foi aceito.** O link no e-mail leva você à página Data Share Invitation no portal Azure, que lista apenas convites pendentes. Se você já aceitou o convite, ele não aparecerá mais na página convite de compartilhamento de dados. Proceda para o recurso Data Share que você usou para aceitar o convite para exibir os compartilhamentos recebidos e configurar a configuração de cluster do Azure Data Explorer de destino.

## <a name="error-when-creating-or-receiving-a-new-share"></a>Erro ao criar ou receber uma nova parte

"Falha ao adicionar conjuntos de dados"

"Falha no mapa de conjuntos de dados"

"Incapaz de conceder recurso de compartilhamento de dados x acesso a y"

"Você não tem permissões adequadas para x"

"Não foi possível adicionar permissões de gravação para a conta Do Compartilhamento de Dados do Azure a um ou mais de seus recursos selecionados"

Se você receber qualquer um dos erros acima ao criar um novo compartilhamento ou mapear conjuntos de dados, pode ser devido a permissões insuficientes para o armazenamento de dados do Azure. Consulte [Funções e requisitos](concepts-roles-permissions.md) para permissões necessárias. 

Você precisa de permissão de gravação para compartilhar ou receber dados de um armazenamento de dados do Azure, que normalmente existe na função Contribuinte. 

Se esta é a primeira vez que você está compartilhando ou recebendo dados do armazenamento de dados do Azure, você também precisa da *permissão Microsoft.Authorization/role assignments/write,* que normalmente existe na função Proprietário. Mesmo que você tenha criado o recurso do armazenamento de dados do Azure, ele NÃO faz de você automaticamente o proprietário do recurso. Com a permissão adequada, o serviço Azure Data Share concede automaticamente o acesso gerenciado de identidade do recurso de compartilhamento de dados ao armazenamento de dados. Esse processo pode levar alguns minutos para fazer efeito. Se você sentir o fracasso devido a este atraso, tente novamente em poucos minutos.

O compartilhamento baseado em SQL requer permissões adicionais. Consulte O compartilhamento baseado em SQL para obter detalhes.

## <a name="troubleshooting-sql-based-sharing"></a>Solucionando problemas de compartilhamento baseado em SQL

"O usuário x não existe no banco de dados SQL"

Se você receber esse erro ao adicionar um conjunto de dados de uma fonte baseada em SQL, pode ser porque você não criou um usuário para a identidade gerenciada do Azure Data Share no seu SQL Server.  Para resolver esse problema, execute o seguinte script:

```sql
    create user "<share_acct_name>" from external provider; 
    exec sp_addrolemember db_datareader, "<share_acct_name>";
```      
Se você receber esse erro ao mapear o conjunto de dados para um destino baseado em SQL, pode ser porque você não criou um usuário para a identidade gerenciada do Azure Data Share no seu SQL Server.  Para resolver esse problema, execute o seguinte script:

```sql
    create user "<share_acc_name>" from external provider; 
    exec sp_addrolemember db_datareader, "<share_acc_name>"; 
    exec sp_addrolemember db_datawriter, "<share_acc_name>"; 
    exec sp_addrolemember db_ddladmin, "<share_acc_name>";
```
Observe que *<share_acc_name>* é o nome do seu recurso do Data Share.      

Certifique-se de que você seguiu todos os pré-requisitos listados no [Compartilhar seus dados](share-your-data.md) e aceitar e receber tutoriais de [dados.](subscribe-to-data-share.md)

## <a name="snapshot-failed"></a>Falha no snapshot
O snapshot pode falhar devido a uma variedade de razões. Você pode encontrar uma mensagem de erro detalhada clicando na hora inicial do snapshot e, em seguida, no status de cada conjunto de dados. 

Se a mensagem de erro estiver relacionada à permissão, verifique se o serviço Data Share tem a permissão necessária. Consulte [Funções e requisitos](concepts-roles-permissions.md) para obter detalhes. Se esta é a primeira vez que você está tirando um instantâneo, pode levar alguns minutos para que o recurso Data Share tenha acesso ao armazenamento de dados do Azure. Espere alguns minutos e tente de novo.

## <a name="next-steps"></a>Próximas etapas

Para saber como iniciar o compartilhamento de dados, continue com o tutorial sobre como [compartilhar seus dados](share-your-data.md). 

Para saber como receber dados, continue a aceitar e receber o tutorial [de dados.](subscribe-to-data-share.md)

