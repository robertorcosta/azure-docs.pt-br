---
title: 'Início Rápido: criar uma instância de modo híbrido do Serviço de Migração de Banco de Dados do Azure usando o Portal do Azure | Microsoft Docs'
description: Use o portal do Azure para criar uma instância do Serviço de Migração de Banco de Dados do Azure no modo híbrido
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: quickstart
ms.date: 11/06/2019
ms.openlocfilehash: 47c0ecb5674f821d3034fb5f165df08f176c2e93
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73646936"
---
# <a name="quickstart-create-an-instance-of-azure-database-migration-service-in-hybrid-mode-using-the-azure-portal-preview"></a>Início Rápido: criar uma instância do Serviço de Migração de Banco de Dados do Azure no modo híbrido usando o portal do Azure (versão prévia)

O modo híbrido do Serviço de Migração de Banco de Dados do Azure gerencia migrações de banco de dados usando um trabalho de migração que é hospedado localmente junto com uma instância do Serviço de Migração de Banco de Dados do Azure em execução na nuvem. O modo híbrido é especialmente útil para cenários nos quais há uma falta de conectividade site a site entre a rede local e o Azure ou se há largura de banda de conectividade limitada de site a site.

Nesse Início Rápido, use o portal do Azure para criar uma instância do Serviço de Migração de Banco de Dados do Azure no modo híbrido. Posteriormente, você baixa, instala e configura o Hybrid Worker em sua rede local. Durante a versão prévia, é possível usar o modo híbrido do Serviço de Migração de Banco de Dados do Azure para migrar dados de uma instância local do SQL Server para o Banco de Dados SQL do Azure.

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Abra seu navegador da Web, navegue até o [portal do Microsoft Azure](https://portal.azure.com/) e insira suas credenciais para entrar no portal.

A exibição padrão é o painel de serviço.

## <a name="register-the-resource-provider"></a>Registre o provedor de recursos

Registre o provedor de recursos Microsoft.DataMigration antes de criar sua primeira instância do Serviço de Migração de Banco de Dados do Azure.

1. No portal do Azure, selecione **Assinaturas**, escolha a assinatura na qual você deseja criar a instância do Serviço de Migração de Banco de Dados do Azure e, em seguida, selecione **Provedores de recursos**.

    ![Provedor de recursos do pesquisa](media/quickstart-create-data-migration-service-hybrid-portal/dms-portal-search-resource-provider.png)

2. Pesquise por migração e, em seguida, à direita do **Microsoft.DataMigration**, selecione **Registrar**.

    ![Registrar provedor de recursos](media/quickstart-create-data-migration-service-hybrid-portal/dms-portal-register-resource-provider.png)

## <a name="create-an-instance-of-the-service"></a>Crie uma instância do serviço

1. Selecione +**Criar um recurso** para criar uma instância do Serviço de Migração de Banco de Dados do Azure.

2. Pesquise "migração" no marketplace, selecione **Serviço de Migração de Banco de Dados do Azure** e, em seguida, na tela **Serviço de Migração de Banco de Dados do Azure**, selecione **Criar**.

3. Na tela **Criar Serviço de Migração**:

    - Escolha um **Nome de Serviço** que seja fácil de lembrar e exclusivo para identificar sua instância do Serviço de Migração de Banco de Dados do Azure.
    - Selecione a **Assinatura** do Azure na qual quer criar a instância.
    - Selecione um **Grupo de Recursos** existente ou crie um novo.
    - Escolha o **Local** mais próximo ao seu servidor de origem ou de destino.

    > [!IMPORTANT]
    > Durante a versão prévia, há suporte para o modo híbrido apenas na região Leste dos EUA. Como o Hybrid Worker está instalado em sua rede local, há pouco ou nenhum impacto no desempenho, mesmo se você estiver migrando para um destino em uma região diferente.

    - No **Modo de serviço**, selecione **Híbrido (versão prévia)** .

      ![Criar serviço de migração – noções básicas](media/quickstart-create-data-migration-service-hybrid-portal/dms-create-service-basics.png)

4. Selecione **Examinar + criar**.

5. Na guia **Examinar + criar**, revise os Termos, verifique as outras informações fornecidas e, em seguida, selecione **Criar**.

    ![Criar serviço de migração – examinar + criar](media/quickstart-create-data-migration-service-hybrid-portal/dms-create-service-review-and-create.png)

    Após alguns instantes, sua instância do Serviço de Migração de Banco de Dados do Azure no modo híbrido é criada e estará pronta para uso. A instância do Serviço de Migração de Banco de Dados do Azure é exibida conforme mostrado na imagem a seguir:

    ![Instância de modo híbrido do Serviço de Migração de Banco de Dados do Azure](media/quickstart-create-data-migration-service-hybrid-portal/dms-instance-hybrid-mode.png)

6. Depois que o serviço for criado, selecione **Propriedades** e, em seguida, copie o valor exibido na caixa **ID do recurso**, que será usado para instalar o Hybrid Worker do Serviço de Migração de Banco de Dados do Azure.

    ![Propriedades de modo híbrido do Serviço de Migração de Banco de Dados do Azure](media/quickstart-create-data-migration-service-hybrid-portal/dms-copy-resource-id.png)

## <a name="create-azure-app-registration-id"></a>Criar a ID de registro do Aplicativo Azure

Você precisa criar uma ID de registro do Aplicativo Azure que o Hybrid Worker local possa usar para se comunicar com o Serviço de Migração de Banco de Dados do Azure na nuvem.

1. No portal do Azure, selecione **Azure Active Directory**, escolha **Registros de aplicativos** e, em seguida, selecione **Novo registro**.
2. Especifique um nome para o aplicativo e, em seguida, em **Tipos de conta com suporte**, selecione os tipos de contas com suporte para especificar quem pode usar o aplicativo.

    ![Aplicativo de registro modo híbrido do Serviço de Migração de Banco de Dados do Azure](media/quickstart-create-data-migration-service-hybrid-portal/dms-register-application.png)

3. Use os valores padrão para os campos **URI de redirecionamento (opcional)** e, em seguida, selecione **Registrar**.

4. Após concluir o registro da ID do aplicativo, anote a **ID do aplicativo (cliente)** , que será usada ao instalar o Hybrid Worker.

5. Na portal do Azure, navegue até Serviço de Migração de Banco de Dados do Azure, selecione **IAM (controle de acesso)** e, em seguida, selecione **Adicionar atribuição de função** para atribuir acesso de colaborador à ID do aplicativo.

    ![Atribuir função de colaborador no modo híbrido do Serviço de Migração de Banco de Dados do Azure](media/quickstart-create-data-migration-service-hybrid-portal/dms-app-assign-contributor.png)

6. Selecione **Colaborador** como a função, atribua acesso à **Entidade de serviço ou usuário do Azure AD** e, em seguida, selecione o nome da ID do aplicativo.

    ![Detalhes de atribuir função de colaborador no modo híbrido do Serviço de Migração de Banco de Dados do Azure](media/quickstart-create-data-migration-service-hybrid-portal/dms-add-role-assignment.png)

7. Selecione **Salvar** para salvar a atribuição de função para a ID do aplicativo no recurso de Serviço de Migração de Banco de Dados do Azure.

## <a name="download-and-install-the-hybrid-worker"></a>Baixar e instalar o Hybrid Worker

1. Na portal do Azure, navegue até sua instância do Serviço de Migração de Banco de Dados do Azure.

2. Em **Configurações**, selecione **Híbrido** e, em seguida, selecione **Baixar o instalador** para baixar o Hybrid Worker.

    ![Baixar o Hybrid Worker do Serviço de Migração de Banco de Dados do Azure](media/quickstart-create-data-migration-service-hybrid-portal/dms-installer-download.png)

3. Extraia o arquivo ZIP no servidor que hospedará o Hybrid Worker do Serviço de Migração de Banco de Dados do Azure.

4. Na pasta de instalação, localize e abra o arquivo **dmsSettings.json**, especifique o **ApplicationId** e **resourceId** e, em seguida, salve o arquivo.

    ![Configurar o Hybrid Worker do Serviço de Migração de Banco de Dados do Azure](media/quickstart-create-data-migration-service-hybrid-portal/dms-settings.png)
 
5. Gere um certificado que o Serviço de Migração de Banco de Dados do Azure possa usar para autenticar a comunicação do Hybrid Worker usando o comando a seguir.

    ```
    <drive>:\<folder>\Install>DMSWorkerBootstrap.exe -a GenerateCert
    ```

    Um certificado é gerado na pasta de instalação.

    ![Certificado do Hybrid Worker do Serviço de Migração de Banco de Dados do Azure](media/quickstart-create-data-migration-service-hybrid-portal/dms-certificate.png)

6. No portal do Azure, navegue até a ID do aplicativo, em **Gerenciar**, selecione **Certificado e segredos** e, em seguida, selecione **Carregar certificado** para selecionar o certificado público que você acabou de gerar.

    ![Carregar o certificado do Hybrid Worker do Serviço de Migração de Banco de Dados do Azure](media/quickstart-create-data-migration-service-hybrid-portal/dms-app-upload-certificate.png)

7. Instale o Hybrid Worker do Serviço de Migração de Banco de Dados do Azure em seu servidor local executando o seguinte comando:

    ```
    <drive>:\<folder>\Install>DMSWorkerBootstrap.exe -a Install -IAcceptDMSLicenseTerms
    ```

8. Se o instalador for executado sem erros, o serviço mostrará um status online no Serviço de Migração de Banco de Dados do Azure e você estará pronto para migrar seus bancos de dados.

    ![Serviço de Migração de Banco de Dados do Azure online](media/quickstart-create-data-migration-service-hybrid-portal/dms-instance-hybrid-mode-online.png)

## <a name="uninstall-azure-database-migration-service-hybrid-mode"></a>Desinstalar o modo híbrido do Serviço de Migração de Banco de Dados do Azure

Atualmente, a desinstalação do modo híbrido do Serviço de Migração de Banco de Dados do Azure tem suporte apenas por meio do instalador do Hybrid Worker do Serviço de Migração de Banco de Dados do Azure no servidor local, usando o seguinte comando:

```
<drive>:\<folder>\Install>DMSWorkerBootstrap.exe -a uninstall
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Migrar o SQL Server para uma instância gerenciada do Banco de Dados SQL do Azure online](tutorial-sql-server-managed-instance-online.md)
> [Migrar o SQL Server para um banco de dados individual ou um banco de dados em pool no Banco de Dados SQL do Azure offline](tutorial-sql-server-to-azure-sql.md)
