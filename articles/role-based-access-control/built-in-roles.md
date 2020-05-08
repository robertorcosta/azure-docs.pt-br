---
title: Funções internas do Azure – RBAC do Azure
description: Este artigo descreve as funções internas do Azure para o controle de acesso baseado em função do Azure (RBAC do Azure). Ele lista ações, não ações, dataactions e NotDataActions.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: reference
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 05/04/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: it-pro
ms.openlocfilehash: b5ba4b84fcd9c1722e8ab2f4031ec1551357e406
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82869982"
---
# <a name="azure-built-in-roles"></a>Funções internas do Azure

O Azure [RBAC (controle de acesso baseado em função)](overview.md) do Azure tem várias funções internas do Azure que você pode atribuir a usuários, grupos, entidades de serviço e identidades gerenciadas. Atribuições de função são a maneira de controlar o acesso aos recursos do Azure. Se as funções internas não atenderem às necessidades específicas de sua organização, você poderá criar suas próprias [funções personalizadas do Azure](custom-roles.md).

Este artigo lista as funções internas do Azure, que estão sempre em evolução. Para obter as funções mais recentes, use [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) ou [az role definition list](/cli/azure/role/definition#az-role-definition-list). Se você estiver procurando por funções de administrador para Azure Active Directory (AD do Azure), consulte [permissões de função de administrador no Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md).

## <a name="all"></a>Todos

A tabela a seguir fornece uma breve descrição e a ID exclusiva de cada função interna. Selecione o nome da função para ver a lista `Actions`de `NotActions`, `DataActions`, e `NotDataActions` para cada função. Para obter informações sobre o que essas ações significam e como elas se aplicam aos planos de gerenciamento e de dados, consulte [entender as definições de função do Azure](role-definitions.md).


> [!div class="mx-tableFixed"]
> | Função interna | Descrição | ID |
> | --- | --- | --- |
> | **Geral** |  |  |
> | [Colaborador](#contributor) | Permite que você gerencie tudo, exceto conceder acesso aos recursos. | b24988ac-6180-42a0-ab88-20f7382dd24c |
> | [Proprietário](#owner) | Permite que você gerencie tudo, incluindo acesso aos recursos. | 8e3af657-a8ff-443c-a75c-2fe8c4bcb635 |
> | [Leitor](#reader) | Permite que você veja tudo, mas não faça alterações. | acdd72a7-3385-48ef-bd42-f606fba81ae7 |
> | [Administrador de Acesso do Usuário](#user-access-administrator) | Permite que você gerencie o acesso do usuário aos recursos do Azure. | 18d7d88d-d35e-4fb5-a5c3-7773c20a72d9 |
> | **Compute** |  |  |
> | [Colaborador da máquina virtual clássica](#classic-virtual-machine-contributor) | Permite gerenciar máquinas virtuais clássicas, mas não o acesso a elas, nem à rede virtual ou conta de armazenamento à qual estão conectadas. | d73bb868-a0df-4d4d-bd69-98a00b01fccb |
> | [Logon de administrador da máquina virtual](#virtual-machine-administrator-login) | Máquinas Virtuais do Microsoft Azure no portal e logon como administrador | 1c0163c0-47E6-4577-8991-ea5c82e286e4 |
> | [Colaborador de Máquina Virtual](#virtual-machine-contributor) | Permite gerenciar máquinas virtuais, mas não o acesso a elas, nem à rede virtual ou conta de armazenamento à qual estão conectadas. | 9980e02c-c2be-4d73-94e8-173b1dc7cf3c |
> | [Logon de usuário da máquina virtual](#virtual-machine-user-login) | Visualize as Máquinas Virtuais do Microsoft Azure no portal e faça logon como usuário. | fb879df8-f326-4884-b1cf-06f3ad86be52 |
> | **Rede** |  |  |
> | [Colaborador de ponto de extremidade de CDN](#cdn-endpoint-contributor) | Pode gerenciar pontos de extremidade CDN, mas não pode conceder acesso a outros usuários. | 426e0c7f-0c7e-4658-b36f-ff54d6c29b45 |
> | [Leitor de ponto de extremidade de CDN](#cdn-endpoint-reader) | Pode exibir pontos de extremidade CDN, mas não pode fazer alterações. | 871e35f6-b5c1-49cc-a043-bde969a0f2cd |
> | [Colaborador de perfil de CDN](#cdn-profile-contributor) | Pode gerenciar perfis CDN e seus pontos de extremidade, mas não pode conceder acesso a outros usuários. | ec156ff8-a8d1-4d15-830C-5b80698ca432 |
> | [Leitor de perfis de CDN](#cdn-profile-reader) | Pode exibir perfis CDN e seus pontos de extremidade, mas não pode fazer alterações. | 8f96442b-4075-438f-813d-ad51ab4019af |
> | [Colaborador de rede clássica](#classic-network-contributor) | Permite que você gerencie redes clássicas, mas não acessá-las. | b34d265f-36f7-4a0d-a4d4-e158ca92e90f |
> | [Colaborador de zona DNS](#dns-zone-contributor) | Permite gerenciar zonas DNS e conjuntos de registros no DNS do Azure, mas não permite controlar quem tem acesso a eles. | befefa01-2a29-4197-83a8-272ff33ce314 |
> | [Colaborador de rede](#network-contributor) | Permite gerenciar redes, mas não acessá-las. | 4d97b98b-1d4f-4787-a291-c67834d212e7 |
> | [Colaborador do Gerenciador de Tráfego](#traffic-manager-contributor) | Permite gerenciar perfis do Gerenciador de Tráfego, mas não permite controlar quem tem acesso a eles. | a4b10055-b0c7-44c2-b00f-c7b5b3550cf7 |
> | **Storage** |  |  |
> | [Colaborador de avere](#avere-contributor) | Pode criar e gerenciar um cluster avere vFXT. | 4f8fab4f-1852-4a58-a46a-8eaf358af14a |
> | [Operador avere](#avere-operator) | Usado pelo cluster avere vFXT para gerenciar o cluster | c025889f-8102-4ebf-b32c-fc0c6f0c6bd9 |
> | [Colaborador de backup](#backup-contributor) | Permite que você gerencie o serviço de backup, mas não pode criar cofres e conceder acesso a outros | 5e467623-bb1f-42f4-a55d-6e525e11384b |
> | [Operador de Backup](#backup-operator) | Permite que você gerencie serviços de backup, exceto a remoção de backup, a criação de cofres e o fornecimento de acesso a outras pessoas | 00c29273-979b-4161-815C-10b084fb9324 |
> | [Leitor de Backup](#backup-reader) | Pode exibir serviços de backup, mas não pode fazer alterações | a795c7a0-d4a2-40c1-ae25-d81f01202912 |
> | [Colaborador da conta de armazenamento clássica](#classic-storage-account-contributor) | Permite que você gerencie contas de armazenamento clássico, mas não acessá-las. | 86e8f5dc-a6e9-4c67-9d15-de283e8eac25 |
> | [Função do Serviço de Operador da Chave da Conta de Armazenamento Clássica](#classic-storage-account-key-operator-service-role) | Os Operadores da Chave da Conta de Armazenamento Clássica têm permissão para listar e regenerar chaves nas Contas de Armazenamento Clássicas | 985d6b00-f706-48f5-a6fe-d0ca12fb668d |
> | [Colaborador do Data Box](#data-box-contributor) | Permite que você gerencie tudo sob o serviço Data Box exceto fornecer acesso a outras pessoas. | add466c9-e687-43fc-8d98-dfcf8d720be5 |
> | [Leitor do Data Box](#data-box-reader) | Permite que você gerencie o serviço do Azure Data Box, exceto a ordem de criação ou edição de detalhes do pedido e fornecer acesso a outras pessoas. | 028f4ed7-e2a9-465e-a8f4-9c0ffdfdc027 |
> | [Desenvolvedor do Data Lake Analytics](#data-lake-analytics-developer) | Permite enviar, monitorar e gerenciar seus próprios trabalhos, mas não criar nem excluir contas do Data Lake Analytics. | 47b7735b-770e-4598-a7da-8b91488b4c88 |
> | [Acesso a Dados e Leitor](#reader-and-data-access) | Permite que você exiba tudo, mas não permitirá que exclua ou crie uma conta de armazenamento ou um recurso contido. Ele também permitirá o acesso de leitura/gravação a todos os dados contidos em uma conta de armazenamento por meio de acesso às chaves de conta de armazenamento. | c12c1c16-33a1-487b-954d-41c89c60f349 |
> | [Colaborador da Conta de Armazenamento](#storage-account-contributor) | Permite o gerenciamento de contas de armazenamento. Fornece acesso à chave de conta, que pode ser usada para acessar dados por meio de autorização de chave compartilhada. | 17d1049b-9a84-46fb-8f53-869881c3d3ab |
> | [Função do Serviço de Operador da Chave da Conta de Armazenamento](#storage-account-key-operator-service-role) | Permite listar e regenerar chaves de acesso da conta de armazenamento. | 81a9662b-bebf-436f-a333-f67b29880f12 |
> | [Colaborador de dados de blob de armazenamento](#storage-blob-data-contributor) | Ler, gravar e excluir contêineres e blobs de armazenamento do Azure. Para saber quais ações são necessárias para uma determinada operação de dados, confira [permissões para chamar operações de BLOB e de dados de fila](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | ba92f5b4-2d11-453d-a403-e96b0029c9fe |
> | [Proprietário de Dados do Blob de Armazenamento](#storage-blob-data-owner) | Fornece acesso completo aos dados e contêineres de blob do armazenamento do Azure, incluindo a atribuição de controle de acesso POSIX. Para saber quais ações são necessárias para uma determinada operação de dados, confira [permissões para chamar operações de BLOB e de dados de fila](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | b7e6dc6d-f1e8-4753-8033-0f276bb0955b |
> | [Leitor de Dados do Blob de Armazenamento](#storage-blob-data-reader) | Leia e liste contêineres e blobs de armazenamento do Azure. Para saber quais ações são necessárias para uma determinada operação de dados, confira [permissões para chamar operações de BLOB e de dados de fila](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1 |
> | [Delegador de blob de armazenamento](#storage-blob-delegator) | Obtenha uma chave de delegação de usuário, que pode ser usada para criar uma assinatura de acesso compartilhado para um contêiner ou BLOB que é assinado com as credenciais do Azure AD. Para obter mais informações, consulte [criar uma SAS de delegação de usuário](https://docs.microsoft.com/rest/api/storageservices/create-user-delegation-sas). | db58b8e5-c6ad-4a2a-8342-4190687cbf4a |
> | [Colaborador de compartilhamento SMB de dados de arquivo de armazenamento](#storage-file-data-smb-share-contributor) | Permite o acesso de leitura, gravação e exclusão em arquivos/diretórios nos compartilhamentos de arquivos do Azure. Essa função não tem equivalente interno nos servidores de arquivos do Windows. | 0c867c2a-1d8c-454a-a3db-ab2ea1bdc8bb |
> | [Colaborador elevado de compartilhamento SMB de dados de arquivo de armazenamento](#storage-file-data-smb-share-elevated-contributor) | Permite ler, gravar, excluir e modificar ACLs em arquivos/diretórios nos compartilhamentos de arquivos do Azure. Essa função é equivalente a uma ACL de compartilhamento de arquivos de alteração em servidores de arquivos do Windows. | a7264617-510b-434b-a828-9731dc254ea7 |
> | [Leitor de compartilhamento SMB de dados de arquivo de armazenamento](#storage-file-data-smb-share-reader) | Permite acesso de leitura em arquivos/diretórios nos compartilhamentos de arquivos do Azure. Essa função é equivalente a uma ACL de compartilhamento de arquivos de leitura em servidores de arquivos do Windows. | aba4ae5f-2193-4029-9191-0cb91df5e314 |
> | [Colaborador de dados da fila de armazenamento](#storage-queue-data-contributor) | Ler, gravar e excluir filas do armazenamento do Azure e mensagens da fila. Para saber quais ações são necessárias para uma determinada operação de dados, confira [permissões para chamar operações de BLOB e de dados de fila](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | 974c5e8b-45b9-4653-ba55-5f855dd0fb88 |
> | [Processador de mensagens de dados da fila de armazenamento](#storage-queue-data-message-processor) | Espiar, recuperar e excluir uma mensagem de uma fila de armazenamento do Azure. Para saber quais ações são necessárias para uma determinada operação de dados, confira [permissões para chamar operações de BLOB e de dados de fila](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | 8a0f0c08-91a1-4084-bc3d-661d67233fed |
> | [Remetente da mensagem de dados da fila de armazenamento](#storage-queue-data-message-sender) | Adicionar mensagens a uma fila de armazenamento do Azure. Para saber quais ações são necessárias para uma determinada operação de dados, confira [permissões para chamar operações de BLOB e de dados de fila](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | c6a89b2d-59bc-44d0-9896-0f6e12d7b80a |
> | [Leitor de dados da fila de armazenamento](#storage-queue-data-reader) | Leia e liste as filas do armazenamento do Azure e as mensagens da fila. Para saber quais ações são necessárias para uma determinada operação de dados, confira [permissões para chamar operações de BLOB e de dados de fila](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | 19e7f393-937e-4f77-808e-94535e297925 |
> | **Web** |  |  |
> | [Leitor de dados do Azure Maps](#azure-maps-data-reader) | Concede acesso para ler dados relacionados ao mapa de uma conta do Azure Maps. | 423170ca-a8f6-4b0f-8487-9e4eb8f49bfa |
> | [Colaborador do Serviço de Pesquisa](#search-service-contributor) | Permite gerenciar serviços de pesquisa, mas não acessá-las. | 7ca78c08-252a-4471-8644-bb5ff32d4ba0 |
> | [Colaborador do Plano de Web](#web-plan-contributor) | Permite gerenciar os planos da Web para sites, mas não o acesso a eles. | 2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b |
> | [Colaborador do Site](#website-contributor) | Permite gerenciar sites (não planos da Web), mas não acessá-los. | de139f84-1756-47ae-9be6-808fbbe84772 |
> | **Contêineres** |  |  |
> | [AcrDelete](#acrdelete) | exclusão de ACR | c2f4ef07-c644-48eb-af81-4b1b4947fb11 |
> | [AcrImageSigner](#acrimagesigner) | signatário de imagem ACR | 6cef56e8-d556-48e5-a04f-b8e64114680f |
> | [AcrPull](#acrpull) | acr pull | 7f951dda-4ed3-4680-a7ca-43fe172d538d |
> | [AcrPush](#acrpush) | acr push | 8311e382-0749-4cb8-b61a-304f252e45ec |
> | [AcrQuarantineReader](#acrquarantinereader) | leitor de dados de quarentena acr | cdda3590-29a3-44f6-95f2-9f980659eb04 |
> | [AcrQuarantineWriter](#acrquarantinewriter) | gravador de dados de quarentena acr | c8d4ff99-41c3-41a8-9f60-21dfdad59608 |
> | [Função de Administrador do Cluster do Serviço de Kubernetes do Azure](#azure-kubernetes-service-cluster-admin-role) | Liste a ação de credencial de administrador de cluster. | 0ab0b1a8-8aac-4efd-b8c2-3ee1fb270be8 |
> | [Função de Usuário do Cluster do Serviço de Kubernetes do Azure](#azure-kubernetes-service-cluster-user-role) | Liste a ação de credencial de usuário de cluster. | 4abbcc35-e782-43d8-92c5-2d3f1bd2253f |
> | **Bancos de dados** |  |  |
> | [Função de leitor de conta do Cosmos DB](#cosmos-db-account-reader-role) | Pode ler dados de contas do Azure Cosmos DB. Consulte [Colaborador de conta do DocumentDB](#documentdb-account-contributor) para gerenciar contas do Azure Cosmos DB. | fbdf93bf-df7d-467e-a4d2-9458aa1360c8 |
> | [Operador de Cosmos DB](#cosmos-db-operator) | Permite que você gerencie contas Azure Cosmos DB, mas não acesse os dados nelas. Impede o acesso a chaves de conta e cadeias de conexão. | 230815da-be43-4aae-9cb4-875f7bd000aa |
> | [CosmosBackupOperator](#cosmosbackupoperator) | Pode enviar solicitação de restauração de um banco de dados Cosmos DB ou de um contêiner em uma conta | db7b14f2-5adf-42da-9f96-f2ee17bab5cb |
> | [Colaborador de Conta do DocumentDB](#documentdb-account-contributor) | Pode gerenciar contas do Azure Cosmos DB. O Azure Cosmos DB era anteriormente conhecido como DocumentDB. | 5bd9cd88-fe45-4216-938b-f97437e15450 |
> | [Colaborador do Cache Redis](#redis-cache-contributor) | Permite gerenciar caches Redis, mas não acessá-los. | e0f68234-74aa-48ED-b826-c38b57376e17 |
> | [Colaborador do BD SQL](#sql-db-contributor) | Permite gerenciar Bancos de Dados SQL, mas não acessá-los. Além disso, não é possível gerenciar as políticas relacionadas à segurança ou respectivos servidores SQL pai. | 9b7fa17d-e63e-47b0-bb0a-15c516ac86ec |
> | [Colaborador do SQL Instância Gerenciada](#sql-managed-instance-contributor) | Permite que você gerencie instâncias gerenciadas do SQL e a configuração de rede necessária, mas não pode conceder acesso a outras pessoas. | 4939a1f6-9ae0-4e48-a1e0-f2cbe897382d |
> | [Gerenciador de Segurança do SQL](#sql-security-manager) | Permite você gerenciar as políticas relacionadas à segurança de servidores e bancos de dados SQL, mas não acessá-los. | 056cd41c-7e88-42e1-933e-88ba6a50c9c3 |
> | [Colaborador do SQL Server](#sql-server-contributor) | Permite gerenciar servidores e bancos de dados SQL, mas não acessá-los, nem suas políticas relacionadas à segurança. | 6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437 |
> | **Análise** |  |  |
> | [Proprietário de dados dos hubs de eventos do Azure](#azure-event-hubs-data-owner) | Permite acesso completo aos recursos dos hubs de eventos do Azure. | f526a384-b230-433a-b45c-95f59c4a2dec |
> | [Receptor de dados dos hubs de eventos do Azure](#azure-event-hubs-data-receiver) | Permite o acesso de recebimento aos recursos dos hubs de eventos do Azure. | a638d3c7-ab3a-418d-83e6-5f17a39d4fde |
> | [Remetente de dados dos hubs de eventos do Azure](#azure-event-hubs-data-sender) | Permite o acesso de envio aos recursos dos hubs de eventos do Azure. | 2b629674-e913-4c01-ae53-ef4638d8f975 |
> | [Colaborador da fábrica de dados](#data-factory-contributor) | Cria e gerencia data factories, assim como os recursos filhos neles. | 673868aa-7521-48A0-acc6-0f60742d39f5 |
> | [Limpador de Dados](#data-purger) | Pode limpar os dados de análise | 150f5e0c-0603-4f03-8c7f-cf70034c4e90 |
> | [Operador de cluster HDInsight](#hdinsight-cluster-operator) | Permite que você leia e modifique as configurações do cluster HDInsight. | 61ed4efc-fab3-44fd-b111-e24485cc132a |
> | [Colaborador dos serviços de domínio do HDInsight](#hdinsight-domain-services-contributor) | Pode ler, criar, modificar e excluir operações relacionadas aos serviços de domínio necessárias para o Enterprise Security Package do HDInsight | 8d8d5a11-05d3-4bda-a417-a08778121c7c |
> | [Colaborador do Log Analytics](#log-analytics-contributor) | O Colaborador do Log Analytics pode ler todos os dados de monitoramento e editar as configurações de monitoramento. A edição das configurações de monitoramento inclui a adição da extensão da VM às VMs, leitura das chaves da conta de armazenamento para poder configurar a coleção de logs do Armazenamento do Microsoft Azure, criação e configuração de contas de Automação, adição de soluções e configuração do diagnóstico do Azure em todos os recursos do Azure. | 92aaf0da-9dab-42b6-94a3-d43ce8d16293 |
> | [Leitor do Log Analytics](#log-analytics-reader) | Um Leitor do Log Analytics pode exibir e pesquisar todos os dados de monitoramento além de exibir as configurações de monitoramento, incluindo a exibição da configuração do diagnóstico do Azure em todos os recursos do Azure. | 73c42c96-874c-492b-b04d-ab87d138a893 |
> | **Blockchain** |  |  |
> | [Acesso ao nó de membro Blockchain (visualização)](#blockchain-member-node-access-preview) | Permite o acesso a nós membro Blockchain | 31a002a1-acaf-453e-8a5b-297c9ca1ea24 |
> | **IA + Machine Learning** |  |  |
> | [Colaborador dos Serviços Cognitivos](#cognitive-services-contributor) | Permite criar, ler, atualizar, excluir e gerenciar chaves dos Serviços Cognitivos. | 25fbc0a9-bd7c-42a3-aa1a-3b75d497ee68 |
> | [Leitor de dados de serviços cognitivas (versão prévia)](#cognitive-services-data-reader-preview) | Permite que você leia dados de serviços cognitivas. | b59867f0-fa02-499b-be73-45a86b5b3e1c |
> | [Usuário dos Serviços Cognitivos](#cognitive-services-user) | Permite ler e listar as chaves dos Serviços Cognitivos. | a97b65f3-24c7-4388-baec-2e87135dc908 |
> | **Realidade misturada** |  |  |
> | [Colaborador da conta de âncoras espaciais](#spatial-anchors-account-contributor) | Permite gerenciar âncoras espaciais em sua conta, mas não excluí-las | 8bbe83f1-e2a6-4df7-8cb4-4e04d4e5c827 |
> | [Proprietário da conta das âncoras espaciais](#spatial-anchors-account-owner) | Permite que você gerencie âncoras espaciais em sua conta, incluindo excluí-las | 70bbe301-9835-447d-afdd-19eb3167307c |
> | [Leitor de conta de âncoras espaciais](#spatial-anchors-account-reader) | Permite localizar e ler propriedades de âncoras espaciais em sua conta | 5d51204f-eb77-4b1c-b86a-2ec626c49413 |
> | **Integração** |  |  |
> | [Colaborador de serviço de gerenciamento de API](#api-management-service-contributor) | Pode gerenciar o serviço e as APIs | 312a565d-c81f-4fd8-895a-4e21e48d571c |
> | [Função do operador de serviço de gerenciamento da API](#api-management-service-operator-role) | Pode gerenciar serviços, mas não as APIs | e022efe7-f5ba-4159-bbe4-b44f577e9b61 |
> | [Função de leitor do Serviço de Gerenciamento de API](#api-management-service-reader-role) | Acesso somente leitura ao serviço e APIs | 71522526-b88f-4d52-b57f-d31fc3546d0d |
> | [Proprietário dos dados de configuração do aplicativo](#app-configuration-data-owner) | Permite acesso completo aos dados de configuração do aplicativo. | 5ae67dd6-50cb-40e7-96ff-dc2bfa4b606b |
> | [Leitor de dados de configuração de aplicativo](#app-configuration-data-reader) | Permite acesso de leitura aos dados de configuração do aplicativo. | 516239f1-63e1-4d78-a4de-a74fb236a071 |
> | [Proprietário de dados do barramento de serviço do Azure](#azure-service-bus-data-owner) | Permite acesso completo aos recursos do barramento de serviço do Azure. | 090c5cfd-751d-490a-894a-3ce6f1109419 |
> | [Receptor de dados do barramento de serviço do Azure](#azure-service-bus-data-receiver) | Permite o acesso de recebimento aos recursos do barramento de serviço do Azure. | 4f6d3b9b-027b-4f4c-9142-0e5a2a2247e0 |
> | [Remetente de dados do barramento de serviço do Azure](#azure-service-bus-data-sender) | Permite o acesso de envio aos recursos do barramento de serviço do Azure. | 69a216fc-b8fb-44d8-bc22-1f3c2cd27a39 |
> | [Proprietário de registro do Microsoft Azure Stack](#azure-stack-registration-owner) | Permite que você gerencie registros do Microsoft Azure Stack. | 6f12a6df-dd06-4f3e-bcb1-ce8be600526a |
> | [Colaborador de EventGrid EventSubscription](#eventgrid-eventsubscription-contributor) | Permite que você gerencie operações de assinatura de evento EventGrid. | 428e0ff0-5e57-4d9c-a221-2c70d0e0a443 |
> | [Leitor de EventGrid EventSubscription](#eventgrid-eventsubscription-reader) | Permite que você gerencie operações de assinatura de evento EventGrid. | 2414bbcf-6497-4faf-8c65-045460748405 |
> | [Colaborador de conta do sistemas inteligentes](#intelligent-systems-account-contributor) | Permite gerenciar contas do Intelligent Systems, mas não acessá-las. | 03a6d094-3444-4b3d-88af-7477090a9e5e |
> | [Colaborador de Aplicativo Lógico](#logic-app-contributor) | Permite que você gerencie aplicativos lógicos, mas não altere o acesso a eles. | 87a39d53-fc1b-424a-814c-f7e04687dc9e |
> | [Operador de Aplicativo Lógico](#logic-app-operator) | Permite ler, habilitar e desabilitar aplicativos lógicos, mas não editá-los ou atualizá-los. | 515c2055-d9d4-4321-b1b9-bd0c9a0f79fe |
> | **Identidade** |  |  |
> | [Colaborador de Identidade Gerenciada](#managed-identity-contributor) | Criar, ler, atualizar e excluir a identidade atribuída pelo usuário | e40ec5ca-96e0-45a2-b4ff-59039f2c2b59 |
> | [Operador de Identidade Gerenciada](#managed-identity-operator) | Ler e atribuir identidade atribuída pelo usuário | f1a07417-d97a-45cb-824c-7a7467783830 |
> | **Segurança** |  |  |
> | [Colaborador do Azure Sentinel](#azure-sentinel-contributor) | Colaborador do Azure Sentinel | ab8e14d6-4a74-4a29-9ba8-549422addade |
> | [Leitor do Azure Sentinel](#azure-sentinel-reader) | Leitor do Azure Sentinel | 8d289c81-5878-46d4-8554-54e1e3d8b5cb |
> | [Respondente do Azure Sentinel](#azure-sentinel-responder) | Respondente do Azure Sentinel | 3e150937-b8fe-4cfb-8069-0eaf05ecd056 |
> | [Colaborador de Key Vault](#key-vault-contributor) | Permite gerenciar cofres de chaves, mas não acessá-los. | f25e0fa2-a7c8-4377-a976-54943a77a395 |
> | [Administrador de segurança](#security-admin) | Permissões de exibição e atualização para a central de segurança. Mesmas permissões que a função leitor de segurança e também podem atualizar a política de segurança e ignorar alertas e recomendações. | fb1c8493-542b-48eb-b624-b4c8fea62acd |
> | [Colaborador de avaliação de segurança](#security-assessment-contributor) | Permite enviar avaliações por push para a central de segurança | 612c2aa1-cb24-443b-ac28-3ab7272de6f5 |
> | [Gerenciador de Segurança (Herdado)](#security-manager-legacy) | Esta é uma função herdada. Em vez disso, use o administrador de segurança. | e3d13bf0-dd5a-482e-ba6b-9b8433878d10 |
> | [Leitor de segurança](#security-reader) | Exibir permissões para a central de segurança. Pode exibir recomendações, alertas, uma política de segurança e Estados de segurança, mas não pode fazer alterações. | 39bc4728-0917-49c7-9d2c-d95423bc2eb4 |
> | **DevOps** |  |  |
> | [Usuário do DevTest Labs](#devtest-labs-user) | Permite conectar, iniciar, reiniciar e encerrar as máquinas virtuais no Azure DevTest Labs. | 76283e04-6283-4c54-8f91-bcf1374a3c64 |
> | [Criador de laboratório](#lab-creator) | Permite a você criar, gerenciar e excluir os laboratórios gerenciados nas contas de laboratório do Azure. | b97fb8bc-a8b2-4522-a38b-dd33c7e65ead |
> | **Monitor** |  |  |
> | [Colaborador de componente do Application Insights](#application-insights-component-contributor) | Pode gerenciar os componentes do Application Insights | ae349356-3a1b-4a5e-921d-050484c6347e |
> | [Depurador de Instantâneos do Application Insights](#application-insights-snapshot-debugger) | Concede permissão ao usuário para exibir e baixar os instantâneos de depuração coletados com o Depurador de Instantâneos do Application Insights. Observe que essas permissões não estão incluídas nas funções [Proprietário](#owner) ou [Colaborador](#contributor). Ao conceder aos usuários a função de Depurador de Instantâneos Application Insights, você deve conceder a função diretamente ao usuário. A função não é reconhecida quando adicionada a uma função personalizada. | 08954f03-6346-4c2e-81c0-ec3a5cfae23b |
> | [Colaborador de monitoramento](#monitoring-contributor) | Pode ler todos os dados de monitoramento e editar configurações de monitoramento. Consulte também [Introdução às funções, permissões e segurança com o Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/roles-permissions-security#built-in-monitoring-roles). | 749f88d5-cbae-40b8-bcfc-e573ddc772fa |
> | [Publicador de Métricas de Monitoramento](#monitoring-metrics-publisher) | Habilita a publicação de métricas com base nos recursos do Azure | 3913510d-42f4-4e42-8a64-420c390055eb |
> | [Leitor de monitoramento](#monitoring-reader) | Pode ler todos os dados de monitoramento (métricas, logs, etc). Consulte também [Introdução às funções, permissões e segurança com o Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/roles-permissions-security#built-in-monitoring-roles). | 43d0d8ad-25c7-4714-9337-8ba259a9fe05 |
> | [Colaborador da pasta de trabalho](#workbook-contributor) | Pode salvar pastas de trabalho compartilhadas. | e8ddcd69-c73f-4f9f-9844-4100522f16ad |
> | [Leitor de pasta de trabalho](#workbook-reader) | Pode ler pastas de trabalho. | b279062a-9be3-42a0-92ae-8b3cf002ec4d |
> | **Gerenciamento + governança** |  |  |
> | [Operador do Trabalho de Automação](#automation-job-operator) | Criar e gerenciar trabalhos usando runbooks de Automação. | 4fe576fe-1146-4730-92eb-48519fa6bf9f |
> | [Operador de automação](#automation-operator) | Os Operadores de Automação podem iniciar, interromper, suspender e retomar trabalhos | d3881f73-407a-4167-8283-e981cbba0404 |
> | [Operador de Runbook de Automação](#automation-runbook-operator) | Ler propriedades do Runbook - para poder criar Trabalhos do runbook. | 5fb5aef8-1081-4b8e-bb16-9d5d0385bab5 |
> | [Integração do computador conectado ao Azure](#azure-connected-machine-onboarding) | Pode carregar computadores conectados do Azure. | b64e21ea-ac4e-4cdf-9dc9-5b892992bee7 |
> | [Administrador de recursos do computador conectado do Azure](#azure-connected-machine-resource-administrator) | Pode ler, gravar, excluir e re-integrar computadores conectados do Azure. | cd570a14-e51a-42ad-bac8-bafd67325302 |
> | [Leitor de cobrança](#billing-reader) | Permite o acesso de leitura aos dados de cobrança | fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64 |
> | [Colaborador do Blueprint](#blueprint-contributor) | Pode gerenciar definições de blueprint, mas não as atribuir. | 41077137-E803-4205-871c-5a86e6a753b4 |
> | [Operador do Blueprint](#blueprint-operator) | Pode atribuir plantas publicadas existentes, mas não pode criar novas plantas. Observe que isso só funcionará se a atribuição for feita com uma identidade gerenciada atribuída pelo usuário. | 437d2ced-4a38-4302-8479-ed2bcb43d090 |
> | [Colaborador de gerenciamento de custos](#cost-management-contributor) | Pode exibir os custos e gerenciar a configuração de custo (por exemplo, orçamentos, exportações) | 434105ed-43f6-45c7-a02f-909b2ba83430 |
> | [Leitor do Gerenciamento de Custos](#cost-management-reader) | Pode exibir dados e configuração de custos (por exemplo, orçamentos, exportações) | 72fafb9e-0641-4937-9268-a91bfd8191a3 |
> | [Função colaborador de aplicativo gerenciado](#managed-application-contributor-role) | Permite a criação de recursos de aplicativos gerenciados. | 641177b8-a67a-45b9-a033-47bc880bb21e |
> | [Função do Operador de Aplicativos Gerenciado](#managed-application-operator-role) | Permite que você leia e execute as ações nos recursos de aplicativo gerenciado | c7393b34-138c-406f-901b-d8cf2b17e6ae |
> | [Leitor de aplicativos gerenciados](#managed-applications-reader) | Permite ler os recursos de um aplicativo gerenciado e solicitar acesso JIT. | b9331d33-8a36-4f8c-b097-4f54124fdb44 |
> | [Função de exclusão de atribuição de registro de serviços gerenciados](#managed-services-registration-assignment-delete-role) | Atribuição de registro de serviços gerenciados excluir função permite que o gerenciamento de usuários de locatários exclua a atribuição de registro atribuída ao seu locatário. | 91c1777a-f3dc-4fae-b103-61d183457e46 |
> | [Colaborador do Grupo de Gerenciamento](#management-group-contributor) | Função de Colaborador do Grupo de Gerenciamento | 5d58bcaf-24a5-4b20-bdb6-eed9f69fbe4c |
> | [Leitor do Grupo de Gerenciamento](#management-group-reader) | Função de Leitor do Grupo de Gerenciamento | ac63b705-f282-497d-ac71-919bf39d939d |
> | [Colaborador de Conta APM do New Relic](#new-relic-apm-account-contributor) | Permite que você gerencie contas e aplicativos do Gerenciamento de desempenho de aplicativos da New Relic, mas não tem acesso a eles. | 5d28c62d-5b37-4476-8438-e587778df237 |
> | [Gravador de dados de informações de política (versão prévia)](#policy-insights-data-writer-preview) | Permite acesso de leitura a políticas de recursos e acesso de gravação aos eventos de política de componente de recurso. | 66bb4e9e-b016-4a94-8249-4c0511c2be84 |
> | [Colaborador da política de recurso](#resource-policy-contributor) | Usuários com direitos para criar/modificar a política de recursos, criar tíquete de suporte e ler recursos/hierarquia. | 36243c78-bf99-498c-9df9-86d9f8d28608 |
> | [Colaborador do Site Recovery](#site-recovery-contributor) | Permite gerenciar o serviço do Azure Site Recovery, exceto a criação de cofre e atribuição de função | 6670b86e-a3f7-4917-ac9b-5d6ab1be4567 |
> | [Operador do Site Recovery](#site-recovery-operator) | Permite failover e failback, mas não executa outras operações de gerenciamento do Azure Site Recovery | 494ae006-db33-4328-bf46-533a6560a3ca |
> | [Leitor do Site Recovery](#site-recovery-reader) | Permite visualizar o status do Azure Site Recovery, mas não executar outras operações de gerenciamento | dbaa88c4-0c30-4179-9fb3-46319faa6149 |
> | [Colaborador de solicitação de suporte](#support-request-contributor) | Permite criar e gerenciar Solicitações de Suporte | cfd33db0-3dd1-45e3-aa9d-cdbdf3b6f24e |
> | [Colaborador de marca](#tag-contributor) | Permite que você gerencie marcas em entidades, sem fornecer acesso às próprias entidades. | 4a9ae827-6dc8-4573-8ac7-8239d42aa03f |
> | **Outros** |  |  |
> | [Colaborador do BizTalk](#biztalk-contributor) | Permite gerenciar serviços do BizTalk, mas não acessá-los. | 5e3c6656-6cfa-4708-81fe-0de47ac73342 |
> | [Colaborador de Coleções de Trabalho do Agendador](#scheduler-job-collections-contributor) | Permite gerenciar as coleções de trabalhos do Agendador, mas não acessá-las. | 188a0f2f-5c9e-469b-ae67-2aa5ce574b94 |


## <a name="general"></a>Geral


### <a name="contributor"></a>Colaborador

Permite que você gerencie tudo, exceto conceder acesso aos recursos.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | * | Criar e gerenciar recursos de todos os tipos |
> | **NotActions** |  |
> | Microsoft.Authorization/*/Delete | Excluir funções, atribuições de política, definições de política e definições de conjunto de políticas |
> | Microsoft.Authorization/*/Write | Criar funções, atribuições de função, atribuições de política, definições de política e definições de conjunto de políticas |
> | Microsoft.Authorization/elevateAccess/Action | Concede ao chamador acesso de administrador de acesso do usuário no escopo do locatário |
> | Microsoft.Blueprint/blueprintAssignments/write | Criar ou atualizar quaisquer atribuições de Blueprint |
> | Microsoft.Blueprint/blueprintAssignments/delete | Excluir quaisquer atribuições de Blueprint |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage everything except access to resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
  "name": "b24988ac-6180-42a0-ab88-20f7382dd24c",
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": [
        "Microsoft.Authorization/*/Delete",
        "Microsoft.Authorization/*/Write",
        "Microsoft.Authorization/elevateAccess/Action",
        "Microsoft.Blueprint/blueprintAssignments/write",
        "Microsoft.Blueprint/blueprintAssignments/delete"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="owner"></a>Proprietário

Permite que você gerencie tudo, incluindo acesso aos recursos.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | * | Criar e gerenciar recursos de todos os tipos |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage everything, including access to resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
  "name": "8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="reader"></a>Leitor

Permite que você veja tudo, mas não faça alterações.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | */leitura | Ler recursos de todos os tipos, exceto segredos. |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you view everything, but not make any changes.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
  "name": "acdd72a7-3385-48ef-bd42-f606fba81ae7",
  "permissions": [
    {
      "actions": [
        "*/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="user-access-administrator"></a>Administrador de Acesso do Usuário

Permite que você gerencie o acesso do usuário aos recursos do Azure.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | */leitura | Ler recursos de todos os tipos, exceto segredos. |
> | Microsoft.Authorization/* | Gerenciar autorização |
> | Microsoft.Support/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage user access to Azure resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
  "name": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Authorization/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "User Access Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="compute"></a>Computação


### <a name="classic-virtual-machine-contributor"></a>Colaborador de Máquina Virtual Clássica

Permite gerenciar máquinas virtuais clássicas, mas não o acesso a elas, nem à rede virtual ou conta de armazenamento à qual estão conectadas.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.ClassicCompute/domainNames/* | Criar e gerenciar nomes de domínio de computação clássica |
> | Microsoft.ClassicCompute/virtualMachines/* | Criar e gerenciar máquinas virtuais |
> | Microsoft.ClassicNetwork/networkSecurityGroups/join/action |  |
> | Microsoft.ClassicNetwork/reservedIps/link/action | Vincular um IP reservado |
> | Microsoft.ClassicNetwork/reservedIps/read | Obter os IPs reservados |
> | Microsoft.ClassicNetwork/virtualNetworks/join/action | Ingressar na rede virtual. |
> | Microsoft.ClassicNetwork/virtualNetworks/read | Obter a rede virtual. |
> | Microsoft.ClassicStorage/storageAccounts/disks/read | Retornar o disco da conta de armazenamento. |
> | Microsoft.ClassicStorage/storageAccounts/images/read | Retornar a imagem da conta de armazenamento. (Preterido. Usar 'Microsoft.ClassicStorage/storageAccounts/vmImages') |
> | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Listar as chaves de acesso das contas de armazenamento. |
> | Microsoft.ClassicStorage/storageAccounts/read | Retornar a conta de armazenamento com a conta fornecida. |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
> | Microsoft.Resources/deployments/* | Criar e gerenciar uma implantação |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Support/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage classic virtual machines, but not access to them, and not the virtual network or storage account they're connected to.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/d73bb868-a0df-4d4d-bd69-98a00b01fccb",
  "name": "d73bb868-a0df-4d4d-bd69-98a00b01fccb",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicCompute/domainNames/*",
        "Microsoft.ClassicCompute/virtualMachines/*",
        "Microsoft.ClassicNetwork/networkSecurityGroups/join/action",
        "Microsoft.ClassicNetwork/reservedIps/link/action",
        "Microsoft.ClassicNetwork/reservedIps/read",
        "Microsoft.ClassicNetwork/virtualNetworks/join/action",
        "Microsoft.ClassicNetwork/virtualNetworks/read",
        "Microsoft.ClassicStorage/storageAccounts/disks/read",
        "Microsoft.ClassicStorage/storageAccounts/images/read",
        "Microsoft.ClassicStorage/storageAccounts/listKeys/action",
        "Microsoft.ClassicStorage/storageAccounts/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Classic Virtual Machine Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="virtual-machine-administrator-login"></a>Logon de administrador da Máquina Virtual

Máquinas Virtuais do Microsoft Azure no portal e logon como administrador

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Network/publicIPAddresses/read | Obter uma definição de endereço IP público. |
> | Microsoft.Network/virtualNetworks/read | Obter a definição de rede virtual |
> | Microsoft.Network/loadBalancers/read | Obter uma definição de balanceador de carga |
> | Microsoft.Network/networkInterfaces/read | Obter uma definição de adaptador de rede.  |
> | Microsoft.Compute/virtualMachines/*/read |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | Microsoft.Compute/virtualMachines/login/action | Faça logon em uma máquina virtual como um usuário normal |
> | Microsoft.Compute/virtualMachines/loginAsAdmin/action | Faça logon em uma máquina virtual com os privilégios de administrador do Windows ou de usuário raiz do Linux |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "View Virtual Machines in the portal and login as administrator",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/1c0163c0-47e6-4577-8991-ea5c82e286e4",
  "name": "1c0163c0-47e6-4577-8991-ea5c82e286e4",
  "permissions": [
    {
      "actions": [
        "Microsoft.Network/publicIPAddresses/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/loadBalancers/read",
        "Microsoft.Network/networkInterfaces/read",
        "Microsoft.Compute/virtualMachines/*/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Compute/virtualMachines/login/action",
        "Microsoft.Compute/virtualMachines/loginAsAdmin/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Virtual Machine Administrator Login",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="virtual-machine-contributor"></a>Colaborador de Máquina Virtual

Permite gerenciar máquinas virtuais, mas não o acesso a elas, nem à rede virtual ou conta de armazenamento à qual estão conectadas.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Compute/availabilitySets/* | Criar e gerenciar conjuntos de disponibilidade de computação |
> | Microsoft.Compute/locations/* | Criar e gerenciar locais de computação |
> | Microsoft.Compute/virtualMachines/* | Criar e gerenciar máquinas virtuais |
> | Microsoft.Compute/virtualMachineScaleSets/* | Criar e gerenciar conjuntos de escala de máquina virtual |
> | Microsoft.Compute/disks/write | Criar um novo disco ou atualizar um existente |
> | Microsoft.Compute/disks/read | Obter as propriedades de um disco |
> | Microsoft.Compute/disks/delete | Excluir o disco |
> | Microsoft.DevTestLab/schedules/* |  |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | Microsoft.Network/applicationGateways/backendAddressPools/join/action | Une um pool de endereços de back-end do gateway de aplicativo. Não é possível alertá-lo. |
> | Microsoft.Network/loadBalancers/backendAddressPools/join/action | Une um pool de endereços de back-end do balanceador de carga. Não é possível alertá-lo. |
> | Microsoft.Network/loadBalancers/inboundNatPools/join/action | Une um pool de NAT de entrada do balanceador de carga. Não é possível alertá-lo. |
> | Microsoft.Network/loadBalancers/inboundNatRules/join/action | Une uma regra NAT de entrada do balanceador de carga. Não é possível alertá-lo. |
> | Microsoft.Network/loadBalancers/probes/join/action | Permitir o uso de investigações de um balanceador de carga. Por exemplo, com essa permissão, a propriedade healthProbe do conjunto de dimensionamento de VM pode referenciar a investigação. Não é possível alertá-lo. |
> | Microsoft.Network/loadBalancers/read | Obter uma definição de balanceador de carga |
> | Microsoft.Network/locations/* | Criar e gerenciar locais de rede |
> | Microsoft.Network/networkInterfaces/* | Criar e gerenciar as interfaces de rede |
> | Microsoft.Network/networkSecurityGroups/join/action | Une um grupo de segurança de rede. Não é possível alertá-lo. |
> | Microsoft.Network/networkSecurityGroups/read | Obter uma definição de um grupo de segurança de rede |
> | Microsoft.Network/publicIPAddresses/join/action | Une um endereço IP público. Não é possível alertá-lo. |
> | Microsoft.Network/publicIPAddresses/read | Obter uma definição de endereço IP público. |
> | Microsoft.Network/virtualNetworks/read | Obter a definição de rede virtual |
> | Microsoft.Network/virtualNetworks/subnets/join/action | Une uma rede virtual. Não é possível alertá-lo. |
> | Microsoft.RecoveryServices/locations/* |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write | Criar uma Intenção de Proteção de backup |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/*/read |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | Retornar detalhes do objeto do item protegido |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write | Criar um item protegido de backup |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Retornar todas as políticas de proteção |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/write | Criar uma política de proteção |
> | Microsoft.RecoveryServices/Vaults/read | A operação Obter cofre obtém um objeto que representa o recurso do Azure do tipo 'cofre' |
> | Microsoft.RecoveryServices/Vaults/usages/read | Retornar os detalhes de uso para um cofre dos Serviços de Recuperação. |
> | Microsoft.RecoveryServices/Vaults/write | A operação Criar cofre cria um recurso do Azure do tipo 'cofre' |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
> | Microsoft.Resources/deployments/* | Criar e gerenciar uma implantação |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.SqlVirtualMachine/* |  |
> | Microsoft.Storage/storageAccounts/listKeys/action | Retornar as chaves de acesso da conta de armazenamento especificada. |
> | Microsoft.Storage/storageAccounts/read | Retornar a lista de contas de armazenamento ou obter as propriedades da conta de armazenamento especificada. |
> | Microsoft.Support/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage virtual machines, but not access to them, and not the virtual network or storage account they're connected to.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
  "name": "9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Compute/availabilitySets/*",
        "Microsoft.Compute/locations/*",
        "Microsoft.Compute/virtualMachines/*",
        "Microsoft.Compute/virtualMachineScaleSets/*",
        "Microsoft.Compute/disks/write",
        "Microsoft.Compute/disks/read",
        "Microsoft.Compute/disks/delete",
        "Microsoft.DevTestLab/schedules/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/applicationGateways/backendAddressPools/join/action",
        "Microsoft.Network/loadBalancers/backendAddressPools/join/action",
        "Microsoft.Network/loadBalancers/inboundNatPools/join/action",
        "Microsoft.Network/loadBalancers/inboundNatRules/join/action",
        "Microsoft.Network/loadBalancers/probes/join/action",
        "Microsoft.Network/loadBalancers/read",
        "Microsoft.Network/locations/*",
        "Microsoft.Network/networkInterfaces/*",
        "Microsoft.Network/networkSecurityGroups/join/action",
        "Microsoft.Network/networkSecurityGroups/read",
        "Microsoft.Network/publicIPAddresses/join/action",
        "Microsoft.Network/publicIPAddresses/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/virtualNetworks/subnets/join/action",
        "Microsoft.RecoveryServices/locations/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/*/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/write",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/Vaults/write",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.SqlVirtualMachine/*",
        "Microsoft.Storage/storageAccounts/listKeys/action",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Virtual Machine Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="virtual-machine-user-login"></a>Logon de usuário da Máquina Virtual

Visualize as Máquinas Virtuais do Microsoft Azure no portal e faça logon como usuário.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Network/publicIPAddresses/read | Obter uma definição de endereço IP público. |
> | Microsoft.Network/virtualNetworks/read | Obter a definição de rede virtual |
> | Microsoft.Network/loadBalancers/read | Obter uma definição de balanceador de carga |
> | Microsoft.Network/networkInterfaces/read | Obter uma definição de adaptador de rede.  |
> | Microsoft.Compute/virtualMachines/*/read |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | Microsoft.Compute/virtualMachines/login/action | Faça logon em uma máquina virtual como um usuário normal |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "View Virtual Machines in the portal and login as a regular user.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fb879df8-f326-4884-b1cf-06f3ad86be52",
  "name": "fb879df8-f326-4884-b1cf-06f3ad86be52",
  "permissions": [
    {
      "actions": [
        "Microsoft.Network/publicIPAddresses/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/loadBalancers/read",
        "Microsoft.Network/networkInterfaces/read",
        "Microsoft.Compute/virtualMachines/*/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Compute/virtualMachines/login/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Virtual Machine User Login",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="networking"></a>Rede


### <a name="cdn-endpoint-contributor"></a>Colaborador de ponto de extremidade de CDN

Pode gerenciar pontos de extremidade CDN, mas não pode conceder acesso a outros usuários.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/endpoints/* |  |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | Microsoft.Resources/deployments/* | Criar e gerenciar uma implantação |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Support/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage CDN endpoints, but can't grant access to other users.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/426e0c7f-0c7e-4658-b36f-ff54d6c29b45",
  "name": "426e0c7f-0c7e-4658-b36f-ff54d6c29b45",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cdn/edgenodes/read",
        "Microsoft.Cdn/operationresults/*",
        "Microsoft.Cdn/profiles/endpoints/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CDN Endpoint Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cdn-endpoint-reader"></a>Leitor de ponto de extremidade de CDN

Pode exibir pontos de extremidade CDN, mas não pode fazer alterações.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/endpoints/*/read |  |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | Microsoft.Resources/deployments/* | Criar e gerenciar uma implantação |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Support/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view CDN endpoints, but can't make changes.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/871e35f6-b5c1-49cc-a043-bde969a0f2cd",
  "name": "871e35f6-b5c1-49cc-a043-bde969a0f2cd",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cdn/edgenodes/read",
        "Microsoft.Cdn/operationresults/*",
        "Microsoft.Cdn/profiles/endpoints/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CDN Endpoint Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cdn-profile-contributor"></a>Colaborador de perfil de CDN

Pode gerenciar perfis CDN e seus pontos de extremidade, mas não pode conceder acesso a outros usuários.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/* |  |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | Microsoft.Resources/deployments/* | Criar e gerenciar uma implantação |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Support/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage CDN profiles and their endpoints, but can't grant access to other users.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ec156ff8-a8d1-4d15-830c-5b80698ca432",
  "name": "ec156ff8-a8d1-4d15-830c-5b80698ca432",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cdn/edgenodes/read",
        "Microsoft.Cdn/operationresults/*",
        "Microsoft.Cdn/profiles/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CDN Profile Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cdn-profile-reader"></a>Leitor de perfil de CDN

Pode exibir perfis CDN e seus pontos de extremidade, mas não pode fazer alterações.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/*/read |  |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | Microsoft.Resources/deployments/* | Criar e gerenciar uma implantação |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Support/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view CDN profiles and their endpoints, but can't make changes.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8f96442b-4075-438f-813d-ad51ab4019af",
  "name": "8f96442b-4075-438f-813d-ad51ab4019af",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cdn/edgenodes/read",
        "Microsoft.Cdn/operationresults/*",
        "Microsoft.Cdn/profiles/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CDN Profile Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="classic-network-contributor"></a>Colaborador de rede clássica

Permite que você gerencie redes clássicas, mas não acessá-las.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.ClassicNetwork/* | Criar e gerenciar redes clássicas |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
> | Microsoft.Resources/deployments/* | Criar e gerenciar uma implantação |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Support/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage classic networks, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b34d265f-36f7-4a0d-a4d4-e158ca92e90f",
  "name": "b34d265f-36f7-4a0d-a4d4-e158ca92e90f",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicNetwork/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Classic Network Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="dns-zone-contributor"></a>Colaborador de zona DNS

Permite gerenciar zonas DNS e conjuntos de registros no DNS do Azure, mas não permite controlar quem tem acesso a eles.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | Microsoft.Network/dnsZones/* | Criar e gerenciar zonas e registros DNS |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
> | Microsoft.Resources/deployments/* | Criar e gerenciar uma implantação |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Support/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage DNS zones and record sets in Azure DNS, but does not let you control who has access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/befefa01-2a29-4197-83a8-272ff33ce314",
  "name": "befefa01-2a29-4197-83a8-272ff33ce314",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/dnsZones/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "DNS Zone Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="network-contributor"></a>Colaborador de rede

Permite gerenciar redes, mas não acessá-las.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | Microsoft.Network/* | Criar e gerenciar redes |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
> | Microsoft.Resources/deployments/* | Criar e gerenciar uma implantação |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Support/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage networks, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4d97b98b-1d4f-4787-a291-c67834d212e7",
  "name": "4d97b98b-1d4f-4787-a291-c67834d212e7",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Network Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="traffic-manager-contributor"></a>Colaborador do Gerenciador de Tráfego

Permite gerenciar perfis do Gerenciador de Tráfego, mas não permite controlar quem tem acesso a eles.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | Microsoft.Network/trafficManagerProfiles/* |  |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
> | Microsoft.Resources/deployments/* | Criar e gerenciar uma implantação |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Support/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Traffic Manager profiles, but does not let you control who has access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a4b10055-b0c7-44c2-b00f-c7b5b3550cf7",
  "name": "a4b10055-b0c7-44c2-b00f-c7b5b3550cf7",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/trafficManagerProfiles/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Traffic Manager Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="storage"></a>Armazenamento


### <a name="avere-contributor"></a>Colaborador de avere

Pode criar e gerenciar um cluster avere vFXT.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft. Compute/*/Read |  |
> | Microsoft.Compute/availabilitySets/* |  |
> | Microsoft.Compute/virtualMachines/* |  |
> | Microsoft. Compute/disks/* |  |
> | Microsoft. Network/*/Read |  |
> | Microsoft.Network/networkInterfaces/* |  |
> | Microsoft.Network/virtualNetworks/read | Obter a definição de rede virtual |
> | Microsoft.Network/virtualNetworks/subnets/read | Obter uma definição de sub-rede da rede virtual |
> | Microsoft.Network/virtualNetworks/subnets/join/action | Une uma rede virtual. Não é possível alertá-lo. |
> | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Adicionar recursos como conta de armazenamento ou banco de dados SQL a uma sub-rede. Não é possível alertá-lo. |
> | Microsoft.Network/networkSecurityGroups/join/action | Une um grupo de segurança de rede. Não é possível alertá-lo. |
> | Microsoft.Resources/deployments/* | Criar e gerenciar uma implantação |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft. Storage/*/Read |  |
> | Microsoft.Storage/storageAccounts/* | Criar e gerenciar contas de armazenamento |
> | Microsoft.Support/* | Criar e atualizar um tíquete de suporte |
> | Microsoft. Resources/subscriptions/resourceGroups/Resources/Read | Obter os recursos do grupo de recursos. |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete | Retorna o resultado da exclusão de um blob |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Retorna um blob ou uma lista de blobs |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write | Retorna o resultado da gravação de um blob |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can create and manage an Avere vFXT cluster.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4f8fab4f-1852-4a58-a46a-8eaf358af14a",
  "name": "4f8fab4f-1852-4a58-a46a-8eaf358af14a",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Compute/*/read",
        "Microsoft.Compute/availabilitySets/*",
        "Microsoft.Compute/virtualMachines/*",
        "Microsoft.Compute/disks/*",
        "Microsoft.Network/*/read",
        "Microsoft.Network/networkInterfaces/*",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/virtualNetworks/subnets/read",
        "Microsoft.Network/virtualNetworks/subnets/join/action",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action",
        "Microsoft.Network/networkSecurityGroups/join/action",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/*/read",
        "Microsoft.Storage/storageAccounts/*",
        "Microsoft.Support/*",
        "Microsoft.Resources/subscriptions/resourceGroups/resources/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Avere Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="avere-operator"></a>Operador avere

Usado pelo cluster avere vFXT para gerenciar o cluster

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Compute/virtualMachines/read | Obter as propriedades de uma máquina virtual |
> | Microsoft.Network/networkInterfaces/read | Obter uma definição de adaptador de rede.  |
> | Microsoft.Network/networkInterfaces/write | Criar uma interface de rede ou atualizar uma interface de rede existente.  |
> | Microsoft.Network/virtualNetworks/read | Obter a definição de rede virtual |
> | Microsoft.Network/virtualNetworks/subnets/read | Obter uma definição de sub-rede da rede virtual |
> | Microsoft.Network/virtualNetworks/subnets/join/action | Une uma rede virtual. Não é possível alertá-lo. |
> | Microsoft.Network/networkSecurityGroups/join/action | Une um grupo de segurança de rede. Não é possível alertá-lo. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/delete | Retornar o resultado da exclusão de um contêiner |
> | Microsoft.Storage/storageAccounts/blobServices/containers/read | Retorna a lista de contêineres |
> | Microsoft.Storage/storageAccounts/blobServices/containers/write | Retorna o resultado do contêiner de put blob |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete | Retorna o resultado da exclusão de um blob |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Retorna um blob ou uma lista de blobs |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write | Retorna o resultado da gravação de um blob |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Used by the Avere vFXT cluster to manage the cluster",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c025889f-8102-4ebf-b32c-fc0c6f0c6bd9",
  "name": "c025889f-8102-4ebf-b32c-fc0c6f0c6bd9",
  "permissions": [
    {
      "actions": [
        "Microsoft.Compute/virtualMachines/read",
        "Microsoft.Network/networkInterfaces/read",
        "Microsoft.Network/networkInterfaces/write",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/virtualNetworks/subnets/read",
        "Microsoft.Network/virtualNetworks/subnets/join/action",
        "Microsoft.Network/networkSecurityGroups/join/action",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/write"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Avere Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="backup-contributor"></a>Colaborador de Backup

Permite que você gerencie o serviço de backup, mas não pode criar cofres e conceder acesso a outros

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Network/virtualNetworks/read | Obter a definição de rede virtual |
> | Microsoft.RecoveryServices/locations/* |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/* | Gerenciar os resultados da operação no gerenciamento de backup |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/* | Criar e gerenciar contêineres de backup em malhas de backup do cofre de Serviços de Recuperação |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action | Atualizar a lista de contêineres |
> | Microsoft.RecoveryServices/Vaults/backupJobs/* | Criar e gerenciar trabalhos de backup |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Exportar trabalhos |
> | Microsoft.RecoveryServices/Vaults/backupOperationResults/* | Criar e gerenciar os Resultados das operações de gerenciamento de backup |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/* | Criar e gerenciar políticas de backup |
> | Microsoft.RecoveryServices/Vaults/backupProtectableItems/* | Criar e gerenciar itens para backup |
> | Microsoft.RecoveryServices/Vaults/backupProtectedItems/* | Criar e gerenciar itens submetidos a backup |
> | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/* | Criar e gerenciar contêineres que armazenam itens de backup |
> | Microsoft.RecoveryServices/Vaults/backupSecurityPIN/* |  |
> | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Retornar resumos de itens protegidos e servidores protegidos para os Serviços de Recuperação. |
> | Microsoft.RecoveryServices/Vaults/certificates/* | Criar e gerenciar certificados relacionados a backup em um cofre de Serviços de Recuperação |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/* | Criar e gerenciar informações estendidas relacionadas ao cofre |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Obter os alertas para o cofre dos Serviços de Recuperação. |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/* |  |
> | Microsoft.RecoveryServices/Vaults/read | A operação Obter cofre obtém um objeto que representa o recurso do Azure do tipo 'cofre' |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/* | Criar e gerenciar identidades registradas |
> | Microsoft.RecoveryServices/Vaults/usages/* | Criar e gerenciar o uso do cofre dos Serviços de Recuperação |
> | Microsoft.Resources/deployments/* | Criar e gerenciar uma implantação |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Storage/storageAccounts/read | Retornar a lista de contas de armazenamento ou obter as propriedades da conta de armazenamento especificada. |
> | Microsoft.RecoveryServices/Vaults/backupstorageconfig/* |  |
> | Microsoft.RecoveryServices/Vaults/backupconfig/* |  |
> | Microsoft.RecoveryServices/Vaults/backupValidateOperation/action | Validar operação no Item protegido |
> | Microsoft.RecoveryServices/Vaults/write | A operação Criar cofre cria um recurso do Azure do tipo 'cofre' |
> | Microsoft.RecoveryServices/Vaults/backupOperations/read | Retornar o status da operação de backup para o cofre dos Serviços de Recuperação. |
> | Microsoft.RecoveryServices/Vaults/backupEngines/read | Retornar todos os servidores de gerenciamento de backup registrados com cofre. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/* |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read | Obter todos os contêineres protegidos |
> | Microsoft.RecoveryServices/locations/backupStatus/action | Verificar o status de backup para os Cofres dos Serviços de Recuperação |
> | Microsoft.RecoveryServices/locations/backupPreValidateProtection/action |  |
> | Microsoft.RecoveryServices/locations/backupValidateFeatures/action | Validar recursos |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/write | Resolver o alerta. |
> | Microsoft.RecoveryServices/operations/read | Operação retorna a lista de operações para um provedor de recursos |
> | Microsoft.RecoveryServices/locations/operationStatus/read | Obtém o Status da operação para uma determinada operação |
> | Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read | Listar todas as Intenções de Proteção de backup |
> | Microsoft.Support/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage backup service,but can't create vaults and give access to others",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5e467623-bb1f-42f4-a55d-6e525e11384b",
  "name": "5e467623-bb1f-42f4-a55d-6e525e11384b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.RecoveryServices/locations/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action",
        "Microsoft.RecoveryServices/Vaults/backupJobs/*",
        "Microsoft.RecoveryServices/Vaults/backupJobsExport/action",
        "Microsoft.RecoveryServices/Vaults/backupOperationResults/*",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/*",
        "Microsoft.RecoveryServices/Vaults/backupProtectableItems/*",
        "Microsoft.RecoveryServices/Vaults/backupProtectedItems/*",
        "Microsoft.RecoveryServices/Vaults/backupProtectionContainers/*",
        "Microsoft.RecoveryServices/Vaults/backupSecurityPIN/*",
        "Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read",
        "Microsoft.RecoveryServices/Vaults/certificates/*",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/*",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/read",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/*",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/*",
        "Microsoft.RecoveryServices/Vaults/usages/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.RecoveryServices/Vaults/backupstorageconfig/*",
        "Microsoft.RecoveryServices/Vaults/backupconfig/*",
        "Microsoft.RecoveryServices/Vaults/backupValidateOperation/action",
        "Microsoft.RecoveryServices/Vaults/write",
        "Microsoft.RecoveryServices/Vaults/backupOperations/read",
        "Microsoft.RecoveryServices/Vaults/backupEngines/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read",
        "Microsoft.RecoveryServices/locations/backupStatus/action",
        "Microsoft.RecoveryServices/locations/backupPreValidateProtection/action",
        "Microsoft.RecoveryServices/locations/backupValidateFeatures/action",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/write",
        "Microsoft.RecoveryServices/operations/read",
        "Microsoft.RecoveryServices/locations/operationStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Backup Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="backup-operator"></a>Operador de Backup

Permite que você gerencie serviços de backup, exceto a remoção de backup, a criação de cofres e o fornecimento de acesso a outras pessoas

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Network/virtualNetworks/read | Obter a definição de rede virtual |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | Retornar o status da operação |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read | Obter o resultado da operação executada no contêiner de proteção. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action | Executar um backup para um item protegido. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | Obter o resultado da operação executada em itens protegidos. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | Retornar o status da operação executada em itens protegidos. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | Retornar detalhes do objeto do item protegido |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action | Provisionar recuperação de item instantânea para item protegido |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | Obter pontos de recuperação para itens protegidos. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action | Restaurar pontos de recuperação para itens protegidos. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action | Revogar a recuperação de item instantânea para item protegido |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write | Criar um item protegido de backup |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | Retornar todos os contêineres registrados |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action | Atualizar a lista de contêineres |
> | Microsoft.RecoveryServices/Vaults/backupJobs/* | Criar e gerenciar trabalhos de backup |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Exportar trabalhos |
> | Microsoft.RecoveryServices/Vaults/backupOperationResults/* | Criar e gerenciar os Resultados das operações de gerenciamento de backup |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | Obter os resultados da operação de política. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Retornar todas as políticas de proteção |
> | Microsoft.RecoveryServices/Vaults/backupProtectableItems/* | Criar e gerenciar itens para backup |
> | Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | Retornar a lista de todos os itens protegidos. |
> | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | Retornar todos os contêineres pertencentes à assinatura |
> | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Retornar resumos de itens protegidos e servidores protegidos para os Serviços de Recuperação. |
> | Microsoft.RecoveryServices/Vaults/certificates/write | A operação Atualizar certificado do recurso atualiza o certificado de credencial de cofre/recurso. |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | A operação Obter Informações Estendidas obtém informações estendidas de um objeto que representa o recurso do Azure do tipo ?vault? |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/write | A operação Obter Informações Estendidas obtém informações estendidas de um objeto que representa o recurso do Azure do tipo ?vault? |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Obter os alertas para o cofre dos Serviços de Recuperação. |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/* |  |
> | Microsoft.RecoveryServices/Vaults/read | A operação Obter cofre obtém um objeto que representa o recurso do Azure do tipo 'cofre' |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | A operação Obter resultados da operação pode ser usada para obter o status da operação e o resultado da operação enviada de forma assíncrona |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | A operação Obter contêineres pode ser usada para obter os contêineres registrados para um recurso. |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/write | A operação Registrar o contêiner de serviço pode ser usada para registrar um contêiner com o Serviço de Recuperação. |
> | Microsoft.RecoveryServices/Vaults/usages/read | Retornar os detalhes de uso para um cofre dos Serviços de Recuperação. |
> | Microsoft.Resources/deployments/* | Criar e gerenciar uma implantação |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Storage/storageAccounts/read | Retornar a lista de contas de armazenamento ou obter as propriedades da conta de armazenamento especificada. |
> | Microsoft.RecoveryServices/Vaults/backupstorageconfig/* |  |
> | Microsoft.RecoveryServices/Vaults/backupValidateOperation/action | Validar operação no Item protegido |
> | Microsoft.RecoveryServices/Vaults/backupOperations/read | Retornar o status da operação de backup para o cofre dos Serviços de Recuperação. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read | Obter o status da operação de política. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/write | Criar um contêiner registrado |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/inquire/action | Consultar cargas de trabalho em um contêiner |
> | Microsoft.RecoveryServices/Vaults/backupEngines/read | Retornar todos os servidores de gerenciamento de backup registrados com cofre. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write | Criar uma Intenção de Proteção de backup |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read | Obter uma Intenção de Proteção de backup |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read | Obter todos os contêineres protegidos |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read | Obter todos os itens em um contêiner |
> | Microsoft.RecoveryServices/locations/backupStatus/action | Verificar o status de backup para os Cofres dos Serviços de Recuperação |
> | Microsoft.RecoveryServices/locations/backupPreValidateProtection/action |  |
> | Microsoft.RecoveryServices/locations/backupValidateFeatures/action | Validar recursos |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/write | Resolver o alerta. |
> | Microsoft.RecoveryServices/operations/read | Operação retorna a lista de operações para um provedor de recursos |
> | Microsoft.RecoveryServices/locations/operationStatus/read | Obtém o Status da operação para uma determinada operação |
> | Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read | Listar todas as Intenções de Proteção de backup |
> | Microsoft.Support/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage backup services, except removal of backup, vault creation and giving access to others",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/00c29273-979b-4161-815c-10b084fb9324",
  "name": "00c29273-979b-4161-815c-10b084fb9324",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action",
        "Microsoft.RecoveryServices/Vaults/backupJobs/*",
        "Microsoft.RecoveryServices/Vaults/backupJobsExport/action",
        "Microsoft.RecoveryServices/Vaults/backupOperationResults/*",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectableItems/*",
        "Microsoft.RecoveryServices/Vaults/backupProtectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read",
        "Microsoft.RecoveryServices/Vaults/certificates/write",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/read",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/write",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/read",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/*",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/write",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.RecoveryServices/Vaults/backupstorageconfig/*",
        "Microsoft.RecoveryServices/Vaults/backupValidateOperation/action",
        "Microsoft.RecoveryServices/Vaults/backupOperations/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/write",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/inquire/action",
        "Microsoft.RecoveryServices/Vaults/backupEngines/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read",
        "Microsoft.RecoveryServices/locations/backupStatus/action",
        "Microsoft.RecoveryServices/locations/backupPreValidateProtection/action",
        "Microsoft.RecoveryServices/locations/backupValidateFeatures/action",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/write",
        "Microsoft.RecoveryServices/operations/read",
        "Microsoft.RecoveryServices/locations/operationStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Backup Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="backup-reader"></a>Leitor de Backup

Pode exibir serviços de backup, mas não pode fazer alterações

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp é uma operação interna usada pelo serviço |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | Retornar o status da operação |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read | Obter o resultado da operação executada no contêiner de proteção. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | Obter o resultado da operação executada em itens protegidos. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | Retornar o status da operação executada em itens protegidos. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | Retornar detalhes do objeto do item protegido |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | Obter pontos de recuperação para itens protegidos. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | Retornar todos os contêineres registrados |
> | Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read | Retornar o resultado da operação do trabalho. |
> | Microsoft.RecoveryServices/Vaults/backupJobs/read | Retornar todos os objetos de trabalho |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Exportar trabalhos |
> | Microsoft.RecoveryServices/Vaults/backupOperationResults/read | Retornar o resultado da operação de backup para o cofre dos Serviços de Recuperação. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | Obter os resultados da operação de política. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Retornar todas as políticas de proteção |
> | Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | Retornar a lista de todos os itens protegidos. |
> | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | Retornar todos os contêineres pertencentes à assinatura |
> | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Retornar resumos de itens protegidos e servidores protegidos para os Serviços de Recuperação. |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | A operação Obter Informações Estendidas obtém informações estendidas de um objeto que representa o recurso do Azure do tipo ?vault? |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Obter os alertas para o cofre dos Serviços de Recuperação. |
> | Microsoft.RecoveryServices/Vaults/read | A operação Obter cofre obtém um objeto que representa o recurso do Azure do tipo 'cofre' |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | A operação Obter resultados da operação pode ser usada para obter o status da operação e o resultado da operação enviada de forma assíncrona |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | A operação Obter contêineres pode ser usada para obter os contêineres registrados para um recurso. |
> | Microsoft.RecoveryServices/Vaults/backupstorageconfig/read | Retornar cofre de armazenamento para o cofre dos Serviços de Recuperação. |
> | Microsoft.RecoveryServices/Vaults/backupconfig/read | Retornar a configuração para cofre dos Serviços de Recuperação. |
> | Microsoft.RecoveryServices/Vaults/backupOperations/read | Retornar o status da operação de backup para o cofre dos Serviços de Recuperação. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read | Obter o status da operação de política. |
> | Microsoft.RecoveryServices/Vaults/backupEngines/read | Retornar todos os servidores de gerenciamento de backup registrados com cofre. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read | Obter uma Intenção de Proteção de backup |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read | Obter todos os itens em um contêiner |
> | Microsoft.RecoveryServices/locations/backupStatus/action | Verificar o status de backup para os Cofres dos Serviços de Recuperação |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/* |  |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/write | Resolver o alerta. |
> | Microsoft.RecoveryServices/operations/read | Operação retorna a lista de operações para um provedor de recursos |
> | Microsoft.RecoveryServices/locations/operationStatus/read | Obtém o Status da operação para uma determinada operação |
> | Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read | Listar todas as Intenções de Proteção de backup |
> | Microsoft.RecoveryServices/Vaults/usages/read | Retornar os detalhes de uso para um cofre dos Serviços de Recuperação. |
> | Microsoft.RecoveryServices/locations/backupValidateFeatures/action | Validar recursos |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view backup services, but can't make changes",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a795c7a0-d4a2-40c1-ae25-d81f01202912",
  "name": "a795c7a0-d4a2-40c1-ae25-d81f01202912",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.RecoveryServices/locations/allocatedStamp/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupJobs/read",
        "Microsoft.RecoveryServices/Vaults/backupJobsExport/action",
        "Microsoft.RecoveryServices/Vaults/backupOperationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/read",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/read",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/read",
        "Microsoft.RecoveryServices/Vaults/backupstorageconfig/read",
        "Microsoft.RecoveryServices/Vaults/backupconfig/read",
        "Microsoft.RecoveryServices/Vaults/backupOperations/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read",
        "Microsoft.RecoveryServices/Vaults/backupEngines/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read",
        "Microsoft.RecoveryServices/locations/backupStatus/action",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/*",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/write",
        "Microsoft.RecoveryServices/operations/read",
        "Microsoft.RecoveryServices/locations/operationStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/locations/backupValidateFeatures/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Backup Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="classic-storage-account-contributor"></a>Colaborador da conta de armazenamento clássica

Permite que você gerencie contas de armazenamento clássico, mas não acessá-las.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.ClassicStorage/storageAccounts/* | Criar e gerenciar contas de armazenamento |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
> | Microsoft.Resources/deployments/* | Criar e gerenciar uma implantação |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Support/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage classic storage accounts, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/86e8f5dc-a6e9-4c67-9d15-de283e8eac25",
  "name": "86e8f5dc-a6e9-4c67-9d15-de283e8eac25",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicStorage/storageAccounts/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Classic Storage Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="classic-storage-account-key-operator-service-role"></a>Função do Serviço de Operador da Chave da Conta de Armazenamento Clássica

Os Operadores da Chave da Conta de Armazenamento Clássica têm permissão para listar e regenerar chaves nas Contas de Armazenamento Clássicas

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.ClassicStorage/storageAccounts/listkeys/action | Listar as chaves de acesso das contas de armazenamento. |
> | Microsoft.ClassicStorage/storageAccounts/regeneratekey/action | Regenera as chaves de acesso existentes da conta de armazenamento. |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Classic Storage Account Key Operators are allowed to list and regenerate keys on Classic Storage Accounts",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/985d6b00-f706-48f5-a6fe-d0ca12fb668d",
  "name": "985d6b00-f706-48f5-a6fe-d0ca12fb668d",
  "permissions": [
    {
      "actions": [
        "Microsoft.ClassicStorage/storageAccounts/listkeys/action",
        "Microsoft.ClassicStorage/storageAccounts/regeneratekey/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Classic Storage Account Key Operator Service Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-box-contributor"></a>Colaborador do Data Box

Permite que você gerencie tudo sob o serviço Data Box exceto fornecer acesso a outras pessoas.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
> | Microsoft.Resources/deployments/* | Criar e gerenciar uma implantação |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Support/* | Criar e atualizar um tíquete de suporte |
> | Microsoft.Databox/* |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage everything under Data Box Service except giving access to others.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/add466c9-e687-43fc-8d98-dfcf8d720be5",
  "name": "add466c9-e687-43fc-8d98-dfcf8d720be5",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Databox/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Box Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-box-reader"></a>Leitor do Data Box

Permite que você gerencie o serviço do Azure Data Box, exceto a ordem de criação ou edição de detalhes do pedido e fornecer acesso a outras pessoas.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Databox/*/read |  |
> | Microsoft.Databox/jobs/listsecrets/action |  |
> | Microsoft.Databox/jobs/listcredentials/action | Lista as credenciais não criptografadas relacionadas ao pedido. |
> | Microsoft.Databox/locations/availableSkus/action | Este método retorna a lista de SKUs disponíveis. |
> | Microsoft. Data Box/Locations/validateInputs/Action | Esse método faz todo o tipo de validações. |
> | Microsoft. Data Box/Locations/regionConfiguration/Action | Esse método retorna as configurações para a região. |
> | Microsoft. Data Box/Locations/validateAddress/Action | Validará o endereço de entrega e fornecerá endereços alternativos, se houver algum. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
> | Microsoft.Support/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Data Box Service except creating order or editing order details and giving access to others.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/028f4ed7-e2a9-465e-a8f4-9c0ffdfdc027",
  "name": "028f4ed7-e2a9-465e-a8f4-9c0ffdfdc027",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Databox/*/read",
        "Microsoft.Databox/jobs/listsecrets/action",
        "Microsoft.Databox/jobs/listcredentials/action",
        "Microsoft.Databox/locations/availableSkus/action",
        "Microsoft.Databox/locations/validateInputs/action",
        "Microsoft.Databox/locations/regionConfiguration/action",
        "Microsoft.Databox/locations/validateAddress/action",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Box Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-lake-analytics-developer"></a>Desenvolvedor do Data Lake Analytics

Permite enviar, monitorar e gerenciar seus próprios trabalhos, mas não criar nem excluir contas do Data Lake Analytics.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.BigAnalytics/accounts/* |  |
> | Microsoft.DataLakeAnalytics/accounts/* |  |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
> | Microsoft.Resources/deployments/* | Criar e gerenciar uma implantação |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Support/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | Microsoft.BigAnalytics/accounts/Delete |  |
> | Microsoft.BigAnalytics/accounts/TakeOwnership/action |  |
> | Microsoft.BigAnalytics/accounts/Write |  |
> | Microsoft.DataLakeAnalytics/accounts/Delete | Excluir uma conta DataLakeAnalytics. |
> | Microsoft.DataLakeAnalytics/accounts/TakeOwnership/action | Conceder permissões para cancelar trabalhos enviados por outros usuários. |
> | Microsoft.DataLakeAnalytics/accounts/Write | Criar ou atualizar uma conta DataLakeAnalytics. |
> | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Write | Criar ou atualizar uma conta DataLakeStore vinculada de uma conta DataLakeAnalytics. |
> | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Delete | Desvincular uma conta do Data Lake Store de uma conta do Data Lake Analytics. |
> | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Write | Criar ou atualizar uma conta de armazenamento vinculada de uma conta DataLakeAnalytics. |
> | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Delete | Desvincular uma conta de armazenamento de uma conta DataLakeAnalytics. |
> | Microsoft.DataLakeAnalytics/accounts/firewallRules/Write | Criar ou atualizar uma regra de firewall. |
> | Microsoft.DataLakeAnalytics/accounts/firewallRules/Delete | Excluir uma regra de firewall. |
> | Microsoft.DataLakeAnalytics/accounts/computePolicies/Write | Criar ou atualizar uma política de computação. |
> | Microsoft.DataLakeAnalytics/accounts/computePolicies/Delete | Excluir uma política de computação. |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you submit, monitor, and manage your own jobs but not create or delete Data Lake Analytics accounts.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/47b7735b-770e-4598-a7da-8b91488b4c88",
  "name": "47b7735b-770e-4598-a7da-8b91488b4c88",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.BigAnalytics/accounts/*",
        "Microsoft.DataLakeAnalytics/accounts/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.BigAnalytics/accounts/Delete",
        "Microsoft.BigAnalytics/accounts/TakeOwnership/action",
        "Microsoft.BigAnalytics/accounts/Write",
        "Microsoft.DataLakeAnalytics/accounts/Delete",
        "Microsoft.DataLakeAnalytics/accounts/TakeOwnership/action",
        "Microsoft.DataLakeAnalytics/accounts/Write",
        "Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Write",
        "Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Delete",
        "Microsoft.DataLakeAnalytics/accounts/storageAccounts/Write",
        "Microsoft.DataLakeAnalytics/accounts/storageAccounts/Delete",
        "Microsoft.DataLakeAnalytics/accounts/firewallRules/Write",
        "Microsoft.DataLakeAnalytics/accounts/firewallRules/Delete",
        "Microsoft.DataLakeAnalytics/accounts/computePolicies/Write",
        "Microsoft.DataLakeAnalytics/accounts/computePolicies/Delete"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Lake Analytics Developer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="reader-and-data-access"></a>Acesso a Dados e Leitor

Permite que você exiba tudo, mas não permitirá que exclua ou crie uma conta de armazenamento ou um recurso contido. Ele também permitirá o acesso de leitura/gravação a todos os dados contidos em uma conta de armazenamento por meio de acesso às chaves de conta de armazenamento.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Storage/storageAccounts/listKeys/action | Retornar as chaves de acesso da conta de armazenamento especificada. |
> | Microsoft. Storage/storageAccounts/ListAccountSas/Action | Retornar o token SAS da conta para a conta de armazenamento especificada. |
> | Microsoft.Storage/storageAccounts/read | Retornar a lista de contas de armazenamento ou obter as propriedades da conta de armazenamento especificada. |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you view everything but will not let you delete or create a storage account or contained resource. It will also allow read/write access to all data contained in a storage account via access to storage account keys.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c12c1c16-33a1-487b-954d-41c89c60f349",
  "name": "c12c1c16-33a1-487b-954d-41c89c60f349",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/listKeys/action",
        "Microsoft.Storage/storageAccounts/ListAccountSas/action",
        "Microsoft.Storage/storageAccounts/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Reader and Data Access",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-account-contributor"></a>Colaborador da Conta de Armazenamento

Permite o gerenciamento de contas de armazenamento. Fornece acesso à chave de conta, que pode ser usada para acessar dados por meio de autorização de chave compartilhada.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | Microsoft.Insights/diagnosticSettings/* | Criar, atualizar ou ler a configuração de diagnóstico do Analysis Server |
> | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Adicionar recursos como conta de armazenamento ou banco de dados SQL a uma sub-rede. Não é possível alertá-lo. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
> | Microsoft.Resources/deployments/* | Criar e gerenciar uma implantação |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Storage/storageAccounts/* | Criar e gerenciar contas de armazenamento |
> | Microsoft.Support/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage storage accounts, including accessing storage account keys which provide full access to storage account data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/17d1049b-9a84-46fb-8f53-869881c3d3ab",
  "name": "17d1049b-9a84-46fb-8f53-869881c3d3ab",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/diagnosticSettings/*",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-account-key-operator-service-role"></a>Função do Serviço de Operador da Chave da Conta de Armazenamento

Permite listar e regenerar chaves de acesso da conta de armazenamento.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Storage/storageAccounts/listkeys/action | Retornar as chaves de acesso da conta de armazenamento especificada. |
> | Microsoft.Storage/storageAccounts/regeneratekey/action | Regenerar as chaves de acesso da conta de armazenamento especificada. |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Storage Account Key Operators are allowed to list and regenerate keys on Storage Accounts",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/81a9662b-bebf-436f-a333-f67b29880f12",
  "name": "81a9662b-bebf-436f-a333-f67b29880f12",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/listkeys/action",
        "Microsoft.Storage/storageAccounts/regeneratekey/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Account Key Operator Service Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-blob-data-contributor"></a>Colaborador de dados de blob de armazenamento

Ler, gravar e excluir contêineres e blobs de armazenamento do Azure. Para saber quais ações são necessárias para uma determinada operação de dados, confira [permissões para chamar operações de BLOB e de dados de fila](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/delete | Excluir um contêiner. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/read | Retornar um contêiner ou uma lista de contêineres. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/write | Modifique os metadados ou as propriedades de um contêiner. |
> | Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action | Retorna uma chave de delegação de usuário para o serviço BLOB. |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete | Excluir um blob. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Retornar um BLOB ou uma lista de BLOBs. |
> | Microsoft. Storage/storageAccounts/blobservices/contêineres/BLOBs/mover/ação | Move o blob de um caminho para outro |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write | Gravar em um blob. |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read, write and delete access to Azure Storage blob containers and data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ba92f5b4-2d11-453d-a403-e96b0029c9fe",
  "name": "ba92f5b4-2d11-453d-a403-e96b0029c9fe",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/write",
        "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/move/action",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Blob Data Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-blob-data-owner"></a>Proprietário de Dados do Blob de Armazenamento

Fornece acesso completo aos dados e contêineres de blob do armazenamento do Azure, incluindo a atribuição de controle de acesso POSIX. Para saber quais ações são necessárias para uma determinada operação de dados, confira [permissões para chamar operações de BLOB e de dados de fila](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/* | Permissões totais em contêineres. |
> | Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action | Retorna uma chave de delegação de usuário para o serviço BLOB. |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/* | Permissões totais em BLOBs. |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for full access to Azure Storage blob containers and data, including assigning POSIX access control.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b7e6dc6d-f1e8-4753-8033-0f276bb0955b",
  "name": "b7e6dc6d-f1e8-4753-8033-0f276bb0955b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/*",
        "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Blob Data Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-blob-data-reader"></a>Leitor de Dados do Blob de Armazenamento

Leia e liste contêineres e blobs de armazenamento do Azure. Para saber quais ações são necessárias para uma determinada operação de dados, confira [permissões para chamar operações de BLOB e de dados de fila](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/read | Retornar um contêiner ou uma lista de contêineres. |
> | Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action | Retorna uma chave de delegação de usuário para o serviço BLOB. |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Retornar um BLOB ou uma lista de BLOBs. |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read access to Azure Storage blob containers and data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
  "name": "2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/read",
        "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Blob Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-blob-delegator"></a>Delegador de blob de armazenamento

Obtenha uma chave de delegação de usuário, que pode ser usada para criar uma assinatura de acesso compartilhado para um contêiner ou BLOB que é assinado com as credenciais do Azure AD. Para obter mais informações, consulte [criar uma SAS de delegação de usuário](https://docs.microsoft.com/rest/api/storageservices/create-user-delegation-sas).

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action | Retorna uma chave de delegação de usuário para o serviço BLOB. |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for generation of a user delegation key which can be used to sign SAS tokens",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/db58b8e5-c6ad-4a2a-8342-4190687cbf4a",
  "name": "db58b8e5-c6ad-4a2a-8342-4190687cbf4a",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Blob Delegator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-file-data-smb-share-contributor"></a>Colaborador de compartilhamento SMB de dados de arquivo de armazenamento

Permite o acesso de leitura, gravação e exclusão em arquivos/diretórios nos compartilhamentos de arquivos do Azure. Essa função não tem equivalente interno nos servidores de arquivos do Windows.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | *nenhum* |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | Microsoft. Storage/storageAccounts/fileservices/compartilhamentos/arquivos/leitura | Retorna um arquivo/pasta ou uma lista de arquivos/pastas. |
> | Microsoft. Storage/storageAccounts/fileservices/compartilhamentos/arquivos/gravação | Retorna o resultado da gravação de um arquivo ou da criação de uma pasta. |
> | Microsoft. Storage/storageAccounts/fileservices/arquivos/compartilhamento/exclusão | Retorna o resultado da exclusão de um arquivo/pasta. |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read, write, and delete access in Azure Storage file shares over SMB",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/0c867c2a-1d8c-454a-a3db-ab2ea1bdc8bb",
  "name": "0c867c2a-1d8c-454a-a3db-ab2ea1bdc8bb",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/write",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/delete"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage File Data SMB Share Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-file-data-smb-share-elevated-contributor"></a>Colaborador elevado de compartilhamento SMB de dados de arquivo de armazenamento

Permite ler, gravar, excluir e modificar ACLs em arquivos/diretórios nos compartilhamentos de arquivos do Azure. Essa função é equivalente a uma ACL de compartilhamento de arquivos de alteração em servidores de arquivos do Windows.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | *nenhum* |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | Microsoft. Storage/storageAccounts/fileservices/compartilhamentos/arquivos/leitura | Retorna um arquivo/pasta ou uma lista de arquivos/pastas. |
> | Microsoft. Storage/storageAccounts/fileservices/compartilhamentos/arquivos/gravação | Retorna o resultado da gravação de um arquivo ou da criação de uma pasta. |
> | Microsoft. Storage/storageAccounts/fileservices/arquivos/compartilhamento/exclusão | Retorna o resultado da exclusão de um arquivo/pasta. |
> | Microsoft. Storage/storageAccounts/fileservices/arquivo compartilhado/arquivos/modifypermissions/Action | Retorna o resultado da permissão de modificação em um arquivo/pasta. |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read, write, delete and modify NTFS permission access in Azure Storage file shares over SMB",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a7264617-510b-434b-a828-9731dc254ea7",
  "name": "a7264617-510b-434b-a828-9731dc254ea7",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/write",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/delete",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/modifypermissions/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage File Data SMB Share Elevated Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-file-data-smb-share-reader"></a>Leitor de compartilhamento SMB de dados de arquivo de armazenamento

Permite acesso de leitura em arquivos/diretórios nos compartilhamentos de arquivos do Azure. Essa função é equivalente a uma ACL de compartilhamento de arquivos de leitura em servidores de arquivos do Windows.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | *nenhum* |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | Microsoft. Storage/storageAccounts/fileservices/compartilhamentos/arquivos/leitura | Retorna um arquivo/pasta ou uma lista de arquivos/pastas. |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read access to Azure File Share over SMB",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/aba4ae5f-2193-4029-9191-0cb91df5e314",
  "name": "aba4ae5f-2193-4029-9191-0cb91df5e314",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage File Data SMB Share Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-queue-data-contributor"></a>Colaborador de dados da fila de armazenamento

Ler, gravar e excluir filas do armazenamento do Azure e mensagens da fila. Para saber quais ações são necessárias para uma determinada operação de dados, confira [permissões para chamar operações de BLOB e de dados de fila](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/delete | Excluir uma fila. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/read | Retornar uma fila ou uma lista de filas. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/write | Modificar metadados ou propriedades da fila. |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete | Excluir uma ou mais mensagens de uma fila. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | Espiar ou recuperar uma ou mais mensagens de uma fila. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/write | Adicione uma mensagem a uma fila. |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read, write, and delete access to Azure Storage queues and queue messages",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/974c5e8b-45b9-4653-ba55-5f855dd0fb88",
  "name": "974c5e8b-45b9-4653-ba55-5f855dd0fb88",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/delete",
        "Microsoft.Storage/storageAccounts/queueServices/queues/read",
        "Microsoft.Storage/storageAccounts/queueServices/queues/write"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete",
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/read",
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Queue Data Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-queue-data-message-processor"></a>Processador de mensagens de dados da fila de armazenamento

Espiar, recuperar e excluir uma mensagem de uma fila de armazenamento do Azure. Para saber quais ações são necessárias para uma determinada operação de dados, confira [permissões para chamar operações de BLOB e de dados de fila](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | *nenhum* |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | Inspecionar uma mensagem. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action | Recuperar e excluir uma mensagem. |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for peek, receive, and delete access to Azure Storage queue messages",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8a0f0c08-91a1-4084-bc3d-661d67233fed",
  "name": "8a0f0c08-91a1-4084-bc3d-661d67233fed",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/read",
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Queue Data Message Processor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-queue-data-message-sender"></a>Remetente da mensagem de dados da fila de armazenamento

Adicionar mensagens a uma fila de armazenamento do Azure. Para saber quais ações são necessárias para uma determinada operação de dados, confira [permissões para chamar operações de BLOB e de dados de fila](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | *nenhum* |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/add/action | Adicione uma mensagem a uma fila. |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for sending of Azure Storage queue messages",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c6a89b2d-59bc-44d0-9896-0f6e12d7b80a",
  "name": "c6a89b2d-59bc-44d0-9896-0f6e12d7b80a",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/add/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Queue Data Message Sender",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-queue-data-reader"></a>Leitor de dados da fila de armazenamento

Leia e liste as filas do armazenamento do Azure e as mensagens da fila. Para saber quais ações são necessárias para uma determinada operação de dados, confira [permissões para chamar operações de BLOB e de dados de fila](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/read | Retornar uma fila ou uma lista de filas. |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | Espiar ou recuperar uma ou mais mensagens de uma fila. |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read access to Azure Storage queues and queue messages",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/19e7f393-937e-4f77-808e-94535e297925",
  "name": "19e7f393-937e-4f77-808e-94535e297925",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Queue Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="web"></a>Web


### <a name="azure-maps-data-reader"></a>Leitor de dados do Azure Maps

Concede acesso para ler dados relacionados ao mapa de uma conta do Azure Maps.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | *nenhum* |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | Microsoft. Maps/accounts/*/Read |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Grants access to read map related data from an Azure maps account.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/423170ca-a8f6-4b0f-8487-9e4eb8f49bfa",
  "name": "423170ca-a8f6-4b0f-8487-9e4eb8f49bfa",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Maps/accounts/*/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Maps Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="search-service-contributor"></a>Colaborador do Serviço de Pesquisa

Permite gerenciar serviços de pesquisa, mas não acessá-las.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
> | Microsoft.Resources/deployments/* | Criar e gerenciar uma implantação |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Search/searchServices/* | Criar e gerenciar serviços de pesquisa |
> | Microsoft.Support/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Search services, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/7ca78c08-252a-4471-8644-bb5ff32d4ba0",
  "name": "7ca78c08-252a-4471-8644-bb5ff32d4ba0",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Search/searchServices/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Search Service Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="web-plan-contributor"></a>Colaborador do Plano de Web

Permite gerenciar os planos da Web para sites, mas não o acesso a eles.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
> | Microsoft.Resources/deployments/* | Criar e gerenciar uma implantação |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Support/* | Criar e atualizar um tíquete de suporte |
> | Microsoft.Web/serverFarms/* | Criar e gerenciar farms de servidores |
> | Microsoft. Web/hostingEnvironments/junção/ação | Une um Ambiente do Serviço de Aplicativo |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage the web plans for websites, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b",
  "name": "2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Web/serverFarms/*",
        "Microsoft.Web/hostingEnvironments/Join/Action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Web Plan Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="website-contributor"></a>Colaborador do Site

Permite gerenciar sites (não planos da Web), mas não acessá-los.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | Microsoft.Insights/components/* | Criar e gerenciar componentes do Insights |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
> | Microsoft.Resources/deployments/* | Criar e gerenciar uma implantação |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Support/* | Criar e atualizar um tíquete de suporte |
> | Microsoft.Web/certificates/* | Criar e gerenciar certificados de site da Web |
> | Microsoft.Web/listSitesAssignedToHostName/read | Obter nomes dos sites atribuídos ao nome de host. |
> | Microsoft.Web/serverFarms/join/action |  |
> | Microsoft.Web/serverFarms/read | Obter as propriedades em um Plano do Serviço de Aplicativo |
> | Microsoft.Web/sites/* | Criar e gerenciar sites (a criação de sites também requer permissões de gravação para o Plano do Serviço de Aplicativo associado) |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage websites (not web plans), but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/de139f84-1756-47ae-9be6-808fbbe84772",
  "name": "de139f84-1756-47ae-9be6-808fbbe84772",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/components/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Web/certificates/*",
        "Microsoft.Web/listSitesAssignedToHostName/read",
        "Microsoft.Web/serverFarms/join/action",
        "Microsoft.Web/serverFarms/read",
        "Microsoft.Web/sites/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Website Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="containers"></a>Contêineres


### <a name="acrdelete"></a>AcrDelete

exclusão de ACR

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft. ContainerRegistry/registros/artefatos/excluir | Exclua o artefato em um registro de contêiner. |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr delete",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c2f4ef07-c644-48eb-af81-4b1b4947fb11",
  "name": "c2f4ef07-c644-48eb-af81-4b1b4947fb11",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/artifacts/delete"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrDelete",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrimagesigner"></a>AcrImageSigner

signatário de imagem ACR

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.ContainerRegistry/registries/sign/write | Efetuar push/pull de metadados de conteúdo confiável para um registro de contêiner. |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr image signer",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/6cef56e8-d556-48e5-a04f-b8e64114680f",
  "name": "6cef56e8-d556-48e5-a04f-b8e64114680f",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/sign/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrImageSigner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrpull"></a>AcrPull

acr pull

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.ContainerRegistry/registries/pull/read | Efetuar pull ou Obter imagens de um registro de contêiner. |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr pull",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/7f951dda-4ed3-4680-a7ca-43fe172d538d",
  "name": "7f951dda-4ed3-4680-a7ca-43fe172d538d",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/pull/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrPull",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrpush"></a>AcrPush

acr push

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.ContainerRegistry/registries/pull/read | Efetuar pull ou Obter imagens de um registro de contêiner. |
> | Microsoft.ContainerRegistry/registries/push/write | Efetuar push ou Gravar imagens para um registro de contêiner. |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr push",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8311e382-0749-4cb8-b61a-304f252e45ec",
  "name": "8311e382-0749-4cb8-b61a-304f252e45ec",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/pull/read",
        "Microsoft.ContainerRegistry/registries/push/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrPush",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrquarantinereader"></a>AcrQuarantineReader

leitor de dados de quarentena acr

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft. ContainerRegistry/registros/quarentena/leitura | Efetuar pull ou Obter imagens em quarentena do registro de contêiner |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr quarantine data reader",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/cdda3590-29a3-44f6-95f2-9f980659eb04",
  "name": "cdda3590-29a3-44f6-95f2-9f980659eb04",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/quarantine/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrQuarantineReader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrquarantinewriter"></a>AcrQuarantineWriter

gravador de dados de quarentena acr

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft. ContainerRegistry/registros/quarentena/leitura | Efetuar pull ou Obter imagens em quarentena do registro de contêiner |
> | Microsoft. ContainerRegistry/registros/quarentena/gravação | Gravar/Modificar o estado de quarentena das imagens em quarentena |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr quarantine data writer",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c8d4ff99-41c3-41a8-9f60-21dfdad59608",
  "name": "c8d4ff99-41c3-41a8-9f60-21dfdad59608",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/quarantine/read",
        "Microsoft.ContainerRegistry/registries/quarantine/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrQuarantineWriter",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-cluster-admin-role"></a>Função de Administrador do Cluster do Serviço de Kubernetes do Azure

Liste a ação de credencial de administrador de cluster.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action | Listar a credencial clusterAdmin de um cluster gerenciado |
> | Microsoft.ContainerService/managedClusters/accessProfiles/listCredential/action | Obtém um perfil de acesso do cluster gerenciado por nome de função usando a credencial de lista |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "List cluster admin credential action.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/0ab0b1a8-8aac-4efd-b8c2-3ee1fb270be8",
  "name": "0ab0b1a8-8aac-4efd-b8c2-3ee1fb270be8",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action",
        "Microsoft.ContainerService/managedClusters/accessProfiles/listCredential/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Kubernetes Service Cluster Admin Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-cluster-user-role"></a>Função de Usuário do Cluster do Serviço de Kubernetes do Azure

Liste a ação de credencial de usuário de cluster.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.ContainerService/managedClusters/listClusterUserCredential/action | Listar a credencial clusterUser de um cluster gerenciado |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "List cluster user credential action.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4abbcc35-e782-43d8-92c5-2d3f1bd2253f",
  "name": "4abbcc35-e782-43d8-92c5-2d3f1bd2253f",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerService/managedClusters/listClusterUserCredential/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Kubernetes Service Cluster User Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="databases"></a>Bancos de dados


### <a name="cosmos-db-account-reader-role"></a>Função de leitor de conta do Cosmos DB

Pode ler dados de contas do Azure Cosmos DB. Consulte [Colaborador de conta do DocumentDB](#documentdb-account-contributor) para gerenciar contas do Azure Cosmos DB.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.DocumentDB/*/read | Ler qualquer coleção |
> | Microsoft.DocumentDB/databaseAccounts/readonlykeys/action | Ler as chaves somente leitura da conta do banco de dados. |
> | Microsoft.Insights/MetricDefinitions/read | Ler definições de métrica |
> | Microsoft.Insights/Metrics/read | Ler métrica |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Support/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read Azure Cosmos DB Accounts data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fbdf93bf-df7d-467e-a4d2-9458aa1360c8",
  "name": "fbdf93bf-df7d-467e-a4d2-9458aa1360c8",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.DocumentDB/*/read",
        "Microsoft.DocumentDB/databaseAccounts/readonlykeys/action",
        "Microsoft.Insights/MetricDefinitions/read",
        "Microsoft.Insights/Metrics/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cosmos DB Account Reader Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cosmos-db-operator"></a>Operador de Cosmos DB

Permite que você gerencie contas Azure Cosmos DB, mas não acesse os dados nelas. Impede o acesso a chaves de conta e cadeias de conexão.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.DocumentDb/databaseAccounts/* |  |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
> | Microsoft.Resources/deployments/* | Criar e gerenciar uma implantação |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Support/* | Criar e atualizar um tíquete de suporte |
> | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Adicionar recursos como conta de armazenamento ou banco de dados SQL a uma sub-rede. Não é possível alertá-lo. |
> | **NotActions** |  |
> | Microsoft. DocumentDB/databaseAccounts/readonlyKeys/* |  |
> | Microsoft. DocumentDB/databaseAccounts/regenerateKey/* |  |
> | Microsoft. DocumentDB/databaseAccounts/listKeys/* |  |
> | Microsoft. DocumentDB/databaseAccounts/listConnectionStrings/* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Azure Cosmos DB accounts, but not access data in them. Prevents access to account keys and connection strings.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/230815da-be43-4aae-9cb4-875f7bd000aa",
  "name": "230815da-be43-4aae-9cb4-875f7bd000aa",
  "permissions": [
    {
      "actions": [
        "Microsoft.DocumentDb/databaseAccounts/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action"
      ],
      "notActions": [
        "Microsoft.DocumentDB/databaseAccounts/readonlyKeys/*",
        "Microsoft.DocumentDB/databaseAccounts/regenerateKey/*",
        "Microsoft.DocumentDB/databaseAccounts/listKeys/*",
        "Microsoft.DocumentDB/databaseAccounts/listConnectionStrings/*"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cosmos DB Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cosmosbackupoperator"></a>CosmosBackupOperator

Pode enviar solicitação de restauração de um banco de dados Cosmos DB ou de um contêiner em uma conta

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.DocumentDB/databaseAccounts/backup/action | Envie uma solicitação para configurar o backup |
> | Microsoft.DocumentDB/databaseAccounts/restore/action | Enviar uma solicitação de restauração |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can submit restore request for a Cosmos DB database or a container for an account",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/db7b14f2-5adf-42da-9f96-f2ee17bab5cb",
  "name": "db7b14f2-5adf-42da-9f96-f2ee17bab5cb",
  "permissions": [
    {
      "actions": [
        "Microsoft.DocumentDB/databaseAccounts/backup/action",
        "Microsoft.DocumentDB/databaseAccounts/restore/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CosmosBackupOperator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="documentdb-account-contributor"></a>Colaborador de Conta do DocumentDB

Pode gerenciar contas do Azure Cosmos DB. O Azure Cosmos DB era anteriormente conhecido como DocumentDB.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.DocumentDb/databaseAccounts/* | Criar e gerenciar contas do Azure Cosmos DB |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
> | Microsoft.Resources/deployments/* | Criar e gerenciar uma implantação |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Support/* | Criar e atualizar um tíquete de suporte |
> | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Adicionar recursos como conta de armazenamento ou banco de dados SQL a uma sub-rede. Não é possível alertá-lo. |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage DocumentDB accounts, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5bd9cd88-fe45-4216-938b-f97437e15450",
  "name": "5bd9cd88-fe45-4216-938b-f97437e15450",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.DocumentDb/databaseAccounts/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "DocumentDB Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="redis-cache-contributor"></a>Colaborador do Cache Redis

Permite gerenciar caches Redis, mas não acessá-los.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Cache/redis/* | Criar e gerenciar caches Redis |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
> | Microsoft.Resources/deployments/* | Criar e gerenciar uma implantação |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Support/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Redis caches, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e0f68234-74aa-48ed-b826-c38b57376e17",
  "name": "e0f68234-74aa-48ed-b826-c38b57376e17",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cache/redis/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Redis Cache Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="sql-db-contributor"></a>Colaborador do banco de dados SQL

Permite gerenciar Bancos de Dados SQL, mas não acessá-los. Além disso, não é possível gerenciar as políticas relacionadas à segurança ou respectivos servidores SQL pai.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
> | Microsoft.Resources/deployments/* | Criar e gerenciar uma implantação |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Sql/locations/*/read |  |
> | Microsoft.Sql/servers/databases/* | Criar e gerenciar bancos de dados SQL |
> | Microsoft.Sql/servers/read | Retornar a lista de servidores ou obter as propriedades para o servidor especificado. |
> | Microsoft.Support/* | Criar e atualizar um tíquete de suporte |
> | Microsoft.Insights/metrics/read | Ler métrica |
> | Microsoft.Insights/metricDefinitions/read | Ler definições de métrica |
> | **NotActions** |  |
> | Microsoft. SQL/managedInstances/bancos de dados/currentSensitivityLabels/* |  |
> | Microsoft. SQL/managedInstances/bancos de dados/recommendedSensitivityLabels/* |  |
> | Microsoft. SQL/managedInstances/bancos de dados/esquemas/tabelas/colunas/sensitivityLabels/* |  |
> | Microsoft. SQL/managedInstances/bancos de dados/securityAlertPolicies/* |  |
> | Microsoft. SQL/managedInstances/bancos de dados/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/* |  |
> | Microsoft. SQL/managedInstances/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/auditingPolicies/* | Editar políticas de auditoria |
> | Microsoft.Sql/servers/databases/auditingSettings/* | Editar configurações de auditoria |
> | Microsoft.Sql/servers/databases/auditRecords/read | Recuperar os registros de auditoria do blob do banco de dados |
> | Microsoft.Sql/servers/databases/connectionPolicies/* | Editar políticas de conexão |
> | Microsoft. SQL/servidores/bancos de dados/currentSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Editar políticas de mascaramento dos dados |
> | Microsoft.Sql/servers/databases/extendedAuditingSettings/* |  |
> | Microsoft. SQL/servidores/bancos de dados/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/securityAlertPolicies/* | Editar políticas de alerta de segurança |
> | Microsoft.Sql/servers/databases/securityMetrics/* | Editar métricas de segurança |
> | Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | Microsoft.Sql/servers/vulnerabilityAssessments/* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage SQL databases, but not access to them. Also, you can't manage their security-related policies or their parent SQL servers.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/9b7fa17d-e63e-47b0-bb0a-15c516ac86ec",
  "name": "9b7fa17d-e63e-47b0-bb0a-15c516ac86ec",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Sql/locations/*/read",
        "Microsoft.Sql/servers/databases/*",
        "Microsoft.Sql/servers/read",
        "Microsoft.Support/*",
        "Microsoft.Insights/metrics/read",
        "Microsoft.Insights/metricDefinitions/read"
      ],
      "notActions": [
        "Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/databases/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/managedInstances/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/databases/auditingPolicies/*",
        "Microsoft.Sql/servers/databases/auditingSettings/*",
        "Microsoft.Sql/servers/databases/auditRecords/read",
        "Microsoft.Sql/servers/databases/connectionPolicies/*",
        "Microsoft.Sql/servers/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/dataMaskingPolicies/*",
        "Microsoft.Sql/servers/databases/extendedAuditingSettings/*",
        "Microsoft.Sql/servers/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/securityAlertPolicies/*",
        "Microsoft.Sql/servers/databases/securityMetrics/*",
        "Microsoft.Sql/servers/databases/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/*",
        "Microsoft.Sql/servers/vulnerabilityAssessments/*"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SQL DB Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="sql-managed-instance-contributor"></a>Colaborador do SQL Instância Gerenciada

Permite que você gerencie instâncias gerenciadas do SQL e a configuração de rede necessária, mas não pode conceder acesso a outras pessoas.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
> | Microsoft.Resources/deployments/* | Criar e gerenciar uma implantação |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft. Network/networkSecurityGroups/* |  |
> | Microsoft. Network/routeTables/* |  |
> | Microsoft.Sql/locations/*/read |  |
> | Microsoft. SQL/managedInstances/* |  |
> | Microsoft.Support/* | Criar e atualizar um tíquete de suporte |
> | Microsoft. Network/virtualNetworks/sub-redes/* |  |
> | Microsoft. Network/virtualNetworks/* |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | Microsoft.Insights/metrics/read | Ler métrica |
> | Microsoft.Insights/metricDefinitions/read | Ler definições de métrica |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage SQL Managed Instances and required network configuration, but can't give access to others.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4939a1f6-9ae0-4e48-a1e0-f2cbe897382d",
  "name": "4939a1f6-9ae0-4e48-a1e0-f2cbe897382d",
  "permissions": [
    {
      "actions": [
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Network/networkSecurityGroups/*",
        "Microsoft.Network/routeTables/*",
        "Microsoft.Sql/locations/*/read",
        "Microsoft.Sql/managedInstances/*",
        "Microsoft.Support/*",
        "Microsoft.Network/virtualNetworks/subnets/*",
        "Microsoft.Network/virtualNetworks/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/metrics/read",
        "Microsoft.Insights/metricDefinitions/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SQL Managed Instance Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="sql-security-manager"></a>Gerenciador de Segurança do SQL

Permite você gerenciar as políticas relacionadas à segurança de servidores e bancos de dados SQL, mas não acessá-los.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Adicionar recursos como conta de armazenamento ou banco de dados SQL a uma sub-rede. Não é possível alertá-lo. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
> | Microsoft.Resources/deployments/* | Criar e gerenciar uma implantação |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft. SQL/managedInstances/bancos de dados/currentSensitivityLabels/* |  |
> | Microsoft. SQL/managedInstances/bancos de dados/recommendedSensitivityLabels/* |  |
> | Microsoft. SQL/managedInstances/bancos de dados/esquemas/tabelas/colunas/sensitivityLabels/* |  |
> | Microsoft. SQL/managedInstances/bancos de dados/securityAlertPolicies/* |  |
> | Microsoft. SQL/managedInstances/bancos de dados/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/* |  |
> | Microsoft. SQL/managedInstances/securityAlertPolicies/* |  |
> | Microsoft. SQL/managedInstances/bancos de dados/transparentDataEncryption/* |  |
> | Microsoft.Sql/managedInstances/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/auditingPolicies/* | Criar e gerenciar políticas de auditoria do servidor SQL |
> | Microsoft.Sql/servers/auditingSettings/* | Criar e gerenciar a configuração de auditoria do servidor SQL |
> | Microsoft.Sql/servers/extendedAuditingSettings/read | Recuperar detalhes da política de auditoria de blob de servidor estendida configurada em um determinado servidor |
> | Microsoft.Sql/servers/databases/auditingPolicies/* | Criar e gerenciar políticas de auditoria de banco de dados do servidor SQL |
> | Microsoft.Sql/servers/databases/auditingSettings/* | Criar e gerenciar configurações de auditoria de banco de dados do servidor SQL |
> | Microsoft.Sql/servers/databases/auditRecords/read | Recuperar os registros de auditoria do blob do banco de dados |
> | Microsoft.Sql/servers/databases/connectionPolicies/* | Criar e gerenciar políticas de conexão de banco de dados do servidor SQL |
> | Microsoft. SQL/servidores/bancos de dados/currentSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Criar e gerenciar políticas de mascaramento de dados do banco de dados do servidor SQL |
> | Microsoft.Sql/servers/databases/extendedAuditingSettings/read | Recuperar detalhes da política de auditoria de blob estendida configurada em um determinado banco de dados |
> | Microsoft.Sql/servers/databases/read | Retornar a lista de bancos de dados ou obter as propriedades para o banco de dados especificado. |
> | Microsoft. SQL/servidores/bancos de dados/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/schemas/read | Obtenha um esquema de banco de dados. |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/read | Obter uma coluna de banco de dados. |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/schemas/tables/read | Obter uma tabela de banco de dados. |
> | Microsoft.Sql/servers/databases/securityAlertPolicies/* | Criar e gerenciar políticas de alerta de segurança do banco de dados do servidor SQL |
> | Microsoft.Sql/servers/databases/securityMetrics/* | Criar e gerenciar métricas de segurança do banco de dados do servidor SQL |
> | Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
> | Microsoft. SQL/servidores/bancos de dados/transparentDataEncryption/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | Microsoft.Sql/servers/firewallRules/* |  |
> | Microsoft.Sql/servers/read | Retornar a lista de servidores ou obter as propriedades para o servidor especificado. |
> | Microsoft.Sql/servers/securityAlertPolicies/* | Criar e gerenciar políticas de alerta de segurança de servidor SQL |
> | Microsoft.Sql/servers/vulnerabilityAssessments/* |  |
> | Microsoft.Support/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage the security-related policies of SQL servers and databases, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/056cd41c-7e88-42e1-933e-88ba6a50c9c3",
  "name": "056cd41c-7e88-42e1-933e-88ba6a50c9c3",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/databases/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/managedInstances/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/databases/transparentDataEncryption/*",
        "Microsoft.Sql/managedInstances/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/auditingPolicies/*",
        "Microsoft.Sql/servers/auditingSettings/*",
        "Microsoft.Sql/servers/extendedAuditingSettings/read",
        "Microsoft.Sql/servers/databases/auditingPolicies/*",
        "Microsoft.Sql/servers/databases/auditingSettings/*",
        "Microsoft.Sql/servers/databases/auditRecords/read",
        "Microsoft.Sql/servers/databases/connectionPolicies/*",
        "Microsoft.Sql/servers/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/dataMaskingPolicies/*",
        "Microsoft.Sql/servers/databases/extendedAuditingSettings/read",
        "Microsoft.Sql/servers/databases/read",
        "Microsoft.Sql/servers/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/schemas/read",
        "Microsoft.Sql/servers/databases/schemas/tables/columns/read",
        "Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/schemas/tables/read",
        "Microsoft.Sql/servers/databases/securityAlertPolicies/*",
        "Microsoft.Sql/servers/databases/securityMetrics/*",
        "Microsoft.Sql/servers/databases/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/transparentDataEncryption/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/*",
        "Microsoft.Sql/servers/firewallRules/*",
        "Microsoft.Sql/servers/read",
        "Microsoft.Sql/servers/securityAlertPolicies/*",
        "Microsoft.Sql/servers/vulnerabilityAssessments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SQL Security Manager",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="sql-server-contributor"></a>Colaborador do SQL Server

Permite gerenciar servidores e bancos de dados SQL, mas não acessá-los, nem suas políticas relacionadas à segurança.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
> | Microsoft.Resources/deployments/* | Criar e gerenciar uma implantação |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Sql/locations/*/read |  |
> | Microsoft.Sql/servers/* | Criar e gerenciar servidores SQL |
> | Microsoft.Support/* | Criar e atualizar um tíquete de suporte |
> | Microsoft.Insights/metrics/read | Ler métrica |
> | Microsoft.Insights/metricDefinitions/read | Ler definições de métrica |
> | **NotActions** |  |
> | Microsoft. SQL/managedInstances/bancos de dados/currentSensitivityLabels/* |  |
> | Microsoft. SQL/managedInstances/bancos de dados/recommendedSensitivityLabels/* |  |
> | Microsoft. SQL/managedInstances/bancos de dados/esquemas/tabelas/colunas/sensitivityLabels/* |  |
> | Microsoft. SQL/managedInstances/bancos de dados/securityAlertPolicies/* |  |
> | Microsoft. SQL/managedInstances/bancos de dados/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/* |  |
> | Microsoft. SQL/managedInstances/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/auditingPolicies/* | Editar políticas de auditoria do servidor SQL |
> | Microsoft.Sql/servers/auditingSettings/* | Editar configurações de auditoria do servidor SQL |
> | Microsoft.Sql/servers/databases/auditingPolicies/* | Editar políticas de auditoria de banco de dados do servidor SQL |
> | Microsoft.Sql/servers/databases/auditingSettings/* | Editar configurações de auditoria de banco de dados do servidor SQL |
> | Microsoft.Sql/servers/databases/auditRecords/read | Recuperar os registros de auditoria do blob do banco de dados |
> | Microsoft.Sql/servers/databases/connectionPolicies/* | Editar políticas de conexão de banco de dados do servidor SQL |
> | Microsoft. SQL/servidores/bancos de dados/currentSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Editar políticas de mascaramento de banco de dados do servidor SQL |
> | Microsoft.Sql/servers/databases/extendedAuditingSettings/* |  |
> | Microsoft. SQL/servidores/bancos de dados/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/securityAlertPolicies/* | Editar políticas de alerta de segurança de banco de dados do servidor SQL |
> | Microsoft.Sql/servers/databases/securityMetrics/* | Editar métricas de segurança de banco de dados do servidor SQL |
> | Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | Microsoft.Sql/servers/extendedAuditingSettings/* |  |
> | Microsoft.Sql/servers/securityAlertPolicies/* | Editar políticas de alerta de segurança de servidor SQL |
> | Microsoft.Sql/servers/vulnerabilityAssessments/* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage SQL servers and databases, but not access to them, and not their security -related policies.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437",
  "name": "6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Sql/locations/*/read",
        "Microsoft.Sql/servers/*",
        "Microsoft.Support/*",
        "Microsoft.Insights/metrics/read",
        "Microsoft.Insights/metricDefinitions/read"
      ],
      "notActions": [
        "Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/databases/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/managedInstances/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/auditingPolicies/*",
        "Microsoft.Sql/servers/auditingSettings/*",
        "Microsoft.Sql/servers/databases/auditingPolicies/*",
        "Microsoft.Sql/servers/databases/auditingSettings/*",
        "Microsoft.Sql/servers/databases/auditRecords/read",
        "Microsoft.Sql/servers/databases/connectionPolicies/*",
        "Microsoft.Sql/servers/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/dataMaskingPolicies/*",
        "Microsoft.Sql/servers/databases/extendedAuditingSettings/*",
        "Microsoft.Sql/servers/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/securityAlertPolicies/*",
        "Microsoft.Sql/servers/databases/securityMetrics/*",
        "Microsoft.Sql/servers/databases/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/*",
        "Microsoft.Sql/servers/extendedAuditingSettings/*",
        "Microsoft.Sql/servers/securityAlertPolicies/*",
        "Microsoft.Sql/servers/vulnerabilityAssessments/*"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SQL Server Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="analytics"></a>Análise


### <a name="azure-event-hubs-data-owner"></a>Proprietário de dados dos hubs de eventos do Azure

Permite acesso completo aos recursos dos hubs de eventos do Azure.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft. EventHub/* |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | Microsoft. EventHub/* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for full access to Azure Event Hubs resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/f526a384-b230-433a-b45c-95f59c4a2dec",
  "name": "f526a384-b230-433a-b45c-95f59c4a2dec",
  "permissions": [
    {
      "actions": [
        "Microsoft.EventHub/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.EventHub/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Event Hubs Data Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-event-hubs-data-receiver"></a>Receptor de dados dos hubs de eventos do Azure

Permite o acesso de recebimento aos recursos dos hubs de eventos do Azure.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft. EventHub/*/Eventhubs/consumergroups/Read |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | Microsoft. EventHub/*/Receive/Action |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows receive access to Azure Event Hubs resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a638d3c7-ab3a-418d-83e6-5f17a39d4fde",
  "name": "a638d3c7-ab3a-418d-83e6-5f17a39d4fde",
  "permissions": [
    {
      "actions": [
        "Microsoft.EventHub/*/eventhubs/consumergroups/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.EventHub/*/receive/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Event Hubs Data Receiver",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-event-hubs-data-sender"></a>Remetente de dados dos hubs de eventos do Azure

Permite o acesso de envio aos recursos dos hubs de eventos do Azure.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft. EventHub/*/Eventhubs/Read |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | Microsoft. EventHub/*/Send/Action |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows send access to Azure Event Hubs resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2b629674-e913-4c01-ae53-ef4638d8f975",
  "name": "2b629674-e913-4c01-ae53-ef4638d8f975",
  "permissions": [
    {
      "actions": [
        "Microsoft.EventHub/*/eventhubs/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.EventHub/*/send/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Event Hubs Data Sender",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-factory-contributor"></a>Colaborador da fábrica de dados

Cria e gerencia data factories, assim como os recursos filhos neles.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.DataFactory/dataFactories/* | Criar e gerenciar data factories e recursos filho dentro deles. |
> | Microsoft.DataFactory/factories/* | Criar e gerenciar data factories e recursos filho dentro deles. |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
> | Microsoft.Resources/deployments/* | Criar e gerenciar uma implantação |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Support/* | Criar e atualizar um tíquete de suporte |
> | Microsoft.EventGrid/eventSubscriptions/write | Criar ou atualizar um eventSubscription |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Create and manage data factories, as well as child resources within them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/673868aa-7521-48a0-acc6-0f60742d39f5",
  "name": "673868aa-7521-48a0-acc6-0f60742d39f5",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.DataFactory/dataFactories/*",
        "Microsoft.DataFactory/factories/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.EventGrid/eventSubscriptions/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Factory Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-purger"></a>Limpador de Dados

Pode limpar os dados de análise

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Insights/components/*/read |  |
> | Microsoft.Insights/components/purge/action | Limpe dados do Application Insights |
> | Microsoft.OperationalInsights/workspaces/*/read | Exibir dados do log Analytics |
> | Microsoft.OperationalInsights/workspaces/purge/action | Excluir dados especificados do workspace |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can purge analytics data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/150f5e0c-0603-4f03-8c7f-cf70034c4e90",
  "name": "150f5e0c-0603-4f03-8c7f-cf70034c4e90",
  "permissions": [
    {
      "actions": [
        "Microsoft.Insights/components/*/read",
        "Microsoft.Insights/components/purge/action",
        "Microsoft.OperationalInsights/workspaces/*/read",
        "Microsoft.OperationalInsights/workspaces/purge/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Purger",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="hdinsight-cluster-operator"></a>Operador de cluster HDInsight

Permite que você leia e modifique as configurações do cluster HDInsight.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft. HDInsight/*/Read |  |
> | Microsoft. HDInsight/clusters/getGatewaySettings/ação | Obter configurações de gateway para o cluster HDInsight |
> | Microsoft. HDInsight/clusters/updateGatewaySettings/ação | Atualizar as configurações do gateway para o cluster HDInsight |
> | Microsoft. HDInsight/clusters/Configurations/* |  |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Resources/deployments/operations/read | Obter ou lista operações de implantação. |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Support/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read and modify HDInsight cluster configurations.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/61ed4efc-fab3-44fd-b111-e24485cc132a",
  "name": "61ed4efc-fab3-44fd-b111-e24485cc132a",
  "permissions": [
    {
      "actions": [
        "Microsoft.HDInsight/*/read",
        "Microsoft.HDInsight/clusters/getGatewaySettings/action",
        "Microsoft.HDInsight/clusters/updateGatewaySettings/action",
        "Microsoft.HDInsight/clusters/configurations/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "HDInsight Cluster Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="hdinsight-domain-services-contributor"></a>Colaborador dos serviços de domínio do HDInsight

Pode ler, criar, modificar e excluir operações relacionadas aos serviços de domínio necessárias para o Enterprise Security Package do HDInsight

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.AAD/*/read |  |
> | Microsoft.AAD/domainServices/*/read |  |
> | Microsoft.AAD/domainServices/oucontainer/* |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can Read, Create, Modify and Delete Domain Services related operations needed for HDInsight Enterprise Security Package",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8d8d5a11-05d3-4bda-a417-a08778121c7c",
  "name": "8d8d5a11-05d3-4bda-a417-a08778121c7c",
  "permissions": [
    {
      "actions": [
        "Microsoft.AAD/*/read",
        "Microsoft.AAD/domainServices/*/read",
        "Microsoft.AAD/domainServices/oucontainer/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "HDInsight Domain Services Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="log-analytics-contributor"></a>Colaborador do Log Analytics

O Colaborador do Log Analytics pode ler todos os dados de monitoramento e editar as configurações de monitoramento. A edição das configurações de monitoramento inclui a adição da extensão da VM às VMs, leitura das chaves da conta de armazenamento para poder configurar a coleção de logs do Armazenamento do Microsoft Azure, criação e configuração de contas de Automação, adição de soluções e configuração do diagnóstico do Azure em todos os recursos do Azure.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | */leitura | Ler recursos de todos os tipos, exceto segredos. |
> | Microsoft.Automation/automationAccounts/* |  |
> | Microsoft.ClassicCompute/virtualMachines/extensions/* |  |
> | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Listar as chaves de acesso das contas de armazenamento. |
> | Microsoft.Compute/virtualMachines/extensions/* |  |
> | Microsoft. HybridCompute/Machines/Extensions/Write | Instala ou atualiza uma extensão de Arc do Azure |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | Microsoft.Insights/diagnosticSettings/* | Criar, atualizar ou ler a configuração de diagnóstico do Analysis Server |
> | Microsoft.OperationalInsights/* |  |
> | Microsoft.OperationsManagement/* |  |
> | Microsoft.Resources/deployments/* | Criar e gerenciar uma implantação |
> | Microsoft.Resources/subscriptions/resourcegroups/deployments/* |  |
> | Microsoft.Storage/storageAccounts/listKeys/action | Retornar as chaves de acesso da conta de armazenamento especificada. |
> | Microsoft.Support/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Log Analytics Contributor can read all monitoring data and edit monitoring settings. Editing monitoring settings includes adding the VM extension to VMs; reading storage account keys to be able to configure collection of logs from Azure Storage; creating and configuring Automation accounts; adding solutions; and configuring Azure diagnostics on all Azure resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/92aaf0da-9dab-42b6-94a3-d43ce8d16293",
  "name": "92aaf0da-9dab-42b6-94a3-d43ce8d16293",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Automation/automationAccounts/*",
        "Microsoft.ClassicCompute/virtualMachines/extensions/*",
        "Microsoft.ClassicStorage/storageAccounts/listKeys/action",
        "Microsoft.Compute/virtualMachines/extensions/*",
        "Microsoft.HybridCompute/machines/extensions/write",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/diagnosticSettings/*",
        "Microsoft.OperationalInsights/*",
        "Microsoft.OperationsManagement/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourcegroups/deployments/*",
        "Microsoft.Storage/storageAccounts/listKeys/action",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Log Analytics Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="log-analytics-reader"></a>Leitor do Log Analytics

Um Leitor do Log Analytics pode exibir e pesquisar todos os dados de monitoramento além de exibir as configurações de monitoramento, incluindo a exibição da configuração do diagnóstico do Azure em todos os recursos do Azure.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | */leitura | Ler recursos de todos os tipos, exceto segredos. |
> | Microsoft.OperationalInsights/workspaces/analytics/query/action | Pesquisar usando o novo mecanismo. |
> | Microsoft.OperationalInsights/workspaces/search/action | Executar uma consulta de pesquisa |
> | Microsoft.Support/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | Microsoft.OperationalInsights/workspaces/sharedKeys/read | Recupera as chaves compartilhadas do workspace. Essas chaves são usadas para conectar agentes do Insights Operacionais da Microsoft ao workspace. |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Log Analytics Reader can view and search all monitoring data as well as and view monitoring settings, including viewing the configuration of Azure diagnostics on all Azure resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/73c42c96-874c-492b-b04d-ab87d138a893",
  "name": "73c42c96-874c-492b-b04d-ab87d138a893",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.OperationalInsights/workspaces/analytics/query/action",
        "Microsoft.OperationalInsights/workspaces/search/action",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.OperationalInsights/workspaces/sharedKeys/read"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Log Analytics Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="blockchain"></a>Blockchain


### <a name="blockchain-member-node-access-preview"></a>Acesso ao nó de membro Blockchain (visualização)

Permite o acesso a nós membro Blockchain

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft. Blockchain/blockchainMembers/transactionNodes/Read | Obtém ou lista os nós de transação de membro Blockchain existentes. |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | Microsoft. Blockchain/blockchainMembers/transactionNodes/Connect/Action | Conecta-se a um nó de transação de membro Blockchain. |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for access to Blockchain Member nodes",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/31a002a1-acaf-453e-8a5b-297c9ca1ea24",
  "name": "31a002a1-acaf-453e-8a5b-297c9ca1ea24",
  "permissions": [
    {
      "actions": [
        "Microsoft.Blockchain/blockchainMembers/transactionNodes/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Blockchain/blockchainMembers/transactionNodes/connect/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Blockchain Member Node Access (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="ai--machine-learning"></a>IA + Machine Learning


### <a name="cognitive-services-contributor"></a>Colaborador dos Serviços Cognitivos

Permite criar, ler, atualizar, excluir e gerenciar chaves dos Serviços Cognitivos.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.CognitiveServices/* |  |
> | Microsoft.Features/features/read | Obter os recursos de uma assinatura. |
> | Microsoft.Features/providers/features/read | Obter o recurso de uma assinatura em determinado provedor de recursos. |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | Microsoft.Insights/diagnosticSettings/* | Criar, atualizar ou ler a configuração de diagnóstico do Analysis Server |
> | Microsoft.Insights/logDefinitions/read | Ler definições de log |
> | Microsoft.Insights/metricdefinitions/read | Ler definições de métrica |
> | Microsoft.Insights/metrics/read | Ler métrica |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
> | Microsoft.Resources/deployments/* | Criar e gerenciar uma implantação |
> | Microsoft.Resources/deployments/operations/read | Obter ou lista operações de implantação. |
> | Microsoft.Resources/subscriptions/operationresults/read | Obter os resultados da operação da assinatura. |
> | Microsoft.Resources/subscriptions/read | Obter a lista de assinaturas. |
> | Microsoft.Resources/subscriptions/resourcegroups/deployments/* |  |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Support/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you create, read, update, delete and manage keys of Cognitive Services.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/25fbc0a9-bd7c-42a3-aa1a-3b75d497ee68",
  "name": "25fbc0a9-bd7c-42a3-aa1a-3b75d497ee68",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.CognitiveServices/*",
        "Microsoft.Features/features/read",
        "Microsoft.Features/providers/features/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/diagnosticSettings/*",
        "Microsoft.Insights/logDefinitions/read",
        "Microsoft.Insights/metricdefinitions/read",
        "Microsoft.Insights/metrics/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourcegroups/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cognitive Services Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-data-reader-preview"></a>Leitor de dados de serviços cognitivas (versão prévia)

Permite que você leia dados de serviços cognitivas.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | *nenhum* |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | Microsoft.CognitiveServices/*/read |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read Cognitive Services data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b59867f0-fa02-499b-be73-45a86b5b3e1c",
  "name": "b59867f0-fa02-499b-be73-45a86b5b3e1c",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/*/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Cognitive Services Data Reader (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-user"></a>Usuário dos Serviços Cognitivos

Permite ler e listar as chaves dos Serviços Cognitivos.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.CognitiveServices/*/read |  |
> | Microsoft.CognitiveServices/accounts/listkeys/action | Listar chaves |
> | Microsoft.Insights/alertRules/read | Ler alerta de métrica clássico |
> | Microsoft.Insights/diagnosticSettings/read | Ler uma configuração de diagnóstico de recurso |
> | Microsoft.Insights/logDefinitions/read | Ler definições de log |
> | Microsoft.Insights/metricdefinitions/read | Ler definições de métrica |
> | Microsoft.Insights/metrics/read | Ler métrica |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
> | Microsoft.Resources/deployments/operations/read | Obter ou lista operações de implantação. |
> | Microsoft.Resources/subscriptions/operationresults/read | Obter os resultados da operação da assinatura. |
> | Microsoft.Resources/subscriptions/read | Obter a lista de assinaturas. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Support/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | Microsoft.CognitiveServices/* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read and list keys of Cognitive Services.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a97b65f3-24c7-4388-baec-2e87135dc908",
  "name": "a97b65f3-24c7-4388-baec-2e87135dc908",
  "permissions": [
    {
      "actions": [
        "Microsoft.CognitiveServices/*/read",
        "Microsoft.CognitiveServices/accounts/listkeys/action",
        "Microsoft.Insights/alertRules/read",
        "Microsoft.Insights/diagnosticSettings/read",
        "Microsoft.Insights/logDefinitions/read",
        "Microsoft.Insights/metricdefinitions/read",
        "Microsoft.Insights/metrics/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Cognitive Services User",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="mixed-reality"></a>Realidade misturada


### <a name="spatial-anchors-account-contributor"></a>Colaborador da conta de âncoras espaciais

Permite gerenciar âncoras espaciais em sua conta, mas não excluí-las

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | *nenhum* |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/criar/ação | Criar âncoras espaciais |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/descoberta/leitura | Descobrir âncoras espaciais próximas |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/Properties/Read | Obter propriedades de âncoras espaciais |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/consulta/leitura | Localizar âncoras espaciais |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/submitdiag/Read | Enviar dados de diagnóstico para ajudar a melhorar a qualidade do serviço âncoras espaciais do Azure |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/Write | Atualizar propriedades de âncoras espaciais |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage spatial anchors in your account, but not delete them",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8bbe83f1-e2a6-4df7-8cb4-4e04d4e5c827",
  "name": "8bbe83f1-e2a6-4df7-8cb4-4e04d4e5c827",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.MixedReality/SpatialAnchorsAccounts/create/action",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/query/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Spatial Anchors Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="spatial-anchors-account-owner"></a>Proprietário da conta das âncoras espaciais

Permite que você gerencie âncoras espaciais em sua conta, incluindo excluí-las

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | *nenhum* |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/criar/ação | Criar âncoras espaciais |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/Delete | Excluir âncoras espaciais |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/descoberta/leitura | Descobrir âncoras espaciais próximas |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/Properties/Read | Obter propriedades de âncoras espaciais |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/consulta/leitura | Localizar âncoras espaciais |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/submitdiag/Read | Enviar dados de diagnóstico para ajudar a melhorar a qualidade do serviço âncoras espaciais do Azure |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/Write | Atualizar propriedades de âncoras espaciais |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage spatial anchors in your account, including deleting them",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/70bbe301-9835-447d-afdd-19eb3167307c",
  "name": "70bbe301-9835-447d-afdd-19eb3167307c",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.MixedReality/SpatialAnchorsAccounts/create/action",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/delete",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/query/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Spatial Anchors Account Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="spatial-anchors-account-reader"></a>Leitor de conta de âncoras espaciais

Permite localizar e ler propriedades de âncoras espaciais em sua conta

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | *nenhum* |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/descoberta/leitura | Descobrir âncoras espaciais próximas |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/Properties/Read | Obter propriedades de âncoras espaciais |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/consulta/leitura | Localizar âncoras espaciais |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/submitdiag/Read | Enviar dados de diagnóstico para ajudar a melhorar a qualidade do serviço âncoras espaciais do Azure |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you locate and read properties of spatial anchors in your account",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5d51204f-eb77-4b1c-b86a-2ec626c49413",
  "name": "5d51204f-eb77-4b1c-b86a-2ec626c49413",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/query/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Spatial Anchors Account Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="integration"></a>Integração


### <a name="api-management-service-contributor"></a>Colaborador de serviço de gerenciamento de API

Pode gerenciar o serviço e as APIs

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.ApiManagement/service/* | Criar e gerenciar o serviço de gerenciamento da API |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
> | Microsoft.Resources/deployments/* | Criar e gerenciar uma implantação |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Support/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage service and the APIs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/312a565d-c81f-4fd8-895a-4e21e48d571c",
  "name": "312a565d-c81f-4fd8-895a-4e21e48d571c",
  "permissions": [
    {
      "actions": [
        "Microsoft.ApiManagement/service/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "API Management Service Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="api-management-service-operator-role"></a>Função do operador de serviço de gerenciamento da API

Pode gerenciar serviços, mas não as APIs

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.ApiManagement/service/*/read | Ler instâncias do serviço de gerenciamento da API |
> | Microsoft.ApiManagement/service/backup/action | Fazer backup do Serviço de Gerenciamento de API para o contêiner especificado em uma conta de armazenamento fornecida pelo usuário |
> | Microsoft.ApiManagement/service/delete | Excluir uma instância do Serviço de Gerenciamento de API |
> | Microsoft.ApiManagement/service/managedeployments/action | Alterar SKU/unidades, adicionar/remover implantações regionais do Serviço de Gerenciamento de API |
> | Microsoft.ApiManagement/service/read | Ler metadados de uma instância do Serviço de Gerenciamento de API |
> | Microsoft.ApiManagement/service/restore/action | Restaurar o Serviço de Gerenciamento de API do contêiner especificado em uma conta de armazenamento fornecida pelo usuário |
> | Microsoft.ApiManagement/service/updatecertificate/action | Carregar certificado TLS/SSL para um serviço de gerenciamento de API |
> | Microsoft.ApiManagement/service/updatehostname/action | Configurar, atualizar ou remover nomes de domínio personalizado para um Serviço de Gerenciamento de API |
> | Microsoft.ApiManagement/service/write | Criar ou atualizar a instância do serviço de gerenciamento de API |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
> | Microsoft.Resources/deployments/* | Criar e gerenciar uma implantação |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Support/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | Microsoft.ApiManagement/service/users/keys/read | Obter chaves associadas ao usuário |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage service but not the APIs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e022efe7-f5ba-4159-bbe4-b44f577e9b61",
  "name": "e022efe7-f5ba-4159-bbe4-b44f577e9b61",
  "permissions": [
    {
      "actions": [
        "Microsoft.ApiManagement/service/*/read",
        "Microsoft.ApiManagement/service/backup/action",
        "Microsoft.ApiManagement/service/delete",
        "Microsoft.ApiManagement/service/managedeployments/action",
        "Microsoft.ApiManagement/service/read",
        "Microsoft.ApiManagement/service/restore/action",
        "Microsoft.ApiManagement/service/updatecertificate/action",
        "Microsoft.ApiManagement/service/updatehostname/action",
        "Microsoft.ApiManagement/service/write",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.ApiManagement/service/users/keys/read"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "API Management Service Operator Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="api-management-service-reader-role"></a>Função de leitor do Serviço de Gerenciamento de API

Acesso somente leitura ao serviço e APIs

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.ApiManagement/service/*/read | Ler instâncias do serviço de gerenciamento da API |
> | Microsoft.ApiManagement/service/read | Ler metadados de uma instância do Serviço de Gerenciamento de API |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
> | Microsoft.Resources/deployments/* | Criar e gerenciar uma implantação |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Support/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | Microsoft.ApiManagement/service/users/keys/read | Obter chaves associadas ao usuário |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read-only access to service and APIs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/71522526-b88f-4d52-b57f-d31fc3546d0d",
  "name": "71522526-b88f-4d52-b57f-d31fc3546d0d",
  "permissions": [
    {
      "actions": [
        "Microsoft.ApiManagement/service/*/read",
        "Microsoft.ApiManagement/service/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.ApiManagement/service/users/keys/read"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "API Management Service Reader Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="app-configuration-data-owner"></a>Proprietário dos dados de configuração do aplicativo

Permite acesso completo aos dados de configuração do aplicativo.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | *nenhum* |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | Microsoft. AppConfiguration/configurationStores/*/Read |  |
> | Microsoft. AppConfiguration/configurationStores/*/Write |  |
> | Microsoft. AppConfiguration/configurationStores/*/Delete |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows full access to App Configuration data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5ae67dd6-50cb-40e7-96ff-dc2bfa4b606b",
  "name": "5ae67dd6-50cb-40e7-96ff-dc2bfa4b606b",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.AppConfiguration/configurationStores/*/read",
        "Microsoft.AppConfiguration/configurationStores/*/write",
        "Microsoft.AppConfiguration/configurationStores/*/delete"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "App Configuration Data Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="app-configuration-data-reader"></a>Leitor de dados de configuração de aplicativo

Permite acesso de leitura aos dados de configuração do aplicativo.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | *nenhum* |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | Microsoft. AppConfiguration/configurationStores/*/Read |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows read access to App Configuration data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/516239f1-63e1-4d78-a4de-a74fb236a071",
  "name": "516239f1-63e1-4d78-a4de-a74fb236a071",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.AppConfiguration/configurationStores/*/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "App Configuration Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-service-bus-data-owner"></a>Proprietário de dados do barramento de serviço do Azure

Permite acesso completo aos recursos do barramento de serviço do Azure.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft. ServiceBus/* |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | Microsoft. ServiceBus/* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for full access to Azure Service Bus resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/090c5cfd-751d-490a-894a-3ce6f1109419",
  "name": "090c5cfd-751d-490a-894a-3ce6f1109419",
  "permissions": [
    {
      "actions": [
        "Microsoft.ServiceBus/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ServiceBus/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Service Bus Data Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-service-bus-data-receiver"></a>Receptor de dados do barramento de serviço do Azure

Permite o acesso de recebimento aos recursos do barramento de serviço do Azure.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft. ServiceBus/*/Queues/Read |  |
> | Microsoft. ServiceBus/*/topics/Read |  |
> | Microsoft. ServiceBus/*/topics/subscriptions/Read |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | Microsoft. ServiceBus/*/Receive/Action |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for receive access to Azure Service Bus resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4f6d3b9b-027b-4f4c-9142-0e5a2a2247e0",
  "name": "4f6d3b9b-027b-4f4c-9142-0e5a2a2247e0",
  "permissions": [
    {
      "actions": [
        "Microsoft.ServiceBus/*/queues/read",
        "Microsoft.ServiceBus/*/topics/read",
        "Microsoft.ServiceBus/*/topics/subscriptions/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ServiceBus/*/receive/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Service Bus Data Receiver",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-service-bus-data-sender"></a>Remetente de dados do barramento de serviço do Azure

Permite o acesso de envio aos recursos do barramento de serviço do Azure.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft. ServiceBus/*/Queues/Read |  |
> | Microsoft. ServiceBus/*/topics/Read |  |
> | Microsoft. ServiceBus/*/topics/subscriptions/Read |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | Microsoft. ServiceBus/*/Send/Action |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for send access to Azure Service Bus resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/69a216fc-b8fb-44d8-bc22-1f3c2cd27a39",
  "name": "69a216fc-b8fb-44d8-bc22-1f3c2cd27a39",
  "permissions": [
    {
      "actions": [
        "Microsoft.ServiceBus/*/queues/read",
        "Microsoft.ServiceBus/*/topics/read",
        "Microsoft.ServiceBus/*/topics/subscriptions/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ServiceBus/*/send/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Service Bus Data Sender",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-stack-registration-owner"></a>Proprietário de registro do Azure Stack

Permite que você gerencie registros do Microsoft Azure Stack.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft. AzureStack/registrations/Products/*/Action |  |
> | Microsoft.AzureStack/registrations/products/read | Obter as propriedades de um produto do Marketplace do Azure Stack |
> | Microsoft.AzureStack/registrations/read | Obter as propriedades de um registro do Microsoft Azure Stack |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Azure Stack registrations.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/6f12a6df-dd06-4f3e-bcb1-ce8be600526a",
  "name": "6f12a6df-dd06-4f3e-bcb1-ce8be600526a",
  "permissions": [
    {
      "actions": [
        "Microsoft.AzureStack/registrations/products/*/action",
        "Microsoft.AzureStack/registrations/products/read",
        "Microsoft.AzureStack/registrations/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Stack Registration Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="eventgrid-eventsubscription-contributor"></a>Colaborador de EventGrid EventSubscription

Permite que você gerencie operações de assinatura de evento EventGrid.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.EventGrid/eventSubscriptions/* |  |
> | Microsoft.EventGrid/topicTypes/eventSubscriptions/read | Listar assinaturas de eventos globais por tipo de tópico |
> | Microsoft.EventGrid/locations/eventSubscriptions/read | Listar assinaturas de eventos regionais |
> | Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read | Listar assinaturas de eventos regionais por tipo de tópico |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | Microsoft.Resources/deployments/* | Criar e gerenciar uma implantação |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Support/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage EventGrid event subscription operations.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/428e0ff0-5e57-4d9c-a221-2c70d0e0a443",
  "name": "428e0ff0-5e57-4d9c-a221-2c70d0e0a443",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.EventGrid/eventSubscriptions/*",
        "Microsoft.EventGrid/topicTypes/eventSubscriptions/read",
        "Microsoft.EventGrid/locations/eventSubscriptions/read",
        "Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "EventGrid EventSubscription Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="eventgrid-eventsubscription-reader"></a>Leitor de EventGrid EventSubscription

Permite que você gerencie operações de assinatura de evento EventGrid.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.EventGrid/eventSubscriptions/read | Ler um eventSubscription |
> | Microsoft.EventGrid/topicTypes/eventSubscriptions/read | Listar assinaturas de eventos globais por tipo de tópico |
> | Microsoft.EventGrid/locations/eventSubscriptions/read | Listar assinaturas de eventos regionais |
> | Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read | Listar assinaturas de eventos regionais por tipo de tópico |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read EventGrid event subscriptions.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2414bbcf-6497-4faf-8c65-045460748405",
  "name": "2414bbcf-6497-4faf-8c65-045460748405",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.EventGrid/eventSubscriptions/read",
        "Microsoft.EventGrid/topicTypes/eventSubscriptions/read",
        "Microsoft.EventGrid/locations/eventSubscriptions/read",
        "Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "EventGrid EventSubscription Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="intelligent-systems-account-contributor"></a>Colaborador de conta do sistemas inteligentes

Permite gerenciar contas do Intelligent Systems, mas não acessá-las.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | Microsoft.IntelligentSystems/accounts/* | Criar e gerenciar contas do Sistemas Inteligentes |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
> | Microsoft.Resources/deployments/* | Criar e gerenciar uma implantação |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Support/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Intelligent Systems accounts, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/03a6d094-3444-4b3d-88af-7477090a9e5e",
  "name": "03a6d094-3444-4b3d-88af-7477090a9e5e",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.IntelligentSystems/accounts/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Intelligent Systems Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="logic-app-contributor"></a>Colaborador de aplicativo lógico

Permite que você gerencie aplicativos lógicos, mas não altere o acesso a eles.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Listar as chaves de acesso das contas de armazenamento. |
> | Microsoft.ClassicStorage/storageAccounts/read | Retornar a conta de armazenamento com a conta fornecida. |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | Microsoft. insights/metricAlerts/* |  |
> | Microsoft.Insights/diagnosticSettings/* | Criar, atualizar ou ler a configuração de diagnóstico do Analysis Server |
> | Microsoft.Insights/logdefinitions/* | Essa permissão é necessária para usuários que precisam de acesso aos Logs de atividade por meio do portal. Liste as categorias de log no Log de Atividades. |
> | Microsoft.Insights/metricDefinitions/* | Ler definições de métricas (lista de tipos de métrica disponíveis para um recurso). |
> | Microsoft.Logic/* | Gerenciar recursos de Aplicativos Lógicos. |
> | Microsoft.Resources/deployments/* | Criar e gerenciar uma implantação |
> | Microsoft.Resources/subscriptions/operationresults/read | Obter os resultados da operação da assinatura. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Storage/storageAccounts/listkeys/action | Retornar as chaves de acesso da conta de armazenamento especificada. |
> | Microsoft.Storage/storageAccounts/read | Retornar a lista de contas de armazenamento ou obter as propriedades da conta de armazenamento especificada. |
> | Microsoft.Support/* | Criar e atualizar um tíquete de suporte |
> | Microsoft.Web/connectionGateways/* | Criar e gerenciar um Gateway de Conexão. |
> | Microsoft.Web/connections/* | Criar e gerenciar uma Conexão. |
> | Microsoft.Web/customApis/* | Criar e gerenciar uma API Personalizada. |
> | Microsoft.Web/serverFarms/join/action |  |
> | Microsoft.Web/serverFarms/read | Obter as propriedades em um Plano do Serviço de Aplicativo |
> | Microsoft.Web/sites/functions/listSecrets/action | Listar segredos de função. |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage logic app, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/87a39d53-fc1b-424a-814c-f7e04687dc9e",
  "name": "87a39d53-fc1b-424a-814c-f7e04687dc9e",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicStorage/storageAccounts/listKeys/action",
        "Microsoft.ClassicStorage/storageAccounts/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/metricAlerts/*",
        "Microsoft.Insights/diagnosticSettings/*",
        "Microsoft.Insights/logdefinitions/*",
        "Microsoft.Insights/metricDefinitions/*",
        "Microsoft.Logic/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/listkeys/action",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.Support/*",
        "Microsoft.Web/connectionGateways/*",
        "Microsoft.Web/connections/*",
        "Microsoft.Web/customApis/*",
        "Microsoft.Web/serverFarms/join/action",
        "Microsoft.Web/serverFarms/read",
        "Microsoft.Web/sites/functions/listSecrets/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Logic App Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="logic-app-operator"></a>Operador de aplicativo lógico

Permite ler, habilitar e desabilitar aplicativos lógicos, mas não editá-los ou atualizá-los.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Insights/alertRules/*/read | Ler regras de alerta do Insights |
> | Microsoft. insights/metricAlerts/*/Read |  |
> | Microsoft.Insights/diagnosticSettings/*/read | Obter configurações de diagnóstico para Aplicativos Lógicos |
> | Microsoft.Insights/metricDefinitions/*/read | Obter as métricas disponíveis para Aplicativos Lógicos. |
> | Microsoft.Logic/*/read | Ler recursos de Aplicativos Lógicos. |
> | Microsoft.Logic/workflows/disable/action | Desabilitar o fluxo de trabalho. |
> | Microsoft.Logic/workflows/enable/action | Permitir o fluxo de trabalho. |
> | Microsoft.Logic/workflows/validate/action | Valida o fluxo de trabalho. |
> | Microsoft.Resources/deployments/operations/read | Obter ou lista operações de implantação. |
> | Microsoft.Resources/subscriptions/operationresults/read | Obter os resultados da operação da assinatura. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Support/* | Criar e atualizar um tíquete de suporte |
> | Microsoft.Web/connectionGateways/*/read | Ler gateways de conexão. |
> | Microsoft.Web/connections/*/read | Ler conexões. |
> | Microsoft.Web/customApis/*/read | Ler API personalizada. |
> | Microsoft.Web/serverFarms/read | Obter as propriedades em um Plano do Serviço de Aplicativo |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read, enable and disable logic app.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/515c2055-d9d4-4321-b1b9-bd0c9a0f79fe",
  "name": "515c2055-d9d4-4321-b1b9-bd0c9a0f79fe",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*/read",
        "Microsoft.Insights/metricAlerts/*/read",
        "Microsoft.Insights/diagnosticSettings/*/read",
        "Microsoft.Insights/metricDefinitions/*/read",
        "Microsoft.Logic/*/read",
        "Microsoft.Logic/workflows/disable/action",
        "Microsoft.Logic/workflows/enable/action",
        "Microsoft.Logic/workflows/validate/action",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Web/connectionGateways/*/read",
        "Microsoft.Web/connections/*/read",
        "Microsoft.Web/customApis/*/read",
        "Microsoft.Web/serverFarms/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Logic App Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="identity"></a>Identidade


### <a name="managed-identity-contributor"></a>Colaborador de Identidade Gerenciada

Criar, ler, atualizar e excluir a identidade atribuída pelo usuário

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/read | Obter uma identidade atribuída ao usuário existente |
> | Microsoft.ManagedIdentity/userAssignedIdentities/write | Criar uma nova identidade atribuída ao usuário ou atualizar as marcas associadas a uma identidade atribuída a um usuário existente |
> | Microsoft.ManagedIdentity/userAssignedIdentities/delete | Excluir uma identidade atribuída ao usuário existente |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Resources/deployments/* | Criar e gerenciar uma implantação |
> | Microsoft.Support/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Create, Read, Update, and Delete User Assigned Identity",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e40ec5ca-96e0-45a2-b4ff-59039f2c2b59",
  "name": "e40ec5ca-96e0-45a2-b4ff-59039f2c2b59",
  "permissions": [
    {
      "actions": [
        "Microsoft.ManagedIdentity/userAssignedIdentities/read",
        "Microsoft.ManagedIdentity/userAssignedIdentities/write",
        "Microsoft.ManagedIdentity/userAssignedIdentities/delete",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Identity Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-identity-operator"></a>Operador de Identidade Gerenciada

Ler e atribuir identidade atribuída pelo usuário

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/read |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/assign/action |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Resources/deployments/* | Criar e gerenciar uma implantação |
> | Microsoft.Support/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read and Assign User Assigned Identity",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/f1a07417-d97a-45cb-824c-7a7467783830",
  "name": "f1a07417-d97a-45cb-824c-7a7467783830",
  "permissions": [
    {
      "actions": [
        "Microsoft.ManagedIdentity/userAssignedIdentities/*/read",
        "Microsoft.ManagedIdentity/userAssignedIdentities/*/assign/action",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Identity Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="security"></a>Segurança


### <a name="azure-sentinel-contributor"></a>Colaborador do Azure Sentinel

Colaborador do Azure Sentinel

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft. SecurityInsights/* |  |
> | Microsoft.OperationalInsights/workspaces/analytics/query/action | Pesquisar usando o novo mecanismo. |
> | Microsoft.OperationalInsights/workspaces/*/read | Exibir dados do log Analytics |
> | Microsoft.OperationalInsights/workspaces/savedSearches/* |  |
> | Microsoft.OperationsManagement/solutions/read | Obter solução OMS de saída |
> | Microsoft.OperationalInsights/workspaces/query/read | Executar consultas dos dados no workspace |
> | Microsoft. OperationalInsights/Workspaces/Query/*/Read |  |
> | Microsoft. OperationalInsights/Workspaces/fontes de trabalho/leitura | Obter fontes de dados em um workspace. |
> | Microsoft. insights/pastas de trabalho/* |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | Microsoft.Resources/deployments/* | Criar e gerenciar uma implantação |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Support/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Azure Sentinel Contributor",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ab8e14d6-4a74-4a29-9ba8-549422addade",
  "name": "ab8e14d6-4a74-4a29-9ba8-549422addade",
  "permissions": [
    {
      "actions": [
        "Microsoft.SecurityInsights/*",
        "Microsoft.OperationalInsights/workspaces/analytics/query/action",
        "Microsoft.OperationalInsights/workspaces/*/read",
        "Microsoft.OperationalInsights/workspaces/savedSearches/*",
        "Microsoft.OperationsManagement/solutions/read",
        "Microsoft.OperationalInsights/workspaces/query/read",
        "Microsoft.OperationalInsights/workspaces/query/*/read",
        "Microsoft.OperationalInsights/workspaces/dataSources/read",
        "Microsoft.Insights/workbooks/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Sentinel Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-sentinel-reader"></a>Leitor do Azure Sentinel

Leitor do Azure Sentinel

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft. SecurityInsights/*/Read |  |
> | Microsoft. SecurityInsights/dataConnectorsCheckRequirements/Action | Verificar a autorização e a licença do usuário |
> | Microsoft.OperationalInsights/workspaces/analytics/query/action | Pesquisar usando o novo mecanismo. |
> | Microsoft.OperationalInsights/workspaces/*/read | Exibir dados do log Analytics |
> | Microsoft. OperationalInsights/Workspaces/aslinkservices/Read | Obter serviços vinculados em um determinado workspace. |
> | Microsoft.OperationalInsights/workspaces/savedSearches/read | Obter uma consulta de pesquisa salva |
> | Microsoft.OperationsManagement/solutions/read | Obter solução OMS de saída |
> | Microsoft.OperationalInsights/workspaces/query/read | Executar consultas dos dados no workspace |
> | Microsoft. OperationalInsights/Workspaces/Query/*/Read |  |
> | Microsoft. OperationalInsights/Workspaces/fontes de trabalho/leitura | Obter fontes de dados em um workspace. |
> | Microsoft. insights/pastas de trabalho/leitura | Ler uma pasta de trabalho |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | Microsoft.Resources/deployments/* | Criar e gerenciar uma implantação |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Support/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Azure Sentinel Reader",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8d289c81-5878-46d4-8554-54e1e3d8b5cb",
  "name": "8d289c81-5878-46d4-8554-54e1e3d8b5cb",
  "permissions": [
    {
      "actions": [
        "Microsoft.SecurityInsights/*/read",
        "Microsoft.SecurityInsights/dataConnectorsCheckRequirements/action",
        "Microsoft.OperationalInsights/workspaces/analytics/query/action",
        "Microsoft.OperationalInsights/workspaces/*/read",
        "Microsoft.OperationalInsights/workspaces/LinkedServices/read",
        "Microsoft.OperationalInsights/workspaces/savedSearches/read",
        "Microsoft.OperationsManagement/solutions/read",
        "Microsoft.OperationalInsights/workspaces/query/read",
        "Microsoft.OperationalInsights/workspaces/query/*/read",
        "Microsoft.OperationalInsights/workspaces/dataSources/read",
        "Microsoft.Insights/workbooks/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Sentinel Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-sentinel-responder"></a>Respondente do Azure Sentinel

Respondente do Azure Sentinel

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft. SecurityInsights/*/Read |  |
> | Microsoft. SecurityInsights/dataConnectorsCheckRequirements/Action | Verificar a autorização e a licença do usuário |
> | Microsoft. SecurityInsights/cases/* |  |
> | Microsoft. SecurityInsights/Incidents/* |  |
> | Microsoft.OperationalInsights/workspaces/analytics/query/action | Pesquisar usando o novo mecanismo. |
> | Microsoft.OperationalInsights/workspaces/*/read | Exibir dados do log Analytics |
> | Microsoft. OperationalInsights/Workspaces/fontes de trabalho/leitura | Obter fontes de dados em um workspace. |
> | Microsoft.OperationalInsights/workspaces/savedSearches/read | Obter uma consulta de pesquisa salva |
> | Microsoft.OperationsManagement/solutions/read | Obter solução OMS de saída |
> | Microsoft.OperationalInsights/workspaces/query/read | Executar consultas dos dados no workspace |
> | Microsoft. OperationalInsights/Workspaces/Query/*/Read |  |
> | Microsoft. OperationalInsights/Workspaces/fontes de trabalho/leitura | Obter fontes de dados em um workspace. |
> | Microsoft. insights/pastas de trabalho/leitura | Ler uma pasta de trabalho |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | Microsoft.Resources/deployments/* | Criar e gerenciar uma implantação |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Support/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Azure Sentinel Responder",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3e150937-b8fe-4cfb-8069-0eaf05ecd056",
  "name": "3e150937-b8fe-4cfb-8069-0eaf05ecd056",
  "permissions": [
    {
      "actions": [
        "Microsoft.SecurityInsights/*/read",
        "Microsoft.SecurityInsights/dataConnectorsCheckRequirements/action",
        "Microsoft.SecurityInsights/cases/*",
        "Microsoft.SecurityInsights/incidents/*",
        "Microsoft.OperationalInsights/workspaces/analytics/query/action",
        "Microsoft.OperationalInsights/workspaces/*/read",
        "Microsoft.OperationalInsights/workspaces/dataSources/read",
        "Microsoft.OperationalInsights/workspaces/savedSearches/read",
        "Microsoft.OperationsManagement/solutions/read",
        "Microsoft.OperationalInsights/workspaces/query/read",
        "Microsoft.OperationalInsights/workspaces/query/*/read",
        "Microsoft.OperationalInsights/workspaces/dataSources/read",
        "Microsoft.Insights/workbooks/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Sentinel Responder",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-contributor"></a>Colaborador do Key Vault

Permite gerenciar cofres de chaves, mas não acessá-los.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | Microsoft.KeyVault/* |  |
> | Microsoft.Resources/deployments/* | Criar e gerenciar uma implantação |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Support/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | Microsoft.KeyVault/locations/deletedVaults/purge/action | Limpar um cofre de chaves com exclusão reversível |
> | Microsoft.KeyVault/hsmPools/* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage key vaults, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/f25e0fa2-a7c8-4377-a976-54943a77a395",
  "name": "f25e0fa2-a7c8-4377-a976-54943a77a395",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.KeyVault/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.KeyVault/locations/deletedVaults/purge/action",
        "Microsoft.KeyVault/hsmPools/*"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="security-admin"></a>Administrador de Segurança

Permissões de exibição e atualização para a central de segurança. Mesmas permissões que a função leitor de segurança e também podem atualizar a política de segurança e ignorar alertas e recomendações.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Authorization/policyAssignments/* | Criar e gerenciar atribuições de política |
> | Microsoft.Authorization/policyDefinitions/* | Criar e gerenciar definições de política |
> | Microsoft.Authorization/policySetDefinitions/* | Criar e gerenciar conjuntos de política |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | Microsoft.Management/managementGroups/read | Listar grupos de gerenciamento para o usuário autenticado. |
> | Microsoft.operationalInsights/workspaces/*/read | Exibir dados do log Analytics |
> | Microsoft.Resources/deployments/* | Criar e gerenciar uma implantação |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Security/* | Criar e gerenciar políticas e componentes de segurança |
> | Microsoft.Support/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Security Admin Role",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fb1c8493-542b-48eb-b624-b4c8fea62acd",
  "name": "fb1c8493-542b-48eb-b624-b4c8fea62acd",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Authorization/policyAssignments/*",
        "Microsoft.Authorization/policyDefinitions/*",
        "Microsoft.Authorization/policySetDefinitions/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Management/managementGroups/read",
        "Microsoft.operationalInsights/workspaces/*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Security/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Security Admin",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="security-assessment-contributor"></a>Colaborador de avaliação de segurança

Permite enviar avaliações por push para a central de segurança

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft. Security/avaliações/gravação | Criar ou atualizar avaliações de segurança em sua assinatura |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you push assessments to Security Center",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/612c2aa1-cb24-443b-ac28-3ab7272de6f5",
  "name": "612c2aa1-cb24-443b-ac28-3ab7272de6f5",
  "permissions": [
    {
      "actions": [
        "Microsoft.Security/assessments/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Security Assessment Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="security-manager-legacy"></a>Gerenciador de Segurança (Herdado)

Esta é uma função herdada. Em vez disso, use o administrador de segurança.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.ClassicCompute/*/read | Ler informações de configuração de máquinas virtuais clássicas |
> | Microsoft.ClassicCompute/virtualMachines/*/write | Escrever configurações para máquinas virtuais clássicas |
> | Microsoft.ClassicNetwork/*/read | Ler informações de configuração sobre a rede clássica |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
> | Microsoft.Resources/deployments/* | Criar e gerenciar uma implantação |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Security/* | Criar e gerenciar políticas e componentes de segurança |
> | Microsoft.Support/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "This is a legacy role. Please use Security Administrator instead",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e3d13bf0-dd5a-482e-ba6b-9b8433878d10",
  "name": "e3d13bf0-dd5a-482e-ba6b-9b8433878d10",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicCompute/*/read",
        "Microsoft.ClassicCompute/virtualMachines/*/write",
        "Microsoft.ClassicNetwork/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Security/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Security Manager (Legacy)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="security-reader"></a>Leitor de segurança

Exibir permissões para a central de segurança. Pode exibir recomendações, alertas, uma política de segurança e Estados de segurança, mas não pode fazer alterações.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | Microsoft.operationalInsights/workspaces/*/read | Exibir dados do log Analytics |
> | Microsoft.Resources/deployments/* | Criar e gerenciar uma implantação |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Security/*/read | Ler componentes de segurança e políticas |
> | Microsoft.Support/* | Criar e atualizar um tíquete de suporte |
> | Microsoft.Management/managementGroups/read | Listar grupos de gerenciamento para o usuário autenticado. |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Security Reader Role",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/39bc4728-0917-49c7-9d2c-d95423bc2eb4",
  "name": "39bc4728-0917-49c7-9d2c-d95423bc2eb4",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.operationalInsights/workspaces/*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Security/*/read",
        "Microsoft.Support/*",
        "Microsoft.Management/managementGroups/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Security Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="devops"></a>DevOps


### <a name="devtest-labs-user"></a>Usuário do DevTest Labs

Permite conectar, iniciar, reiniciar e encerrar as máquinas virtuais no Azure DevTest Labs.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Compute/availabilitySets/read | Obter as propriedades de um conjunto de disponibilidade |
> | Microsoft.Compute/virtualMachines/*/read | Ler as propriedades de uma máquina virtual (tamanhos de VM, status de runtime, extensões de VM etc.) |
> | Microsoft.Compute/virtualMachines/deallocate/action | Desligar a máquina virtual e liberar os recursos de computação |
> | Microsoft.Compute/virtualMachines/read | Obter as propriedades de uma máquina virtual |
> | Microsoft.Compute/virtualMachines/restart/action | Reinicia a máquina virtual |
> | Microsoft.Compute/virtualMachines/start/action | Inicia a máquina virtual |
> | Microsoft.DevTestLab/*/read | Ler as propriedades de um laboratório |
> | Microsoft.DevTestLab/labs/claimAnyVm/action | Declarar uma máquina virtual aleatória declarável no laboratório. |
> | Microsoft.DevTestLab/labs/createEnvironment/action | Criar máquinas virtuais em um laboratório. |
> | Microsoft. DevTestLab/Labs/ensureCurrentUserProfile/Action | Verifique se o usuário atual tem um perfil válido no laboratório. |
> | Microsoft.DevTestLab/labs/formulas/delete | Excluir fórmulas. |
> | Microsoft.DevTestLab/labs/formulas/read | Ler fórmulas. |
> | Microsoft.DevTestLab/labs/formulas/write | Adicionar ou modificar fórmulas. |
> | Microsoft.DevTestLab/labs/policySets/evaluatePolicies/action | Avaliar a política de laboratório. |
> | Microsoft.DevTestLab/labs/virtualMachines/claim/action | Assumir o controle de uma máquina virtual existente |
> | Microsoft.DevTestLab/labs/virtualMachines/ListApplicableSchedules/action | Lista as agendas de iniciar/parar aplicáveis, se houver alguma. |
> | Microsoft. DevTestLab/Labs/virtualMachines/getRdpFileContents/Action | Obtém uma cadeia de caracteres que representa o conteúdo do arquivo RDP para a máquina virtual |
> | Microsoft.Network/loadBalancers/backendAddressPools/join/action | Une um pool de endereços de back-end do balanceador de carga. Não é possível alertá-lo. |
> | Microsoft.Network/loadBalancers/inboundNatRules/join/action | Une uma regra NAT de entrada do balanceador de carga. Não é possível alertá-lo. |
> | Microsoft.Network/networkInterfaces/*/read | Ler as propriedades de uma interface de rede (por exemplo, todos os balanceadores de carga dos quais o adaptador de rede faz parte) |
> | Microsoft.Network/networkInterfaces/join/action | Une uma máquina virtual a uma interface de rede. Não é possível alertá-lo. |
> | Microsoft.Network/networkInterfaces/read | Obter uma definição de adaptador de rede.  |
> | Microsoft.Network/networkInterfaces/write | Criar uma interface de rede ou atualizar uma interface de rede existente.  |
> | Microsoft.Network/publicIPAddresses/*/read | Ler as propriedades de um endereço IP público |
> | Microsoft.Network/publicIPAddresses/join/action | Une um endereço IP público. Não é possível alertá-lo. |
> | Microsoft.Network/publicIPAddresses/read | Obter uma definição de endereço IP público. |
> | Microsoft.Network/virtualNetworks/subnets/join/action | Une uma rede virtual. Não é possível alertá-lo. |
> | Microsoft.Resources/deployments/operations/read | Obter ou lista operações de implantação. |
> | Microsoft.Resources/deployments/read | Obter ou lista implantações. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Storage/storageAccounts/listKeys/action | Retornar as chaves de acesso da conta de armazenamento especificada. |
> | **NotActions** |  |
> | Microsoft.Compute/virtualMachines/vmSizes/read | Listar os tamanhos disponíveis para os quais a máquina virtual possa ser atualizada |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you connect, start, restart, and shutdown your virtual machines in your Azure DevTest Labs.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/76283e04-6283-4c54-8f91-bcf1374a3c64",
  "name": "76283e04-6283-4c54-8f91-bcf1374a3c64",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Compute/availabilitySets/read",
        "Microsoft.Compute/virtualMachines/*/read",
        "Microsoft.Compute/virtualMachines/deallocate/action",
        "Microsoft.Compute/virtualMachines/read",
        "Microsoft.Compute/virtualMachines/restart/action",
        "Microsoft.Compute/virtualMachines/start/action",
        "Microsoft.DevTestLab/*/read",
        "Microsoft.DevTestLab/labs/claimAnyVm/action",
        "Microsoft.DevTestLab/labs/createEnvironment/action",
        "Microsoft.DevTestLab/labs/ensureCurrentUserProfile/action",
        "Microsoft.DevTestLab/labs/formulas/delete",
        "Microsoft.DevTestLab/labs/formulas/read",
        "Microsoft.DevTestLab/labs/formulas/write",
        "Microsoft.DevTestLab/labs/policySets/evaluatePolicies/action",
        "Microsoft.DevTestLab/labs/virtualMachines/claim/action",
        "Microsoft.DevTestLab/labs/virtualmachines/listApplicableSchedules/action",
        "Microsoft.DevTestLab/labs/virtualMachines/getRdpFileContents/action",
        "Microsoft.Network/loadBalancers/backendAddressPools/join/action",
        "Microsoft.Network/loadBalancers/inboundNatRules/join/action",
        "Microsoft.Network/networkInterfaces/*/read",
        "Microsoft.Network/networkInterfaces/join/action",
        "Microsoft.Network/networkInterfaces/read",
        "Microsoft.Network/networkInterfaces/write",
        "Microsoft.Network/publicIPAddresses/*/read",
        "Microsoft.Network/publicIPAddresses/join/action",
        "Microsoft.Network/publicIPAddresses/read",
        "Microsoft.Network/virtualNetworks/subnets/join/action",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Resources/deployments/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/listKeys/action"
      ],
      "notActions": [
        "Microsoft.Compute/virtualMachines/vmSizes/read"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "DevTest Labs User",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="lab-creator"></a>Criador de laboratório

Permite a você criar, gerenciar e excluir os laboratórios gerenciados nas contas de laboratório do Azure.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.LabServices/labAccounts/*/read |  |
> | Microsoft.LabServices/labAccounts/createLab/action | Criar um laboratório em uma conta de laboratório. |
> | Microsoft.LabServices/labAccounts/sizes/getRegionalAvailability/action |  |
> | Microsoft.LabServices/labAccounts/getRegionalAvailability/action | Obter informações sobre disponibilidade regional para cada categoria de tamanho configurado em uma conta de laboratório |
> | Microsoft. LabServices/labAccounts/getPricingAndAvailability/Action | Obtenha o preço e a disponibilidade de combinações de tamanhos, geografias e sistemas operacionais para a conta de laboratório. |
> | Microsoft. LabServices/labAccounts/getRestrictionsAndUsage/Action | Obter restrições e uso de núcleo para esta assinatura |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Support/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you create, manage, delete your managed labs under your Azure Lab Accounts.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b97fb8bc-a8b2-4522-a38b-dd33c7e65ead",
  "name": "b97fb8bc-a8b2-4522-a38b-dd33c7e65ead",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.LabServices/labAccounts/*/read",
        "Microsoft.LabServices/labAccounts/createLab/action",
        "Microsoft.LabServices/labAccounts/sizes/getRegionalAvailability/action",
        "Microsoft.LabServices/labAccounts/getRegionalAvailability/action",
        "Microsoft.LabServices/labAccounts/getPricingAndAvailability/action",
        "Microsoft.LabServices/labAccounts/getRestrictionsAndUsage/action",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Lab Creator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="monitor"></a>Monitor


### <a name="application-insights-component-contributor"></a>Colaborador de componente do Application Insights

Pode gerenciar os componentes do Application Insights

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta clássicas |
> | Microsoft. insights/metricAlerts/* | Criar e gerenciar novas regras de alerta |
> | Microsoft.Insights/components/* | Criar e gerenciar componentes do Insights |
> | Microsoft.Insights/webtests/* | Criar e gerenciar testes na Web do insights |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
> | Microsoft.Resources/deployments/* | Criar e gerenciar uma implantação |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Support/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage Application Insights components",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ae349356-3a1b-4a5e-921d-050484c6347e",
  "name": "ae349356-3a1b-4a5e-921d-050484c6347e",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/metricAlerts/*",
        "Microsoft.Insights/components/*",
        "Microsoft.Insights/webtests/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Application Insights Component Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="application-insights-snapshot-debugger"></a>Depurador de Instantâneos do Application Insights

Concede permissão ao usuário para exibir e baixar os instantâneos de depuração coletados com o Depurador de Instantâneos do Application Insights. Observe que essas permissões não estão incluídas nas funções [Proprietário](#owner) ou [Colaborador](#contributor). Ao conceder aos usuários a função de Depurador de Instantâneos Application Insights, você deve conceder a função diretamente ao usuário. A função não é reconhecida quando adicionada a uma função personalizada. 

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | Microsoft.Insights/components/*/read |  |
> | Microsoft.Resources/deployments/* | Criar e gerenciar uma implantação |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Support/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Gives user permission to use Application Insights Snapshot Debugger features",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/08954f03-6346-4c2e-81c0-ec3a5cfae23b",
  "name": "08954f03-6346-4c2e-81c0-ec3a5cfae23b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/components/*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Application Insights Snapshot Debugger",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="monitoring-contributor"></a>Colaborador de monitoramento

Pode ler todos os dados de monitoramento e editar configurações de monitoramento. Consulte também [Introdução às funções, permissões e segurança com o Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/roles-permissions-security#built-in-monitoring-roles).

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | */leitura | Ler recursos de todos os tipos, exceto segredos. |
> | Microsoft.AlertsManagement/alerts/* |  |
> | Microsoft.AlertsManagement/alertsSummary/* |  |
> | Microsoft.Insights/actiongroups/* |  |
> | Microsoft.Insights/activityLogAlerts/* |  |
> | Microsoft.Insights/AlertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | Microsoft.Insights/components/* | Criar e gerenciar componentes do Insights |
> | Microsoft.Insights/DiagnosticSettings/* | Criar, atualizar ou ler a configuração de diagnóstico do Analysis Server |
> | Microsoft.Insights/eventtypes/* | Listar eventos do Log de atividades (eventos de gerenciamento) em um assinatura. Essa permissão é aplicável ao acesso programático e ao portal para o Log de atividades. |
> | Microsoft.Insights/LogDefinitions/* | Essa permissão é necessária para usuários que precisam de acesso aos Logs de atividade por meio do portal. Liste as categorias de log no Log de Atividades. |
> | Microsoft.Insights/metricalerts/* |  |
> | Microsoft.Insights/MetricDefinitions/* | Ler definições de métricas (lista de tipos de métrica disponíveis para um recurso). |
> | Microsoft.Insights/Metrics/* | Ler as métricas para um recurso. |
> | Microsoft.Insights/Register/Action | Registrar o provedor do Microsoft Insights |
> | Microsoft.Insights/scheduledqueryrules/* |  |
> | Microsoft.Insights/webtests/* | Criar e gerenciar testes na Web do insights |
> | Microsoft. insights/pastas de trabalho/* |  |
> | Microsoft. insights/privateLinkScopes/* |  |
> | Microsoft. insights/privateLinkScopeOperationStatuses/* |  |
> | Microsoft.OperationalInsights/workspaces/write | Criar um novo workspace ou links para um workspace existente fornecendo a ID do cliente do workspace existente. |
> | Microsoft.OperationalInsights/workspaces/intelligencepacks/* | Ler/gravar/excluir pacotes de solução do log Analytics. |
> | Microsoft.OperationalInsights/workspaces/savedSearches/* | Ler/gravar/excluir pesquisas salvas do log Analytics. |
> | Microsoft.OperationalInsights/workspaces/search/action | Executar uma consulta de pesquisa |
> | Microsoft.OperationalInsights/workspaces/sharedKeys/action | Recupera as chaves compartilhadas do workspace. Essas chaves são usadas para conectar agentes do Insights Operacionais da Microsoft ao workspace. |
> | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/* | Ler/gravar/excluir configurações de insights de armazenamento do log Analytics. |
> | Microsoft.Support/* | Criar e atualizar um tíquete de suporte |
> | Microsoft.WorkloadMonitor/monitors/* |  |
> | Microsoft.WorkloadMonitor/notificationSettings/* |  |
> | Microsoft. AlertsManagement/smartDetectorAlertRules/* |  |
> | Microsoft. AlertsManagement/actionRules/* |  |
> | Microsoft. AlertsManagement/smartGroups/* |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read all monitoring data and update monitoring settings.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/749f88d5-cbae-40b8-bcfc-e573ddc772fa",
  "name": "749f88d5-cbae-40b8-bcfc-e573ddc772fa",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.AlertsManagement/alerts/*",
        "Microsoft.AlertsManagement/alertsSummary/*",
        "Microsoft.Insights/actiongroups/*",
        "Microsoft.Insights/activityLogAlerts/*",
        "Microsoft.Insights/AlertRules/*",
        "Microsoft.Insights/components/*",
        "Microsoft.Insights/DiagnosticSettings/*",
        "Microsoft.Insights/eventtypes/*",
        "Microsoft.Insights/LogDefinitions/*",
        "Microsoft.Insights/metricalerts/*",
        "Microsoft.Insights/MetricDefinitions/*",
        "Microsoft.Insights/Metrics/*",
        "Microsoft.Insights/Register/Action",
        "Microsoft.Insights/scheduledqueryrules/*",
        "Microsoft.Insights/webtests/*",
        "Microsoft.Insights/workbooks/*",
        "Microsoft.Insights/privateLinkScopes/*",
        "Microsoft.Insights/privateLinkScopeOperationStatuses/*",
        "Microsoft.OperationalInsights/workspaces/write",
        "Microsoft.OperationalInsights/workspaces/intelligencepacks/*",
        "Microsoft.OperationalInsights/workspaces/savedSearches/*",
        "Microsoft.OperationalInsights/workspaces/search/action",
        "Microsoft.OperationalInsights/workspaces/sharedKeys/action",
        "Microsoft.OperationalInsights/workspaces/storageinsightconfigs/*",
        "Microsoft.Support/*",
        "Microsoft.WorkloadMonitor/monitors/*",
        "Microsoft.WorkloadMonitor/notificationSettings/*",
        "Microsoft.AlertsManagement/smartDetectorAlertRules/*",
        "Microsoft.AlertsManagement/actionRules/*",
        "Microsoft.AlertsManagement/smartGroups/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Monitoring Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="monitoring-metrics-publisher"></a>Publicador de Métricas de Monitoramento

Habilita a publicação de métricas com base nos recursos do Azure

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Insights/Register/Action | Registrar o provedor do Microsoft Insights |
> | Microsoft.Support/* | Criar e atualizar um tíquete de suporte |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | Microsoft.Insights/Metrics/Write | Gravar métricas |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Enables publishing metrics against Azure resources",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3913510d-42f4-4e42-8a64-420c390055eb",
  "name": "3913510d-42f4-4e42-8a64-420c390055eb",
  "permissions": [
    {
      "actions": [
        "Microsoft.Insights/Register/Action",
        "Microsoft.Support/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Insights/Metrics/Write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Monitoring Metrics Publisher",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="monitoring-reader"></a>Leitor de monitoramento

Pode ler todos os dados de monitoramento (métricas, logs, etc). Consulte também [Introdução às funções, permissões e segurança com o Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/roles-permissions-security#built-in-monitoring-roles).

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | */leitura | Ler recursos de todos os tipos, exceto segredos. |
> | Microsoft.OperationalInsights/workspaces/search/action | Executar uma consulta de pesquisa |
> | Microsoft.Support/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read all monitoring data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/43d0d8ad-25c7-4714-9337-8ba259a9fe05",
  "name": "43d0d8ad-25c7-4714-9337-8ba259a9fe05",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.OperationalInsights/workspaces/search/action",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Monitoring Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="workbook-contributor"></a>Colaborador da pasta de trabalho

Pode salvar pastas de trabalho compartilhadas.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft. insights/pastas de trabalho/gravação | Criar ou atualizar uma pasta de trabalho |
> | Microsoft. insights/pastas de trabalho/exclusão | Excluir uma pasta de trabalho |
> | Microsoft. insights/pastas de trabalho/leitura | Ler uma pasta de trabalho |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can save shared workbooks.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e8ddcd69-c73f-4f9f-9844-4100522f16ad",
  "name": "e8ddcd69-c73f-4f9f-9844-4100522f16ad",
  "permissions": [
    {
      "actions": [
        "Microsoft.Insights/workbooks/write",
        "Microsoft.Insights/workbooks/delete",
        "Microsoft.Insights/workbooks/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Workbook Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="workbook-reader"></a>Leitor de pasta de trabalho

Pode ler pastas de trabalho.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft. insights/pastas de trabalho/leitura | Ler uma pasta de trabalho |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read workbooks.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b279062a-9be3-42a0-92ae-8b3cf002ec4d",
  "name": "b279062a-9be3-42a0-92ae-8b3cf002ec4d",
  "permissions": [
    {
      "actions": [
        "microsoft.insights/workbooks/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Workbook Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="management--governance"></a>Gerenciamento + governança


### <a name="automation-job-operator"></a>Operador do Trabalho de Automação

Criar e gerenciar trabalhos usando runbooks de Automação.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | Ler recursos do Hybrid Runbook Worker |
> | Microsoft.Automation/automationAccounts/jobs/read | Obter um trabalho da Automação do Azure |
> | Microsoft.Automation/automationAccounts/jobs/resume/action | Reinicia um trabalho da Automação do Azure |
> | Microsoft.Automation/automationAccounts/jobs/stop/action | Interrompe um trabalho da Automação do Azure |
> | Microsoft.Automation/automationAccounts/jobs/streams/read | Obter um fluxo de trabalho da Automação do Azure |
> | Microsoft.Automation/automationAccounts/jobs/suspend/action | Suspende um trabalho da Automação do Azure |
> | Microsoft.Automation/automationAccounts/jobs/write | Criar um trabalho da Automação do Azure |
> | Microsoft.Automation/automationAccounts/jobs/output/read | Obter a saída de um trabalho |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | Microsoft.Resources/deployments/* | Criar e gerenciar uma implantação |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Support/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Create and Manage Jobs using Automation Runbooks.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4fe576fe-1146-4730-92eb-48519fa6bf9f",
  "name": "4fe576fe-1146-4730-92eb-48519fa6bf9f",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read",
        "Microsoft.Automation/automationAccounts/jobs/read",
        "Microsoft.Automation/automationAccounts/jobs/resume/action",
        "Microsoft.Automation/automationAccounts/jobs/stop/action",
        "Microsoft.Automation/automationAccounts/jobs/streams/read",
        "Microsoft.Automation/automationAccounts/jobs/suspend/action",
        "Microsoft.Automation/automationAccounts/jobs/write",
        "Microsoft.Automation/automationAccounts/jobs/output/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Automation Job Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="automation-operator"></a>Operador de automação

Os Operadores de Automação podem iniciar, interromper, suspender e retomar trabalhos

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | Ler recursos do Hybrid Runbook Worker |
> | Microsoft.Automation/automationAccounts/jobs/read | Obter um trabalho da Automação do Azure |
> | Microsoft.Automation/automationAccounts/jobs/resume/action | Reinicia um trabalho da Automação do Azure |
> | Microsoft.Automation/automationAccounts/jobs/stop/action | Interrompe um trabalho da Automação do Azure |
> | Microsoft.Automation/automationAccounts/jobs/streams/read | Obter um fluxo de trabalho da Automação do Azure |
> | Microsoft.Automation/automationAccounts/jobs/suspend/action | Suspende um trabalho da Automação do Azure |
> | Microsoft.Automation/automationAccounts/jobs/write | Criar um trabalho da Automação do Azure |
> | Microsoft.Automation/automationAccounts/jobSchedules/read | Obter uma agenda de trabalho da Automação do Azure |
> | Microsoft.Automation/automationAccounts/jobSchedules/write | Criar uma agenda de trabalho da Automação do Azure |
> | Microsoft.Automation/automationAccounts/linkedWorkspace/read | Obtém o workspace vinculado à conta de automação |
> | Microsoft.Automation/automationAccounts/read | Obter uma conta da Automação do Azure |
> | Microsoft.Automation/automationAccounts/runbooks/read | Obter um runbook da Automação do Azure |
> | Microsoft.Automation/automationAccounts/schedules/read | Obter um ativo de agendamento da Automação do Azure |
> | Microsoft.Automation/automationAccounts/schedules/write | Criar ou atualizar um ativo de agendamento da Automação do Azure |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
> | Microsoft.Resources/deployments/* | Criar e gerenciar uma implantação |
> | Microsoft.Automation/automationAccounts/jobs/output/read | Obter a saída de um trabalho |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Support/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Automation Operators are able to start, stop, suspend, and resume jobs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/d3881f73-407a-4167-8283-e981cbba0404",
  "name": "d3881f73-407a-4167-8283-e981cbba0404",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read",
        "Microsoft.Automation/automationAccounts/jobs/read",
        "Microsoft.Automation/automationAccounts/jobs/resume/action",
        "Microsoft.Automation/automationAccounts/jobs/stop/action",
        "Microsoft.Automation/automationAccounts/jobs/streams/read",
        "Microsoft.Automation/automationAccounts/jobs/suspend/action",
        "Microsoft.Automation/automationAccounts/jobs/write",
        "Microsoft.Automation/automationAccounts/jobSchedules/read",
        "Microsoft.Automation/automationAccounts/jobSchedules/write",
        "Microsoft.Automation/automationAccounts/linkedWorkspace/read",
        "Microsoft.Automation/automationAccounts/read",
        "Microsoft.Automation/automationAccounts/runbooks/read",
        "Microsoft.Automation/automationAccounts/schedules/read",
        "Microsoft.Automation/automationAccounts/schedules/write",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Automation/automationAccounts/jobs/output/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Automation Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="automation-runbook-operator"></a>Operador de Runbook de Automação

Ler propriedades do Runbook - para poder criar Trabalhos do runbook.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Automation/automationAccounts/runbooks/read | Obter um runbook da Automação do Azure |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | Microsoft.Resources/deployments/* | Criar e gerenciar uma implantação |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Support/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read Runbook properties - to be able to create Jobs of the runbook.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5fb5aef8-1081-4b8e-bb16-9d5d0385bab5",
  "name": "5fb5aef8-1081-4b8e-bb16-9d5d0385bab5",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Automation/automationAccounts/runbooks/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Automation Runbook Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-connected-machine-onboarding"></a>Integração do computador conectado ao Azure

Pode carregar computadores conectados do Azure.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft. HybridCompute/computadores/ler | Ler qualquer computador do Arc do Azure |
> | Microsoft. HybridCompute/Machines/Write | Grava um computador do Arc do Azure |
> | Microsoft.GuestConfiguration/guestConfigurationAssignments/read | Obter atribuição de configuração de convidado. |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can onboard Azure Connected Machines.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b64e21ea-ac4e-4cdf-9dc9-5b892992bee7",
  "name": "b64e21ea-ac4e-4cdf-9dc9-5b892992bee7",
  "permissions": [
    {
      "actions": [
        "Microsoft.HybridCompute/machines/read",
        "Microsoft.HybridCompute/machines/write",
        "Microsoft.GuestConfiguration/guestConfigurationAssignments/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Connected Machine Onboarding",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-connected-machine-resource-administrator"></a>Administrador de recursos do computador conectado do Azure

Pode ler, gravar, excluir e re-integrar computadores conectados do Azure.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft. HybridCompute/computadores/ler | Ler qualquer computador do Arc do Azure |
> | Microsoft. HybridCompute/Machines/Write | Grava um computador do Arc do Azure |
> | Microsoft. HybridCompute/Machines/Delete | Excluir um computador do Arc do Azure |
> | Microsoft. HybridCompute/Machines/reconnect/ação | Reconecta um computador de arco do Azure |
> | Microsoft. HybridCompute/Machines/Extensions/Write | Instala ou atualiza uma extensão de Arc do Azure |
> | Microsoft. HybridCompute/*/Read |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read, write, delete and re-onboard Azure Connected Machines.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/cd570a14-e51a-42ad-bac8-bafd67325302",
  "name": "cd570a14-e51a-42ad-bac8-bafd67325302",
  "permissions": [
    {
      "actions": [
        "Microsoft.HybridCompute/machines/read",
        "Microsoft.HybridCompute/machines/write",
        "Microsoft.HybridCompute/machines/delete",
        "Microsoft.HybridCompute/machines/reconnect/action",
        "Microsoft.HybridCompute/machines/extensions/write",
        "Microsoft.HybridCompute/*/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Connected Machine Resource Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="billing-reader"></a>Leitor de cobrança

Permite o acesso de leitura aos dados de cobrança

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Billing/*/read | Ler Informações de cobrança |
> | Microsoft.Commerce/*/read |  |
> | Microsoft.Consumption/*/read |  |
> | Microsoft.Management/managementGroups/read | Listar grupos de gerenciamento para o usuário autenticado. |
> | Microsoft.CostManagement/*/read |  |
> | Microsoft.Support/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows read access to billing data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64",
  "name": "fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Billing/*/read",
        "Microsoft.Commerce/*/read",
        "Microsoft.Consumption/*/read",
        "Microsoft.Management/managementGroups/read",
        "Microsoft.CostManagement/*/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Billing Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="blueprint-contributor"></a>Colaborador do Blueprint

Pode gerenciar definições de blueprint, mas não as atribuir.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft. Blueprint/plantas/* | Crie e gerencie definições de plantas ou artefatos de Blueprint. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Resources/deployments/* | Criar e gerenciar uma implantação |
> | Microsoft.Support/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage blueprint definitions, but not assign them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/41077137-e803-4205-871c-5a86e6a753b4",
  "name": "41077137-e803-4205-871c-5a86e6a753b4",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Blueprint/blueprints/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Blueprint Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="blueprint-operator"></a>Operador do Blueprint

Pode atribuir plantas publicadas existentes, mas não pode criar novas plantas. Observe que isso só funcionará se a atribuição for feita com uma identidade gerenciada atribuída pelo usuário.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft. Blueprint/blueprintAssignments/* | Criar e gerenciar atribuições de plano gráfico. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Resources/deployments/* | Criar e gerenciar uma implantação |
> | Microsoft.Support/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can assign existing published blueprints, but cannot create new blueprints. NOTE: this only works if the assignment is done with a user-assigned managed identity.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/437d2ced-4a38-4302-8479-ed2bcb43d090",
  "name": "437d2ced-4a38-4302-8479-ed2bcb43d090",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Blueprint/blueprintAssignments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Blueprint Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cost-management-contributor"></a>Colaborador do Gerenciamento de Custos

Pode exibir os custos e gerenciar a configuração de custo (por exemplo, orçamentos, exportações)

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Consumption/* |  |
> | Microsoft.CostManagement/* |  |
> | Microsoft.Billing/billingPeriods/read |  |
> | Microsoft.Resources/subscriptions/read | Obter a lista de assinaturas. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Support/* | Criar e atualizar um tíquete de suporte |
> | Microsoft.Advisor/configurations/read | Obter configurações |
> | Microsoft.Advisor/recommendations/read | Ler recomendações |
> | Microsoft.Management/managementGroups/read | Listar grupos de gerenciamento para o usuário autenticado. |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view costs and manage cost configuration (e.g. budgets, exports)",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/434105ed-43f6-45c7-a02f-909b2ba83430",
  "name": "434105ed-43f6-45c7-a02f-909b2ba83430",
  "permissions": [
    {
      "actions": [
        "Microsoft.Consumption/*",
        "Microsoft.CostManagement/*",
        "Microsoft.Billing/billingPeriods/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Advisor/configurations/read",
        "Microsoft.Advisor/recommendations/read",
        "Microsoft.Management/managementGroups/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cost Management Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cost-management-reader"></a>Leitor do Gerenciamento de Custos

Pode exibir dados e configuração de custos (por exemplo, orçamentos, exportações)

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Consumption/*/read |  |
> | Microsoft.CostManagement/*/read |  |
> | Microsoft.Billing/billingPeriods/read |  |
> | Microsoft.Resources/subscriptions/read | Obter a lista de assinaturas. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Support/* | Criar e atualizar um tíquete de suporte |
> | Microsoft.Advisor/configurations/read | Obter configurações |
> | Microsoft.Advisor/recommendations/read | Ler recomendações |
> | Microsoft.Management/managementGroups/read | Listar grupos de gerenciamento para o usuário autenticado. |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view cost data and configuration (e.g. budgets, exports)",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/72fafb9e-0641-4937-9268-a91bfd8191a3",
  "name": "72fafb9e-0641-4937-9268-a91bfd8191a3",
  "permissions": [
    {
      "actions": [
        "Microsoft.Consumption/*/read",
        "Microsoft.CostManagement/*/read",
        "Microsoft.Billing/billingPeriods/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Advisor/configurations/read",
        "Microsoft.Advisor/recommendations/read",
        "Microsoft.Management/managementGroups/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cost Management Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-application-contributor-role"></a>Função colaborador de aplicativo gerenciado

Permite a criação de recursos de aplicativos gerenciados.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | */leitura | Ler recursos de todos os tipos, exceto segredos. |
> | Microsoft. Solutions/Applications/* |  |
> | Microsoft.Solutions/register/action | Registrar para Soluções. |
> | Microsoft. Resources/subscriptions/resourceGroups/* |  |
> | Microsoft.Resources/deployments/* | Criar e gerenciar uma implantação |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for creating managed application resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/641177b8-a67a-45b9-a033-47bc880bb21e",
  "name": "641177b8-a67a-45b9-a033-47bc880bb21e",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Solutions/applications/*",
        "Microsoft.Solutions/register/action",
        "Microsoft.Resources/subscriptions/resourceGroups/*",
        "Microsoft.Resources/deployments/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Application Contributor Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-application-operator-role"></a>Função do Operador de Aplicativos Gerenciado

Permite que você leia e execute as ações nos recursos de aplicativo gerenciado

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | */leitura | Ler recursos de todos os tipos, exceto segredos. |
> | Microsoft.Solutions/applications/read | Recuperar uma lista de aplicativos. |
> | Microsoft. Solutions/*/Action |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read and perform actions on Managed Application resources",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c7393b34-138c-406f-901b-d8cf2b17e6ae",
  "name": "c7393b34-138c-406f-901b-d8cf2b17e6ae",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Solutions/applications/read",
        "Microsoft.Solutions/*/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Application Operator Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-applications-reader"></a>Leitor de aplicativos gerenciados

Permite ler os recursos de um aplicativo gerenciado e solicitar acesso JIT.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | */leitura | Ler recursos de todos os tipos, exceto segredos. |
> | Microsoft.Resources/deployments/* | Criar e gerenciar uma implantação |
> | Microsoft.Solutions/jitRequests/* |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read resources in a managed app and request JIT access.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b9331d33-8a36-4f8c-b097-4f54124fdb44",
  "name": "b9331d33-8a36-4f8c-b097-4f54124fdb44",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Solutions/jitRequests/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Applications Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-services-registration-assignment-delete-role"></a>Função de exclusão de atribuição de registro de serviços gerenciados

Atribuição de registro de serviços gerenciados excluir função permite que o gerenciamento de usuários de locatários exclua a atribuição de registro atribuída ao seu locatário.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft. Managedservices/registrationAssignments/Read | Recupera uma lista de atribuições de registro de serviços gerenciados. |
> | Microsoft. Managedservices/registrationAssignments/Delete | Remove a atribuição de registro de serviços gerenciados. |
> | Microsoft. Managedservices/operationStatuses/Read | Ler o status da operação do recurso. |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Managed Services Registration Assignment Delete Role allows the managing tenant users to delete the registration assignment assigned to their tenant.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/91c1777a-f3dc-4fae-b103-61d183457e46",
  "name": "91c1777a-f3dc-4fae-b103-61d183457e46",
  "permissions": [
    {
      "actions": [
        "Microsoft.ManagedServices/registrationAssignments/read",
        "Microsoft.ManagedServices/registrationAssignments/delete",
        "Microsoft.ManagedServices/operationStatuses/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Services Registration assignment Delete Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="management-group-contributor"></a>Colaborador do Grupo de Gerenciamento

Função de Colaborador do Grupo de Gerenciamento

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Management/managementGroups/delete | Excluir um grupo de gerenciamento. |
> | Microsoft.Management/managementGroups/read | Listar grupos de gerenciamento para o usuário autenticado. |
> | Microsoft.Management/managementGroups/subscriptions/delete | Desassociar a assinatura do grupo de gerenciamento. |
> | Microsoft.Management/managementGroups/subscriptions/write | Associar a assinatura existente ao grupo de gerenciamento. |
> | Microsoft.Management/managementGroups/write | Criar ou atualizar um grupo de gerenciamento. |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Management Group Contributor Role",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5d58bcaf-24a5-4b20-bdb6-eed9f69fbe4c",
  "name": "5d58bcaf-24a5-4b20-bdb6-eed9f69fbe4c",
  "permissions": [
    {
      "actions": [
        "Microsoft.Management/managementGroups/delete",
        "Microsoft.Management/managementGroups/read",
        "Microsoft.Management/managementGroups/subscriptions/delete",
        "Microsoft.Management/managementGroups/subscriptions/write",
        "Microsoft.Management/managementGroups/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Management Group Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="management-group-reader"></a>Leitor do Grupo de Gerenciamento

Função de Leitor do Grupo de Gerenciamento

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Management/managementGroups/read | Listar grupos de gerenciamento para o usuário autenticado. |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Management Group Reader Role",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ac63b705-f282-497d-ac71-919bf39d939d",
  "name": "ac63b705-f282-497d-ac71-919bf39d939d",
  "permissions": [
    {
      "actions": [
        "Microsoft.Management/managementGroups/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Management Group Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="new-relic-apm-account-contributor"></a>Colaborador de Conta APM do New Relic

Permite que você gerencie contas e aplicativos do Gerenciamento de desempenho de aplicativos da New Relic, mas não tem acesso a eles.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
> | Microsoft.Resources/deployments/* | Criar e gerenciar uma implantação |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Support/* | Criar e atualizar um tíquete de suporte |
> | NewRelic.APM/accounts/* |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage New Relic Application Performance Management accounts and applications, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5d28c62d-5b37-4476-8438-e587778df237",
  "name": "5d28c62d-5b37-4476-8438-e587778df237",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "NewRelic.APM/accounts/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "New Relic APM Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="policy-insights-data-writer-preview"></a>Gravador de dados de informações de política (versão prévia)

Permite acesso de leitura a políticas de recursos e acesso de gravação aos eventos de política de componente de recurso.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft. Authorization/policyassignments/Read | Obter informações sobre uma atribuição de política. |
> | Microsoft. Authorization/policyDefinitions/Read | Obter informações sobre uma definição de política. |
> | Microsoft. Authorization/policysetdefinitions/Read | Obter informações sobre uma definição de conjunto de políticas. |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | Microsoft. PolicyInsights/checkDataPolicyCompliance/Action | Verifique o status de conformidade de um determinado componente em relação a políticas de dados. |
> | Microsoft. PolicyInsights/policyEvents/logDataEvents/Action | Registre os eventos de política do componente de recurso. |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows read access to resource policies and write access to resource component policy events.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/66bb4e9e-b016-4a94-8249-4c0511c2be84",
  "name": "66bb4e9e-b016-4a94-8249-4c0511c2be84",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/policyassignments/read",
        "Microsoft.Authorization/policydefinitions/read",
        "Microsoft.Authorization/policysetdefinitions/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.PolicyInsights/checkDataPolicyCompliance/action",
        "Microsoft.PolicyInsights/policyEvents/logDataEvents/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Policy Insights Data Writer (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="resource-policy-contributor"></a>Colaborador da política de recurso

Usuários com direitos para criar/modificar a política de recursos, criar tíquete de suporte e ler recursos/hierarquia.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | */leitura | Ler recursos de todos os tipos, exceto segredos. |
> | Microsoft.Authorization/policyAssignments/* | Criar e gerenciar atribuições de política |
> | Microsoft.Authorization/policyDefinitions/* | Criar e gerenciar definições de política |
> | Microsoft.Authorization/policysetdefinitions/* | Criar e gerenciar conjuntos de política |
> | Microsoft.PolicyInsights/* |  |
> | Microsoft.Support/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Users with rights to create/modify resource policy, create support ticket and read resources/hierarchy.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/36243c78-bf99-498c-9df9-86d9f8d28608",
  "name": "36243c78-bf99-498c-9df9-86d9f8d28608",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Authorization/policyassignments/*",
        "Microsoft.Authorization/policydefinitions/*",
        "Microsoft.Authorization/policysetdefinitions/*",
        "Microsoft.PolicyInsights/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Resource Policy Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="site-recovery-contributor"></a>Colaborador do Site Recovery

Permite gerenciar o serviço do Azure Site Recovery, exceto a criação de cofre e atribuição de função

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | Microsoft.Network/virtualNetworks/read | Obter a definição de rede virtual |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp é uma operação interna usada pelo serviço |
> | Microsoft.RecoveryServices/locations/allocateStamp/action | AllocateStamp é uma operação interna usada pelo serviço |
> | Microsoft.RecoveryServices/Vaults/certificates/write | A operação Atualizar certificado do recurso atualiza o certificado de credencial de cofre/recurso. |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/* | Criar e gerenciar informações estendidas relacionadas ao cofre |
> | Microsoft.RecoveryServices/Vaults/read | A operação Obter cofre obtém um objeto que representa o recurso do Azure do tipo 'cofre' |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/* | Criar e gerenciar identidades registradas |
> | Microsoft.RecoveryServices/vaults/replicationAlertSettings/* | Criar ou atualizar as configurações de alerta de replicação |
> | Microsoft.RecoveryServices/vaults/replicationEvents/read | Ler quaisquer eventos |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/* | Criar e gerenciar malhas de replicação |
> | Microsoft.RecoveryServices/vaults/replicationJobs/* | Criar e gerenciar trabalhos de replicação |
> | Microsoft.RecoveryServices/vaults/replicationPolicies/* | Criar e gerenciar políticas de replicação |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/* | Criar e gerenciar planos de recuperação |
> | Microsoft.RecoveryServices/Vaults/storageConfig/* | Criar e gerenciar a configuração de armazenamento do cofre de Serviços de Recuperação |
> | Microsoft.RecoveryServices/Vaults/tokenInfo/read |  |
> | Microsoft.RecoveryServices/Vaults/usages/read | Retornar os detalhes de uso para um cofre dos Serviços de Recuperação. |
> | Microsoft.RecoveryServices/Vaults/vaultTokens/read | A operação de token do cofre pode ser usada a fim de obter o token do cofre para operações de back-end no nível do cofre. |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/* | Obter os alertas para o cofre dos Serviços de Recuperação |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
> | Microsoft.Resources/deployments/* | Criar e gerenciar uma implantação |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Storage/storageAccounts/read | Retornar a lista de contas de armazenamento ou obter as propriedades da conta de armazenamento especificada. |
> | Microsoft. Recoveryservices/Vaults/replicationOperationStatus/Read | Ler o status da operação de replicação do cofre |
> | Microsoft.Support/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Site Recovery service except vault creation and role assignment",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/6670b86e-a3f7-4917-ac9b-5d6ab1be4567",
  "name": "6670b86e-a3f7-4917-ac9b-5d6ab1be4567",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.RecoveryServices/locations/allocatedStamp/read",
        "Microsoft.RecoveryServices/locations/allocateStamp/action",
        "Microsoft.RecoveryServices/Vaults/certificates/write",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/*",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/refreshContainers/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/*",
        "Microsoft.RecoveryServices/vaults/replicationAlertSettings/*",
        "Microsoft.RecoveryServices/vaults/replicationEvents/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/*",
        "Microsoft.RecoveryServices/vaults/replicationJobs/*",
        "Microsoft.RecoveryServices/vaults/replicationPolicies/*",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/*",
        "Microsoft.RecoveryServices/Vaults/storageConfig/*",
        "Microsoft.RecoveryServices/Vaults/tokenInfo/read",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/Vaults/vaultTokens/read",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/*",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.RecoveryServices/vaults/replicationOperationStatus/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Site Recovery Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="site-recovery-operator"></a>Operador do Site Recovery

Permite failover e failback, mas não executa outras operações de gerenciamento do Azure Site Recovery

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | Microsoft.Network/virtualNetworks/read | Obter a definição de rede virtual |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp é uma operação interna usada pelo serviço |
> | Microsoft.RecoveryServices/locations/allocateStamp/action | AllocateStamp é uma operação interna usada pelo serviço |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | A operação Obter Informações Estendidas obtém informações estendidas de um objeto que representa o recurso do Azure do tipo ?vault? |
> | Microsoft.RecoveryServices/Vaults/read | A operação Obter cofre obtém um objeto que representa o recurso do Azure do tipo 'cofre' |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | A operação Obter resultados da operação pode ser usada para obter o status da operação e o resultado da operação enviada de forma assíncrona |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | A operação Obter contêineres pode ser usada para obter os contêineres registrados para um recurso. |
> | Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | Ler configurações de alertas |
> | Microsoft.RecoveryServices/vaults/replicationEvents/read | Ler quaisquer eventos |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action | Verificar consistência da malha |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/read | Ler quaisquer malhas |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/reassociateGateway/action | Reassociar gateway |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action | Renovar Certificado para malha do Microsoft Azure |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | Ler quaisquer redes |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | Ler quaisquer mapeamentos de rede |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read | Ler quaisquer contêineres de proteção |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | Ler quaisquer itens que podem ser protegidos |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action | Aplicar ponto de recuperação |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/action | Confirmação de failover |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/action | Failover planejado |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | Ler quaisquer itens protegidos |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Ler quaisquer pontos de recuperação de replicação |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/action | Reparar replicação |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/action | Proteger item protegido novamente |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/switchprotection/action | Alterar contêiner de proteção |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/action | Failover de Teste |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action | Limpeza do Failover de teste |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action | Failover |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action | Atualizar serviço de mobilidade |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | Ler quaisquer mapeamentos de contêiner de proteção |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Ler qualquer provedores de Serviços de Recuperação do Microsoft Azure |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/action | Atualizar provedor |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read | Ler quaisquer classificações de armazenamento |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | Ler quaisquer mapeamentos de classificação de armazenamento |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | Ler quaisquer vCenters |
> | Microsoft.RecoveryServices/vaults/replicationJobs/* | Criar e gerenciar trabalhos de replicação |
> | Microsoft.RecoveryServices/vaults/replicationPolicies/read | Ler quaisquer políticas |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/failoverCommit/action | Plano de recuperação de confirmação de failover |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/plannedFailover/action | Plano de recuperação de failover planejado |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | Ler quaisquer planos de recuperação |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/reProtect/action | Proteja plano de recuperação novamente |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailover/action | Testar plano de recuperação de failover |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailoverCleanup/action | Testar plano de recuperação de limpeza do failover |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/unplannedFailover/action | Plano de recuperação de failover |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/* | Obter os alertas para o cofre dos Serviços de Recuperação |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | Microsoft.RecoveryServices/Vaults/storageConfig/read |  |
> | Microsoft.RecoveryServices/Vaults/tokenInfo/read |  |
> | Microsoft.RecoveryServices/Vaults/usages/read | Retornar os detalhes de uso para um cofre dos Serviços de Recuperação. |
> | Microsoft.RecoveryServices/Vaults/vaultTokens/read | A operação de token do cofre pode ser usada a fim de obter o token do cofre para operações de back-end no nível do cofre. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
> | Microsoft.Resources/deployments/* | Criar e gerenciar uma implantação |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Storage/storageAccounts/read | Retornar a lista de contas de armazenamento ou obter as propriedades da conta de armazenamento especificada. |
> | Microsoft.Support/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you failover and failback but not perform other Site Recovery management operations",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/494ae006-db33-4328-bf46-533a6560a3ca",
  "name": "494ae006-db33-4328-bf46-533a6560a3ca",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.RecoveryServices/locations/allocatedStamp/read",
        "Microsoft.RecoveryServices/locations/allocateStamp/action",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/read",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/refreshContainers/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/read",
        "Microsoft.RecoveryServices/vaults/replicationAlertSettings/read",
        "Microsoft.RecoveryServices/vaults/replicationEvents/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/reassociateGateway/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/switchprotection/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read",
        "Microsoft.RecoveryServices/vaults/replicationJobs/*",
        "Microsoft.RecoveryServices/vaults/replicationPolicies/read",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/failoverCommit/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/plannedFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/reProtect/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailoverCleanup/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/unplannedFailover/action",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/*",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read",
        "Microsoft.RecoveryServices/Vaults/storageConfig/read",
        "Microsoft.RecoveryServices/Vaults/tokenInfo/read",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/Vaults/vaultTokens/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Site Recovery Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="site-recovery-reader"></a>Leitor do Site Recovery

Permite visualizar o status do Azure Site Recovery, mas não executar outras operações de gerenciamento

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp é uma operação interna usada pelo serviço |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | A operação Obter Informações Estendidas obtém informações estendidas de um objeto que representa o recurso do Azure do tipo ?vault? |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Obter os alertas para o cofre dos Serviços de Recuperação. |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | Microsoft.RecoveryServices/Vaults/read | A operação Obter cofre obtém um objeto que representa o recurso do Azure do tipo 'cofre' |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | A operação Obter resultados da operação pode ser usada para obter o status da operação e o resultado da operação enviada de forma assíncrona |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | A operação Obter contêineres pode ser usada para obter os contêineres registrados para um recurso. |
> | Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | Ler configurações de alertas |
> | Microsoft.RecoveryServices/vaults/replicationEvents/read | Ler quaisquer eventos |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/read | Ler quaisquer malhas |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | Ler quaisquer redes |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | Ler quaisquer mapeamentos de rede |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read | Ler quaisquer contêineres de proteção |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | Ler quaisquer itens que podem ser protegidos |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | Ler quaisquer itens protegidos |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Ler quaisquer pontos de recuperação de replicação |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | Ler quaisquer mapeamentos de contêiner de proteção |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Ler qualquer provedores de Serviços de Recuperação do Microsoft Azure |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read | Ler quaisquer classificações de armazenamento |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | Ler quaisquer mapeamentos de classificação de armazenamento |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | Ler quaisquer vCenters |
> | Microsoft.RecoveryServices/vaults/replicationJobs/read | Ler todos os trabalhos |
> | Microsoft.RecoveryServices/vaults/replicationPolicies/read | Ler quaisquer políticas |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | Ler quaisquer planos de recuperação |
> | Microsoft.RecoveryServices/Vaults/storageConfig/read |  |
> | Microsoft.RecoveryServices/Vaults/tokenInfo/read |  |
> | Microsoft.RecoveryServices/Vaults/usages/read | Retornar os detalhes de uso para um cofre dos Serviços de Recuperação. |
> | Microsoft.RecoveryServices/Vaults/vaultTokens/read | A operação de token do cofre pode ser usada a fim de obter o token do cofre para operações de back-end no nível do cofre. |
> | Microsoft.Support/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you view Site Recovery status but not perform other management operations",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/dbaa88c4-0c30-4179-9fb3-46319faa6149",
  "name": "dbaa88c4-0c30-4179-9fb3-46319faa6149",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.RecoveryServices/locations/allocatedStamp/read",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/read",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/read",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/refreshContainers/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/read",
        "Microsoft.RecoveryServices/vaults/replicationAlertSettings/read",
        "Microsoft.RecoveryServices/vaults/replicationEvents/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read",
        "Microsoft.RecoveryServices/vaults/replicationJobs/read",
        "Microsoft.RecoveryServices/vaults/replicationPolicies/read",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read",
        "Microsoft.RecoveryServices/Vaults/storageConfig/read",
        "Microsoft.RecoveryServices/Vaults/tokenInfo/read",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/Vaults/vaultTokens/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Site Recovery Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="support-request-contributor"></a>Colaborador de solicitação de suporte

Permite criar e gerenciar Solicitações de Suporte

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Support/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you create and manage Support requests",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/cfd33db0-3dd1-45e3-aa9d-cdbdf3b6f24e",
  "name": "cfd33db0-3dd1-45e3-aa9d-cdbdf3b6f24e",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Support Request Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="tag-contributor"></a>Colaborador de marca

Permite que você gerencie marcas em entidades, sem fornecer acesso às próprias entidades.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft. Resources/subscriptions/resourceGroups/Resources/Read | Obter os recursos do grupo de recursos. |
> | Microsoft.Resources/subscriptions/resources/read | Obter os recursos de uma assinatura. |
> | Microsoft.Resources/deployments/* | Criar e gerenciar uma implantação |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | Microsoft.Support/* | Criar e atualizar um tíquete de suporte |
> | Microsoft. Resources/Tags/* |  |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage tags on entities, without providing access to the entities themselves.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4a9ae827-6dc8-4573-8ac7-8239d42aa03f",
  "name": "4a9ae827-6dc8-4573-8ac7-8239d42aa03f",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/subscriptions/resourceGroups/resources/read",
        "Microsoft.Resources/subscriptions/resources/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Support/*",
        "Microsoft.Resources/tags/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Tag Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="other"></a>Outros


### <a name="biztalk-contributor"></a>Colaborador do BizTalk

Permite gerenciar serviços do BizTalk, mas não acessá-los.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.BizTalkServices/BizTalk/* | Criar e gerenciar serviços BizTalk |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
> | Microsoft.Resources/deployments/* | Criar e gerenciar uma implantação |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Support/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage BizTalk services, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5e3c6656-6cfa-4708-81fe-0de47ac73342",
  "name": "5e3c6656-6cfa-4708-81fe-0de47ac73342",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.BizTalkServices/BizTalk/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "BizTalk Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="scheduler-job-collections-contributor"></a>Colaborador de Coleções de Trabalho do Agendador

Permite gerenciar as coleções de trabalhos do Agendador, mas não acessá-las.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar um alerta de métrica clássico |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
> | Microsoft.Resources/deployments/* | Criar e gerenciar uma implantação |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Scheduler/jobcollections/* | Criar e gerenciar coleções de trabalhos |
> | Microsoft.Support/* | Criar e atualizar um tíquete de suporte |
> | **NotActions** |  |
> | *nenhum* |  |
> | **DataActions** |  |
> | *nenhum* |  |
> | **NotDataActions** |  |
> | *nenhum* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Scheduler job collections, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/188a0f2f-5c9e-469b-ae67-2aa5ce574b94",
  "name": "188a0f2f-5c9e-469b-ae67-2aa5ce574b94",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Scheduler/jobcollections/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Scheduler Job Collections Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="next-steps"></a>Próximas etapas

- [Corresponder provedor de recursos ao serviço](../azure-resource-manager/management/azure-services-resource-providers.md)
- [Funções personalizadas do Azure](custom-roles.md)
- [Permissões na Central de Segurança do Azure](../security-center/security-center-permissions.md)
