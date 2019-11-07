---
title: 'Tutorial: Aceitar e receber dados – Azure Data Share'
description: Tutorial - Aceitar e receber dados usando o Azure Data Share
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: tutorial
ms.date: 07/10/2019
ms.openlocfilehash: 9c24f54fe846459187488b0a65b2582914e25e2a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499344"
---
# <a name="tutorial-accept-and-receive-data-using-azure-data-share"></a>Tutorial: Aceitar e receber dados usando o Azure Data Share  

Neste tutorial, você aprenderá a aceitar um convite de compartilhamento de dados usando o Azure Data Share. Você aprenderá a receber dados que estão sendo compartilhados com você, bem como habilitar um intervalo de atualização regular para sempre ter o instantâneo mais recente dos dados que estão sendo compartilhados com você. 

> [!div class="checklist"]
> * Como aceitar um convite do Azure Data Share
> * Criar um Azure Data Share
> * Especificar um destino para seus dados
> * Criar uma assinatura para seu compartilhamento de dados para a atualização agendada

## <a name="prerequisites"></a>Pré-requisitos
Antes de aceitar um convite de compartilhamento de dados, é preciso provisionar vários recursos do Azure, que estão listados abaixo. 

Cumpra todos os pré-requisitos antes de aceitar um convite de compartilhamento de dados. 

* Assinatura do Azure: Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* Um convite do Azure Data Share: Um convite do Microsoft Azure com o assunto intitulado "Convite do Azure Data Share de **<yourdataprovider@domain.com>** ".

### <a name="receive-data-into-a-storage-account"></a>Receber dados para uma conta de armazenamento: 

* Uma conta de Armazenamento do Azure: Se você não tiver uma, poderá criar uma [conta de Armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account). 
* Permissão para adicionar uma atribuição de função à conta de armazenamento, que está presente na permissão *Microsoft.Authorization/atribuições de função/gravação*. Essa permissão existe na função de proprietário. 
* Registro do Provedor de Recursos para o Microsoft.DataShare. Consulte a documentação sobre [Provedores de Recursos do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services) para obter informações sobre como fazer isso. 

> [!IMPORTANT]
> Para aceitar e receber um Azure Data Share, primeiro registre o provedor de recursos Microsoft.DataShare. Você também deve ser um proprietário da conta de armazenamento na qual você aceita dados. Siga as instruções documentadas em [Solucionar problemas do Azure Data Share](data-share-troubleshoot.md) para registrar o provedor de recursos de compartilhamento de dados, bem como adicionar a si próprio como proprietário da conta de armazenamento. 

### <a name="receive-data-into-a-sql-based-source"></a>Receber dados em uma fonte baseada em SQL:

* Permissão para o MSI de compartilhamento de dados acessar o banco de dados SQL do Azure ou SQL Data Warehouse do Azure. Isso pode ser feito executando as seguintes etapas: 
    1. Defina-se como o Administrador do Azure Active Directory para o servidor.
    1. Conecte-se ao Banco de Dados SQL do Azure/Data Warehouse usando o Azure Active Directory.
    1. Use o Editor de Consultas (versão prévia) para executar o script a seguir para adicionar o MSI do Data Share como um db_owner. Você deve se conectar usando o Active Directory e não a autenticação do SQL Server. 

```sql
    create user <share_acct_name> from external provider;     
    exec sp_addrolemember db_owner, <share_acct_name>; 
```      
Observe que *<share_acc_name>* é o nome da sua Conta do Data Share. Se você ainda não criou uma conta do Data Share, poderá voltar para esse pré-requisito mais tarde.         

* Acesso ao Firewall do SQL Server de IP do cliente: Isso pode ser feito executando as seguintes etapas: 1. Navegue para *Firewalls e Redes Virtuais* 1. Clique na alternância **ligar** para permitir o acesso aos Serviços do Azure. 

