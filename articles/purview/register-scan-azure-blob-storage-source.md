---
title: Como verificar o Azure Storage blob
description: Saiba como verificar o armazenamento de BLOBs do Azure em seu catálogo de dados do Azure alcance.
author: hophan
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/25/2020
ms.openlocfilehash: 6d2e2316525465c1ef9f58e7b83b8d0e99d46bd4
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/03/2020
ms.locfileid: "96551605"
---
# <a name="register-and-scan-azure-blob-storage"></a>Registrar e verificar o armazenamento de BLOBs do Azure

Este artigo descreve como registrar uma conta de armazenamento de BLOBs do Azure no alcance e configurar uma verificação.

## <a name="supported-capabilities"></a>Funcionalidades com suporte

O armazenamento de BLOBs do Azure dá suporte a verificações completas e incrementais para capturar os metadados e o esquema. Ele também classifica os dados automaticamente com base em regras de classificação personalizada e do sistema.

## <a name="prerequisites"></a>Pré-requisitos

- Antes de registrar as fontes de dados, crie uma conta do Azure alcance. Para obter mais informações sobre como criar uma conta do alcance, consulte [início rápido: criar uma conta do Azure alcance](create-catalog-portal.md).
- Você precisa ser um administrador de fonte de dados do Azure alcance

## <a name="setting-up-authentication-for-a-scan"></a>Configurando a autenticação para uma verificação

Há três maneiras de configurar a autenticação para o armazenamento de BLOBs do Azure:

- Identidade Gerenciada
- Chave de conta
- Entidade de Serviço

### <a name="managed-identity-recommended"></a>Identidade gerenciada (recomendado)

Ao escolher a **identidade gerenciada**, para configurar a conexão, primeiro você deve conceder à sua conta do alcance a permissão para verificar a fonte de dados:

1. Navegue para sua conta de armazenamento.
1. Selecione **controle de acesso (iam)** no menu de navegação à esquerda. 
1. Selecione **+ Adicionar**.
1. Defina a **função** para o **leitor de dados de blob de armazenamento** e insira o nome da conta do Azure alcance em **selecionar** caixa de entrada. Em seguida, selecione **salvar** para dar essa atribuição de função à sua conta do alcance.

> [!Note]
> Para obter mais detalhes, consulte as etapas em [autorizar o acesso a BLOBs e filas usando o Azure Active Directory](https://docs.microsoft.com/azure/storage/common/storage-auth-aad)

### <a name="account-key"></a>Chave de conta

Quando o método de autenticação selecionado é **chave de conta**, você precisa obter sua chave de acesso e armazenar no cofre de chaves:

1. Navegue até sua conta de armazenamento
1. Selecione **configurações > chaves de acesso**
1. Copie sua *chave* e salve-a em algum lugar para as próximas etapas
1. Navegue até o Key Vault
1. Selecione **configurações > segredos**
1. Selecione **+ gerar/importar** e insira o **nome** e o **valor** como a *chave* de sua conta de armazenamento
1. Selecione **criar** para concluir
1. Se o cofre de chaves ainda não estiver conectado ao alcance, você precisará [criar uma nova conexão de Key Vault](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)
1. Por fim, [crie uma nova credencial](manage-credentials.md#create-a-new-credential) usando a chave para configurar sua verificação

### <a name="service-principal"></a>Entidade de serviço

Para usar uma entidade de serviço, você pode usar uma existente ou criar uma nova. 

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

#### <a name="granting-the-service-principal-access-to-your-blob-storage"></a>Concedendo acesso à entidade de serviço ao seu armazenamento de BLOBs

1. Navegue para sua conta de armazenamento.
1. Selecione **controle de acesso (iam)** no menu de navegação à esquerda. 
1. Selecione **+ Adicionar**.
1. Defina a **função** para o **leitor de dados de blob de armazenamento** e insira o nome da entidade de serviço ou a ID de objeto na caixa **selecionar** entrada. Em seguida, selecione **salvar** para dar essa atribuição de função à entidade de serviço.

## <a name="firewall-settings"></a>Configurações de firewall

> [!NOTE]
> Se você tiver o firewall habilitado para a conta de armazenamento, deverá usar o método de autenticação de **identidade gerenciada** ao configurar uma verificação.

1. Acesse sua conta de armazenamento no [portal do Azure](https://portal.azure.com)
1. Navegue até **configurações > rede** e
1. Escolha **redes selecionadas** em **permitir acesso de**
1. Na seção **Firewall** , selecione **permitir que os serviços confiáveis da Microsoft acessem esta conta de armazenamento** e clique em **salvar**

:::image type="content" source="./media/register-scan-azure-blob-storage-source/firewall-setting.png" alt-text="Captura de tela mostrando a configuração de firewall":::

## <a name="register-an-azure-blob-storage-account"></a>Registrar uma conta de armazenamento de BLOBs do Azure

Para registrar uma nova conta de BLOB em seu catálogo de dados, faça o seguinte:

1. Navegue até sua conta do alcance
1. Selecionar **fontes** no painel de navegação esquerdo
1. Escolha **Registrar**
1. Em **registrar fontes**, selecione **armazenamento de BLOBs do Azure**
1. Selecione **Continuar**

Na tela **registrar fontes (armazenamento de BLOBs do Azure)** , faça o seguinte:

1. Insira um **nome** no qual a fonte de dados será listada no catálogo. 
1. Escolha sua assinatura para filtrar as contas de armazenamento
1. Selecione uma conta de armazenamento
1. Selecionar uma coleção ou criar uma nova (opcional)
1. **Concluir** para registrar a fonte de dados.

:::image type="content" source="media/register-scan-azure-blob-storage-source/register-sources.png" alt-text="opções de registrar fontes" border="true":::

[!INCLUDE [create and manage scans](includes/manage-scans.md)]

## <a name="next-steps"></a>Próximas etapas

- [Procurar o catálogo de dados do Azure alcance](how-to-browse-catalog.md)
- [Pesquisar no catálogo de dados do Azure alcance](how-to-search-catalog.md)
