---
title: Registrar e verificar um SQL Server local
description: Este tutorial descreve como verificar o SQL Server local usando um IR auto-hospedado.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 09/18/2020
ms.openlocfilehash: 0d282ee805ac61ba17ceb3ecc6a3d8179ea7b319
ms.sourcegitcommit: 6628bce68a5a99f451417a115be4b21d49878bb2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/18/2021
ms.locfileid: "98555892"
---
# <a name="register-and-scan-an-on-premises-sql-server"></a>Registrar e verificar um SQL Server local

Este artigo descreve como registrar uma fonte de dados do SQL Server no alcance e configurar uma verificação nela.

## <a name="supported-capabilities"></a>Funcionalidades compatíveis

A fonte de dados local do SQL Server dá suporte à seguinte funcionalidade:

- **Verificações completas e incrementais** para capturar metadados e classificação em uma rede local ou um SQL Server instalado em uma VM do Azure.

- **Linhagem** entre os ativos de dados para as atividades de cópia/Dataflow do ADF

A fonte de dados local do SQL Server dá suporte a:

- todas as versões do SQL do SQL Server 2019 de volta para o SQL Server 2000

- Método de autenticação: autenticação SQL

### <a name="known-limitations"></a>Limitações conhecidas

O Azure alcance não dá suporte à verificação de [exibições](/sql/relational-databases/views/views) no SQL Server.

## <a name="prerequisites"></a>Pré-requisitos

- Antes de registrar as fontes de dados, crie uma conta do Azure alcance. Para obter mais informações sobre como criar uma conta do alcance, consulte [início rápido: criar uma conta do Azure alcance](create-catalog-portal.md).

- Configure um [tempo de execução de integração auto-hospedado](manage-integration-runtimes.md) para verificar a fonte de dados.

## <a name="setting-up-authentication-for-a-scan"></a>Configurar a autenticação para uma verificação

Há apenas uma maneira de configurar a autenticação para o SQL Server local:

- Autenticação do SQL

### <a name="sql-authentication"></a>Autenticação SQL

A identidade do SQL deve ter acesso ao banco de dados primário. Esse local é o local em que `sys.databases` está armazenado. O scanner alcance precisa enumerar para `sys.databases` Localizar todas as instâncias de BD SQL no servidor.

#### <a name="using-an-existing-server-administrator"></a>Usando um administrador de servidor existente

Se você planeja usar um usuário existente do SA (administrador do servidor) para verificar seu SQL Server local, verifique o seguinte:

1. `sa` Não é o tipo de autenticação do Windows.

2. O usuário de nível de servidor que você está planejando usar deve ter funções de servidor de Public e sysadmin. Você pode verificar isso navegando até SQL Server Management Studio (SSMS), conectando-se ao servidor, navegando até segurança, selecionando o logon que você está planejando usar, clicando com o botão direito do mouse em **Propriedades** e selecionando **funções de servidor**.

   :::image type="content" source="media/register-scan-on-premises-sql-server/server-level-login.png" alt-text="Logon no nível do servidor.":::

3. Os bancos de dados são mapeados para um usuário que tem pelo menos o acesso de nível db_datareader em cada um deles.

   :::image type="content" source="media/register-scan-on-premises-sql-server/user-mapping-sa.png" alt-text="mapeamento de usuário para SA.":::

#### <a name="creating-a-new-login-and-user"></a>Criando um novo logon e usuário

Se você quiser criar um novo logon e um usuário para poder verificar o SQL Server, siga as etapas abaixo:

1. Navegue até SQL Server Management Studio (SSMS), conecte-se ao servidor, navegue até segurança, clique com o botão direito do mouse em logon e crie um novo logon. Certifique-se de selecionar Autenticação SQL.

   :::image type="content" source="media/register-scan-on-premises-sql-server/create-new-login-user.png" alt-text="Crie um novo logon e usuário.":::

2. Selecione funções de servidor no painel de navegação esquerdo e selecione pública e sysadmin.

3. Selecione mapeamento de usuário no painel de navegação esquerdo e selecione todos os bancos de dados no mapa.

   :::image type="content" source="media/register-scan-on-premises-sql-server/user-mapping.png" alt-text="mapeamento de usuário.":::

4. Clique em OK para salvar.

5. Navegue novamente para o usuário que você criou, clicando com o botão direito do mouse e selecionando **Propriedades**. Insira uma nova senha e confirme-a. Selecione ' especificar senha antiga ' e insira a senha antiga. **É necessário alterar sua senha assim que você criar um novo logon.**

   :::image type="content" source="media/register-scan-on-premises-sql-server/change-password.png" alt-text="alterar senha.":::

#### <a name="storing-your-sql-login-password-in-a-key-vault-and-creating-a-credential-in-purview"></a>Armazenando sua senha de logon do SQL em um cofre de chaves e criando uma credencial no alcance

1. Navegue até o seu cofre de chaves no portal do Azure
1. Selecione **Configurações > Segredos**
1. Selecione **+ gerar/importar** e insira o **nome** e o **valor** como a *senha* do seu logon do SQL Server
1. Selecione **Criar** para terminar
1. Se o cofre de chaves ainda não estiver conectado ao Purview, [crie uma conexão do cofre de chaves](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)
1. Por fim, [crie uma credencial](manage-credentials.md#create-a-new-credential) usando o **nome de usuário** e a **senha** para configurar a verificação

## <a name="register-a-sql-server-data-source"></a>Registrar uma fonte de dados do SQL Server

1. Acesse sua conta do Purview

1. Em fontes e verificação no painel de navegação esquerdo, selecione **tempos de execução de integração**. Verifique se um runtime de integração auto-hospedada está configurado. Se não estiver configurado, siga as etapas mencionadas [aqui](manage-integration-runtimes.md) para criar um tempo de execução de integração auto-hospedado para verificação em uma VM do Azure ou local que tenha acesso à sua rede local.

1. Selecionar **Fontes** no painel de navegação à esquerda

1. Escolha **Registrar**

1. Selecione **SQL Server** e **continuar**

   :::image type="content" source="media/register-scan-on-premises-sql-server/set-up-sql-data-source.png" alt-text="Configure a fonte de dados SQL.":::

5. Forneça um nome amigável e um ponto de extremidade do servidor e, em seguida, selecione **concluir** para registrar a fonte de dados. Se, por exemplo, o FQDN do SQL Server for **foobar.Database.Windows.net**, digite *foobar* como o ponto de extremidade do servidor.

[!INCLUDE [create and manage scans](includes/manage-scans.md)]

## <a name="next-steps"></a>Próximas etapas

- [Navegar pelo Catálogo de Dados do Azure Purview](how-to-browse-catalog.md)
- [Pesquisar no Catálogo de Dados do Azure Purview](how-to-search-catalog.md)
