---
title: Registrar e verificar Azure Data Lake Storage (ADLS) Gen2
description: Este tutorial descreve como verificar Azure Data Lake Storage Gen2.
author: prmujumd
ms.author: prmujumd
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/17/2020
ms.openlocfilehash: ec708009e3f3f258c1c40aa6a06a35452d5988f4
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/03/2020
ms.locfileid: "96551797"
---
# <a name="register-and-scan-azure-data-lake-storage-gen2"></a>Registrar e verificar Azure Data Lake Storage Gen2

Este artigo descreve como registrar Azure Data Lake Storage Gen2 como fonte de dados no Azure alcance e configurar uma verificação.

## <a name="supported-capabilities"></a>Funcionalidades com suporte

A fonte de dados Azure Data Lake Storage Gen2 dá suporte à seguinte funcionalidade:

- **Verificações completas e incrementais** para capturar metadados e classificação no Azure data Lake Storage Gen2

- **Linhagem** entre os ativos de dados para as atividades de cópia/Dataflow do ADF

## <a name="prerequisites"></a>Pré-requisitos

Antes de registrar as fontes de dados, crie uma conta do Azure alcance. Para obter mais informações sobre como criar uma conta do alcance, consulte [início rápido: criar uma conta do Azure alcance](create-catalog-portal.md).

### <a name="setting-up-authentication-for-a-scan"></a>Configurando a autenticação para uma verificação

Os seguintes métodos de autenticação têm suporte para Azure Data Lake Storage Gen2:

- Identidade Gerenciada
- Entidade de serviço
- Chave de conta

#### <a name="managed-identity-recommended"></a>Identidade gerenciada (recomendado)

Ao escolher a **identidade gerenciada**, para configurar a conexão, primeiro você deve conceder à sua conta do alcance a permissão para verificar a fonte de dados:

1. Navegue até sua conta de armazenamento ADLS Gen2.
1. Selecione **controle de acesso (iam)** no menu de navegação à esquerda. 
1. Selecione **+ Adicionar**.
1. Defina a **função** para o **leitor de dados de blob de armazenamento** e insira o nome da conta do Azure alcance em **selecionar** caixa de entrada. Em seguida, selecione **salvar** para dar essa atribuição de função à sua conta do alcance.

> [!Note]
> Para obter mais detalhes, consulte as etapas em [autorizar o acesso a BLOBs e filas usando o Azure Active Directory](https://docs.microsoft.com/azure/storage/common/storage-auth-aad)

#### <a name="account-key"></a>Chave de conta

Quando o método de autenticação selecionado é **chave de conta**, você precisa obter sua chave de acesso e armazenar no cofre de chaves:

1. Navegue até sua conta de armazenamento do ADLS Gne2
1. Selecione **configurações > chaves de acesso**
1. Copie sua *chave* e salve-a em algum lugar para as próximas etapas
1. Navegue até o Key Vault
1. Selecione **configurações > segredos**
1. Selecione **+ gerar/importar** e insira o **nome** e o **valor** como a *chave* de sua conta de armazenamento
1. Selecione **criar** para concluir
1. Se o cofre de chaves ainda não estiver conectado ao alcance, você precisará [criar uma nova conexão de Key Vault](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)
1. Por fim, [crie uma nova credencial](manage-credentials.md#create-a-new-credential) usando a chave para configurar sua verificação

#### <a name="service-principal"></a>Entidade de serviço

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

##### <a name="granting-the-service-principal-access-to-your-adls-gen2-account"></a>Concedendo acesso à entidade de serviço à sua conta do ADLS Gen2

1. Navegue para sua conta de armazenamento.
1. Selecione **controle de acesso (iam)** no menu de navegação à esquerda. 
1. Selecione **+ Adicionar**.
1. Defina a **função** para o **leitor de dados de blob de armazenamento** e insira o nome da entidade de serviço ou a ID de objeto na caixa **selecionar** entrada. Em seguida, selecione **salvar** para dar essa atribuição de função à entidade de serviço.
### <a name="firewall-settings"></a>Configurações de firewall

> [!NOTE]
> Se você tiver o firewall habilitado para a conta de armazenamento, deverá usar o método de autenticação de **identidade gerenciada** ao configurar uma verificação.

1. Acesse sua conta de armazenamento do ASLS Gen2 no [portal do Azure](https://portal.azure.com)
1. Navegue até **configurações > rede** e
1. Escolha **redes selecionadas** em **permitir acesso de**
1. Na seção **exceções** , selecione **permitir que os serviços confiáveis da Microsoft acessem esta conta de armazenamento** e clique em **salvar**

:::image type="content" source="./media/register-scan-adls-gen2/firewall-setting.png" alt-text="Captura de tela mostrando a configuração de firewall":::

## <a name="register-azure-data-lake-storage-gen2-data-source"></a>Registrar Azure Data Lake Storage Gen2 fonte de dados

Para registrar uma nova conta de ADLS Gen2 no catálogo de dados, faça o seguinte:

1. Navegue até sua conta do alcance
2. Selecionar **fontes** no painel de navegação esquerdo
3. Escolha **Registrar**
4. Em **registrar fontes**, selecione **Azure data Lake Storage Gen2**
5. Selecione **Continuar**

Na tela **registrar fontes (Azure data Lake Storage Gen2)** , faça o seguinte:

1. Insira um **nome** no qual a fonte de dados será listada no catálogo.
2. Escolha sua assinatura para filtrar as contas de armazenamento
3. Selecione uma conta de armazenamento
4. Selecionar uma coleção ou criar uma nova (opcional)
5. **Concluir** para registrar a fonte de dados.

:::image type="content" source="media/register-scan-adls-gen2/register-sources.png" alt-text="opções de registrar fontes" border="true":::

[!INCLUDE [create and manage scans](includes/manage-scans.md)]

## <a name="next-steps"></a>Próximas etapas

- [Procurar o catálogo de dados do Azure alcance](how-to-browse-catalog.md)
- [Pesquisar no catálogo de dados do Azure alcance](how-to-search-catalog.md)
