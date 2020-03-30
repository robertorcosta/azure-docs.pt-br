---
title: Funções e requisitos do Azure Data Share
description: Conheça as permissões necessárias para compartilhar e receber dados usando o Azure Data Share.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 36a492f6a3e86cfb2fc9505550cc2d9f4746e070
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265500"
---
# <a name="roles-and-requirements-for-azure-data-share"></a>Funções e requisitos do Azure Data Share 

Este artigo descreve funções e permissões necessárias para compartilhar e receber dados usando o serviço Azure Data Share. 

## <a name="roles-and-requirements"></a>Requisitos e funções

Com o serviço Azure Data Share, você pode compartilhar dados sem trocar credenciais entre provedor de dados e consumidor. O serviço Azure Data Share usa identidades gerenciadas (anteriormente conhecidas como MSIs) para autenticar no armazenamento de dados do Azure. 

A identidade gerenciada do recurso Azure Data Share precisa ter acesso ao armazenamento de dados do Azure. O serviço Azure Data Share usa essa identidade gerenciada para ler e gravar dados para compartilhamento baseado em instantâneos e para estabelecer um link simbólico para compartilhamento no local. 

Para compartilhar ou receber dados de um armazenamento de dados do Azure, o usuário precisa pelo menos das seguintes permissões. Permissões adicionais são necessárias para compartilhamento baseado em SQL.
* Permissão para escrever no armazenamento de dados do Azure. Normalmente, essa permissão existe na função **Contribuinte.**
* Permissão para criar atribuição de função no armazenamento de dados do Azure. Normalmente, existe permissão para criar atribuições de função na função **Proprietário,** função administradora de acesso do usuário ou uma função personalizada com a Permissão microsoft.Authorization/roleassignments/write atribuída. Essa permissão não é necessária se a identidade gerenciada do recurso de compartilhamento de dados já tiver acesso ao armazenamento de dados do Azure. Veja a tabela abaixo para a função necessária.

Abaixo está um resumo das funções atribuídas à identidade gerenciada do recurso Data Share:

| |  |  |
|---|---|---|
|**Tipo de armazenamento de dados**|**Data Provider Source Data Store**|**Data Consumer Target Data Store**|
|Armazenamento do Blobs do Azure| Leitor de Dados do Blob de Armazenamento | Colaborador de dados de blob de armazenamento
|Azure Data Lake Gen1 | Proprietário | Sem suporte
|Azure Data Lake Gen2 | Leitor de Dados do Blob de Armazenamento | Colaborador de dados de blob de armazenamento
|Azure SQL Server | Colaborador do banco de dados SQL | Colaborador do banco de dados SQL
|Azure Data Explorer Cluster | Colaborador | Colaborador
|

Para compartilhamento baseado em SQL, um usuário SQL precisa ser criado a partir de um provedor externo no banco de dados SQL com o mesmo nome do recurso Azure Data Share. Abaixo está um resumo da permissão exigida pelo usuário SQL.

| |  |  |
|---|---|---|
|**Tipo de banco de dados SQL**|**Permissão do usuário sql do provedor de dados**|**Permissão do usuário sql do consumidor de dados**|
|Banco de Dados SQL do Azure | db_datareader | db_datareader, db_datawriter, db_ddladmin
|Azure Synapse Analytics (antigo SQL DW) | db_datareader | db_datareader, db_datawriter, db_ddladmin
|


### <a name="data-provider"></a>Provedor de dados 
Para adicionar um conjunto de dados no Azure Data Share, a identidade gerenciada do recurso de compartilhamento de dados do provedor precisa ter acesso ao armazenamento de dados azure de origem. Por exemplo, no caso da conta de armazenamento, a identidade gerenciada do recurso de compartilhamento de dados é concedida à função De leitor de dados do Storage Blob. 

Isso é feito automaticamente pelo serviço Azure Data Share quando o usuário está adicionando conjunto de dados através do portal Azure e o usuário tem a permissão adequada. Por exemplo, o usuário é proprietário do armazenamento de dados do Azure ou é membro de uma função personalizada que tem a permissão Microsoft.Authorization/role assignments/write atribuída. 

Alternativamente, o usuário pode ter o proprietário do armazenamento de dados do Azure adicionar a identidade gerenciada do recurso de compartilhamento de dados ao armazenamento de dados do Azure manualmente. Essa ação só precisa ser realizada uma vez por recurso de compartilhamento de dados.

