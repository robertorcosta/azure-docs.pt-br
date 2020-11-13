---
title: Solucionar problemas do Azure Data Share
description: Saiba como solucionar problemas com convites e erros ao criar ou receber compartilhamentos de dados com o compartilhamento de dados do Azure.
services: data-share
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: troubleshooting
ms.date: 10/15/2020
ms.openlocfilehash: e29c640494a18bb3be2125a5b53b4f943521fe6c
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/13/2020
ms.locfileid: "94579140"
---
# <a name="troubleshoot-common-issues-in-azure-data-share"></a>Solucionar problemas comuns no Azure Data Share 

Este artigo mostra como solucionar problemas comuns do compartilhamento de dados do Azure. 

## <a name="azure-data-share-invitations"></a>Convites do Azure Data Share 

Em alguns casos, quando um novo usuário clica em **aceitar convite** do convite por email enviado, ele pode receber uma lista vazia de convites. 

![Nenhum convite](media/no-invites.png)

Isso pode ocorrer devido aos seguintes motivos:

* **O serviço do Azure Data Share não foi registrado como provedor de recursos das assinaturas do Azure no locatário do Azure.** Você enfrentará esse problema se o recurso Data Share não existir no locatário do Azure. Quando você cria um recurso Azure Data Share, ele registra automaticamente o provedor de recursos na assinatura do Azure. Você também pode registrar manualmente o serviço do Data Share seguindo estas etapas. Você precisará ter a função de Colaborador do Azure para concluir estas etapas.

    1. No portal do Azure, navegue até **Assinaturas**
    1. Selecione a assinatura que você deseja usar para criar o recurso Azure Data Share
    1. Clique em **Provedores de Recursos**
    1. Pesquise **Microsoft.DataShare**
    1. Clique em **Registrar** 

    Você precisará ter a [função de colaborador do Azure](../role-based-access-control/built-in-roles.md#contributor) para a assinatura do Azure para concluir essas etapas. 

* **O convite foi enviado para o alias de email, em vez do email de logon do Azure.** Se você registrou o serviço Azure Data Share ou já criou um recurso Data Share no locatário do Azure, mas ainda não consegue ver o convite, talvez seja porque o provedor inseriu o alias de email como destinatário, em vez do endereço de email de logon do Azure. Entre em contato com o provedor de dados e verifique se ele enviou o convite para o endereço de email de logon do Azure, não para o alias de email.

* **O convite já foi aceito.** O link no email leva você para a página Convite do Data Share no portal do Azure, que lista apenas os convites pendentes. Se você já aceitou o convite, ele não será mais exibido na página Convite do Data Share. Prossiga para o recurso Data Share que você usou para aceitar o convite para exibir os compartilhamentos recebidos e definir a configuração de cluster do Azure Data Explorer de destino.

## <a name="error-when-creating-or-receiving-a-new-share"></a>Erro ao criar ou receber um novo compartilhamento

"Falha ao adicionar conjuntos de itens"

"Falha ao mapear conjuntos de itens"

"Não é possível conceder ao recurso de compartilhamento de dados o acesso x para y"

"Você não tem as permissões adequadas para x"

"Não foi possível adicionar permissões de gravação para a conta do compartilhamento de dados do Azure a um ou mais dos seus recursos selecionados"

Se você receber qualquer um dos erros acima ao criar um novo compartilhamento, Adicionar conjuntos de dados ou mapear conjuntos de dados, isso pode ser devido a permissões insuficientes para o armazenamento do Azure Data. Consulte [funções e requisitos](concepts-roles-permissions.md) para obter as permissões necessárias. 

Você precisa de permissão de gravação para compartilhar ou receber dados de um armazenamento de dados do Azure, que normalmente existe na função **colaborador** . 

Se esta for a primeira vez que você está compartilhando ou recebendo dados do armazenamento de dados do Azure, você também precisa da permissão *Microsoft. Authorization/role/Write* , que normalmente existe na função **Owner** . Mesmo que você tenha criado o recurso de armazenamento de dados do Azure, ele não o torna automaticamente o proprietário do recurso. Com a permissão adequada, o serviço de compartilhamento de dados do Azure concede automaticamente ao armazenamento de dados acesso de identidade gerenciada do recurso de compartilhamento de dados. Esse processo pode levar alguns minutos para entrar em vigor. Se você tiver uma falha devido a esse atraso, tente novamente em alguns minutos.

O compartilhamento baseado em SQL requer permissões adicionais. Consulte [compartilhar de fontes SQL](how-to-share-from-sql.md) para obter uma lista detalhada de pré-requisitos.

## <a name="snapshot-failed"></a>Falha no instantâneo
O instantâneo pode falhar por vários motivos. Você pode encontrar uma mensagem de erro detalhada clicando na hora de início do instantâneo e no status de cada conjunto de informações. Estes são os motivos comuns pelos quais o instantâneo falha:

* O compartilhamento de dados não tem permissão para ler do armazenamento de dados de origem ou gravar no armazenamento de dados de destino. Consulte [funções e requisitos](concepts-roles-permissions.md) para obter os requisitos de permissão detalhados. Se esta for a primeira vez que você está fazendo um instantâneo, pode levar alguns minutos para que o recurso de compartilhamento de dados receba acesso ao armazenamento de dados do Azure. Aguarde alguns minutos e tente novamente.
* A conexão de compartilhamento de dados com o repositório de dados de origem ou de destino está bloqueada pelo firewall.
* O conjunto de dados compartilhado, ou de origem ou de destino, é excluído.

Para fontes SQL, veja a seguir as causas adicionais de falhas de instantâneo. 

* O script SQL de origem ou de destino para conceder a permissão de compartilhamento de dados não é executado. Ou para o banco de dados SQL do Azure ou o Azure Synapse Analytics (anteriormente Azure SQL DW), ele é executado usando a autenticação do SQL em vez de Azure Active Directory autenticação.  
* O armazenamento de dados SQL de origem ou de destino está em pausa.
* Os tipos de dados SQL não são suportados pelo processo de instantâneo ou pelo armazenamento de dados de destino. Consulte [compartilhar de fontes SQL](how-to-share-from-sql.md#supported-data-types) para obter detalhes.
* O armazenamento de dados SQL de origem ou de destino está bloqueado por outros processos. O compartilhamento de dados do Azure não aplica bloqueios ao repositório de dados SQL de origem e de destino. No entanto, os bloqueios existentes no armazenamento de dados SQL de origem e de destino causarão falha de instantâneo.
* A tabela SQL de destino é referenciada por uma restrição FOREIGN KEY. Durante o instantâneo, se existir uma tabela de destino com o mesmo nome, o compartilhamento de dados do Azure descartará a tabela e criará uma nova tabela. Se a tabela SQL de destino for referenciada por uma restrição FOREIGN KEY, a tabela não poderá ser descartada.
* O arquivo CSV de destino é gerado, mas os dados não podem ser lidos no Excel. Isso pode acontecer quando a tabela SQL de origem contém dados com caracteres que não são do inglês. No Excel, selecione a guia "obter dados" e escolha o arquivo CSV, selecione origem do arquivo como 65001: Unicode (UTF-8) e carregar dados.

## <a name="next-steps"></a>Próximas etapas

Para saber como iniciar o compartilhamento de dados, continue com o tutorial sobre como [compartilhar seus dados](share-your-data.md). 

Para saber como receber dados, continue no tutorial [aceitar e receber dados](subscribe-to-data-share.md) .