Depois que esses pré-requisitos forem concluídos, você estará pronto para receber dados em seu SQL Server.

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Entre no [Portal do Azure](https://portal.azure.com/).

## <a name="open-invitation"></a>Convite aberto

Confira sua caixa de entrada para um convite de seu provedor de dados. O convite é do Microsoft Azure, intitulado **Convite do Azure Data Share de <yourdataprovider@domain.com>** . Anote o nome do compartilhamento para garantir que você esteja aceitando o compartilhamento correto, caso haja vários convites. 

Selecione **Exibir convite** para ver seu convite no Azure. Isso o levará à sua exibição de Compartilhamentos Recebidos.

![Convites](./media/invitations.png "Lista de convites") 

Selecione o compartilhamento que você deseja exibir. 

## <a name="accept-invitation"></a>Aceitar o convite
Examine todos os campos, incluindo os **Termos de uso**. Se você concordar com os termos de uso, será solicitado a marcar a caixa para indicar sua concordância. 

![Termos de uso](./media/terms-of-use.png "Termos de uso") 

Em *Conta do Data Share de Destino*, selecione a assinatura e o Grupo de Recursos em que você vai implantar seu Data Share. 

Para o campo **Conta do Data Share**, selecione **Criar nova** se você não tiver uma conta do Data Share. Caso contrário, selecione uma conta do Data Share em que você gostaria de aceitar o compartilhamento de dados. 

Para o campo *Nome do Compartilhamento Recebido*, deixe o padrão especificado pelo Provedor de Dados ou especifique um novo nome para o compartilhamento recebido. 

![Conta de compartilhamento de dados de destino](./media/target-data-share.png "Conta de compartilhamento de dados de destino") 

Depois de aceitar os termos de uso e especificar uma localização para seu compartilhamento, selecione *Aceitar e Configurar*. Se você escolher essa opção, será criada uma assinatura de compartilhamento e a próxima tela solicitará que você selecione uma conta de armazenamento de destino para a qual copiar seus dados. 

![Aceitar opções](./media/accept-options.png "Aceitar opções") 

Se você preferir aceitar o convite agora, mas configurar o armazenamento depois, selecione *Aceitar e Configurar mais tarde*. Essa opção permite configurar sua conta de armazenamento de destino mais tarde. Para continuar configurando o armazenamento mais tarde, confira a página [Como configurar sua conta de armazenamento](how-to-configure-mapping.md) para as etapas detalhadas de como retomar a configuração de seus dados de compartilhamento. 

Se você não quiser aceitar o convite, selecione *Rejeitar*. 

## <a name="configure-storage"></a>Configurar o armazenamento
Em *Configurações de Armazenamento de Destino*, selecione a Assinatura, o Grupo de recursos e a conta de armazenamento em que você gostaria de receber seus dados. 

![Configurações do armazenamento de destino](./media/target-storage-settings.png "Armazenamento de destino") 

Para receber atualizações regulares de seus dados, habilite as configurações de instantâneo. Observe que você só verá um agendamento de configuração de instantâneo se o provedor de dados estiver incluído no compartilhamento de dados. 

![Configurações de instantâneo](./media/snapshot-settings.png "Configurações de instantâneo") 

Clique em *Salvar*. 

> [!IMPORTANT]
> Se você estiver recebendo dados baseados em SQL e quiser receber esses dados em uma fonte baseada em SQL, visite nosso guia de instruções [Configurar um mapeamento de conjunto de dados](how-to-configure-mapping.md) para saber como configurar um SQL Server como o destino do conjunto de dados. 

## <a name="trigger-a-snapshot"></a>Disparar um instantâneo

Você pode disparar um instantâneo na guia Compartilhamentos Recebidos -> Detalhes selecionando **Disparar Instantâneo**. Aqui, você pode disparar um instantâneo completo ou incremental de seus dados. Se esta for a primeira vez que você está recebendo dados de seu provedor de dados, selecione a cópia completa. 

![Disparar instantâneo](./media/trigger-snapshot.png "Disparar instantâneo") 

Quando o status da última execução for *bem-sucedido*, abra a conta de armazenamento para exibir os dados recebidos. 

Para verificar qual conta de armazenamento você usou, selecione **Conjuntos de Dados**. 

![Conjuntos de dados do consumidor](./media/consumer-datasets.png "Mapeamento de conjunto de dados do consumidor") 

## <a name="view-history"></a>Exibir histórico
Para exibir um histórico de seus instantâneos, navegue até Compartilhamentos Recebidos -> Histórico. Aqui, você encontrará um histórico de todos os instantâneos gerados nos últimos 60 dias. 

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você aprendeu a aceitar e receber um Azure Data Share. Para saber mais sobre conceitos do Azure Data Share, prossiga para [Conceitos: terminologia do Azure Data Share](terminology.md).