Para criar uma atribuição de função para a identidade gerenciada do recurso de compartilhamento de dados, siga as etapas abaixo:

1. Navegue até o armazenamento de dados do Azure.
1. Selecione **controle de acesso (IAM)**.
1. Selecione **Adicionar uma atribuição de função**.
1. Em *Função,* selecione a função na tabela de atribuição de função acima (por exemplo, para conta de armazenamento, selecione *Leitor de dados blob de armazenamento*).
1. Em *Select,* digite o nome do recurso Azure Data Share.
1. Clique em *Salvar*.

Para fontes baseadas em SQL, além das etapas acima, um usuário SQL precisa ser criado a partir de um provedor externo no banco de dados SQL com o mesmo nome do recurso Azure Data Share. Este usuário precisa ser concedido *db_datareader* permissão. Um script de exemplo, juntamente com outros pré-requisitos para compartilhamento baseado em SQL, pode ser encontrado no [tutorial de compartilhamento de seus dados.](share-your-data.md) 

### <a name="data-consumer"></a>Consumidor de dados
Para receber dados, a identidade gerenciada do recurso de compartilhamento de dados do consumidor precisa ter acesso ao armazenamento de dados alvo do Azure. Por exemplo, no caso da conta de armazenamento, a identidade gerenciada do recurso de compartilhamento de dados é concedida à função De contribuinte de dados do Blob de armazenamento. 

Isso é feito automaticamente pelo serviço Azure Data Share se o usuário especificar um armazenamento de dados alvo através do portal Azure e o usuário tiver a permissão adequada. Por exemplo, o usuário é proprietário do armazenamento de dados do Azure ou é membro de uma função personalizada que tem a permissão Microsoft.Authorization/role assignments/write atribuída. 

Alternativamente, o usuário pode ter o proprietário do armazenamento de dados do Azure adicionar a identidade gerenciada do recurso de compartilhamento de dados ao armazenamento de dados do Azure manualmente. Essa ação só precisa ser realizada uma vez por recurso de compartilhamento de dados.

Para criar uma atribuição de função para a identidade gerenciada do recurso de compartilhamento de dados manualmente, siga as etapas abaixo:

1. Navegue até o armazenamento de dados do Azure.
1. Selecione **controle de acesso (IAM)**.
1. Selecione **Adicionar uma atribuição de função**.
1. Em *Função,* selecione a função na tabela de atribuição de função acima (por exemplo, para conta de armazenamento, selecione *Leitor de dados blob de armazenamento*).
1. Em *Select,* digite o nome do recurso Azure Data Share.
1. Clique em *Salvar*.

Para o destino baseado em SQL, além das etapas acima, um usuário SQL precisa ser criado a partir de um provedor externo no banco de dados SQL com o mesmo nome do recurso Azure Data Share. Este usuário precisa ser concedido *db_datareader, db_datawriter, db_ddladmin* permissão. Um script de exemplo, juntamente com outros pré-requisitos para compartilhamento baseado em SQL, pode ser encontrado no tutorial [de aceitação e recebimento de dados.](subscribe-to-data-share.md) 

Se você estiver compartilhando dados usando APIs REST, você precisa criar essas atribuições de função manualmente. 

Para saber mais sobre como adicionar uma atribuição de função, consulte [esta documentação,](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#add-a-role-assignment). 

## <a name="resource-provider-registration"></a>Registro de provedor de recursos 

Para ver o convite do Azure Data Share pela primeira vez em seu inquilino do Azure, talvez seja necessário registrar manualmente o provedor de recursos Microsoft.DataShare em sua assinatura do Azure. Siga essas etapas para registrar o provedor de recursos Microsoft.DataShare em sua Assinatura Azure. Você precisa do acesso *do Contribuinte* à assinatura do Azure para registrar o provedor de recursos.

1. No portal Azure, navegue até **assinaturas**.
1. Selecione a assinatura que você está usando para o Azure Data Share.
1. Clique em **Provedores de recursos**.
1. Procure o Microsoft.DataShare.
1. Clique em **Registrar**.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre as funções no Azure – [Entender as definições de função](../role-based-access-control/role-definitions.md)

