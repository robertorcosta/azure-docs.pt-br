---
title: Como verificar Data Explorer do Azure
description: Este guia de instruções descreve os detalhes de como verificar o Azure Data Explorer.
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 10/9/2020
ms.openlocfilehash: 01a1ded570d20d175b5e8eadb3e6cc8556155a85
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/03/2020
ms.locfileid: "96551596"
---
# <a name="register-and-scan-azure-data-explorer"></a>Registrar e verificar Data Explorer do Azure

Este artigo descreve como registrar uma conta de Data Explorer do Azure no Azure alcance e configurar uma verificação.

## <a name="supported-capabilities"></a>Funcionalidades com suporte

O Azure Data Explorer dá suporte a verificações completas e incrementais para capturar os metadados e o esquema. As verificações também classificam os dados automaticamente com base em regras de classificação personalizada e do sistema.

## <a name="prerequisites"></a>Pré-requisitos

- Antes de registrar as fontes de dados, crie uma conta do Azure alcance. Para obter mais informações sobre como criar uma conta do alcance, consulte [início rápido: criar uma conta do Azure alcance](create-catalog-portal.md).
- Você precisa ser um administrador de fonte de dados do Azure alcance

## <a name="setting-up-authentication-for-a-scan"></a>Configurando a autenticação para uma verificação

Há apenas uma maneira de configurar a autenticação para o Azure data Explorer:

- Entidade de Serviço

### <a name="service-principal"></a>Entidade de serviço

Para usar a autenticação de entidade de serviço para verificações, você pode usar uma existente ou criar uma nova. 

> [!Note]
> Se você precisar criar uma nova entidade de serviço, siga estas etapas:
> 1. Navegue até o [Portal do Azure](https://portal.azure.com).
> 1. Selecione **Azure Active Directory** no menu do lado esquerdo.
> 1. Selecione **Registros do Aplicativo**.
> 1. Selecione **+Novo registro de aplicativo**.
> 1. Insira um nome para o **aplicativo** (o nome da entidade de serviço).
> 1. Selecione **contas somente neste diretório organizacional**.
> 1. Para URI de redirecionamento, selecione **Web** e insira qualquer URL desejada; Ele não precisa ser real nem funcionar.
> 1. Em seguida, selecione **Registrar**.

É necessário obter a ID e o segredo do aplicativo da entidade de serviço:

1. Navegue até a entidade de serviço na [portal do Azure](https://portal.azure.com)
1. Copie os valores da **ID do aplicativo (cliente)** da **visão geral** e do **segredo do cliente** dos **certificados & segredos**.
1. Navegue até o Key Vault
1. Selecione **configurações > segredos**
1. Selecione **+ gerar/importar** e insira o **nome** de sua escolha e **valor** como o **segredo do cliente** de sua entidade de serviço
1. Selecione **criar** para concluir
1. Se o cofre de chaves ainda não estiver conectado ao alcance, você precisará [criar uma nova conexão de Key Vault](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)
1. Por fim, [crie uma nova credencial](manage-credentials.md#create-a-new-credential) usando a entidade de serviço para configurar sua verificação

#### <a name="granting-the-service-principal-access-to-your-azure-data-explorer-instance"></a>Concedendo acesso à entidade de serviço à instância do Gerenciador de dados do Azure

1. Navegue até o Portal do Azure. Em seguida, navegue até a instância do Gerenciador de dados do Azure.

1. Adicione a entidade de serviço à função **AllDatabasesViewer** na guia **permissões** , conforme mostrado na captura de tela a seguir.

    :::image type="content" source="./media/register-scan-azure-data-explorer/permissions-auth.png" alt-text="Captura de tela para adicionar a entidade de serviço nas permissões" border="true":::

## <a name="register-an-azure-data-explorer-account"></a>Registrar uma conta de Data Explorer do Azure

Para registrar uma nova conta de Data Explorer do Azure (Kusto) em seu catálogo de dados, faça o seguinte:

1. Navegue até sua conta do alcance
1. Selecionar **fontes** no painel de navegação esquerdo
1. Escolha **Registrar**
1. Em **registrar fontes**, selecione **Data Explorer do Azure**
1. Selecione **Continuar**

:::image type="content" source="media/register-scan-azure-data-explorer/register-new-data-source.png" alt-text="registrar nova fonte de dados" border="true":::

Na tela **registrar fontes (data Explorer do Azure (Kusto))** , faça o seguinte:

1. Insira um **nome** no qual a fonte de dados será listada no catálogo.
1. Escolha como você deseja apontar para a conta de armazenamento desejada:
   1. Selecione **da assinatura do Azure**, selecione a assinatura apropriada na caixa suspensa **assinatura do Azure** e o cluster apropriado na caixa suspensa **cluster** .
   1. Ou, você pode selecionar **inserir manualmente** e inserir um ponto de extremidade de serviço (URL).
1. **Concluir** para registrar a fonte de dados.

:::image type="content" source="media/register-scan-azure-data-explorer/register-sources.png" alt-text="opções de registrar fontes" border="true":::

[!INCLUDE [create and manage scans](includes/manage-scans.md)]

## <a name="next-steps"></a>Próximas etapas

- [Procurar o catálogo de dados do Azure alcance](how-to-browse-catalog.md)
- [Pesquisar no catálogo de dados do Azure alcance](how-to-search-catalog.md)
