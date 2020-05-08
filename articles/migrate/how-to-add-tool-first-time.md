---
title: Adicionar uma ferramenta de avaliação/migração em migrações para Azure
description: Descreve como criar um projeto de migrações para Azure e adicionar uma ferramenta de avaliação/migração.
ms.topic: how-to
ms.date: 04/16/2020
ms.openlocfilehash: 1294831162df973e1f87052c1520d5d95990cf8f
ms.sourcegitcommit: 0fda81f271f1a668ed28c55dcc2d0ba2bb417edd
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2020
ms.locfileid: "82901178"
---
# <a name="add-an-assessmentmigration-tool-for-the-first-time"></a>Adicionar uma ferramenta de avaliação/migração pela primeira vez

Este artigo descreve como adicionar uma ferramenta de avaliação ou migração a um projeto de [migrações para Azure](migrate-overview.md) pela primeira vez.  
As migrações para Azure fornecem um hub central para acompanhar a descoberta, avaliação e migração de seus aplicativos e cargas de trabalho locais e VMs de nuvem privada/pública, para o Azure. O Hub fornece as ferramentas de migração do Azure para avaliação e migração, bem como outras ferramentas e [ofertas](migrate-services-overview.md#isv-integration) de fornecedores independentes de software (ISV). 

## <a name="check-permissions-to-create-project"></a>Verificar permissões para criar projeto

Se você ainda não criou um projeto de migrações para Azure, verifique se tem as permissões corretas.

1. No portal do Azure, abra a assinatura e selecione **Controle de acesso (IAM).**
2. Em Verificar acesso, localize a conta relevante e clique nela para exibir as permissões. Você deve ter permissões de Colaborador e Proprietário.
    - Se você acaba de criar uma conta gratuita do Azure, você é o proprietário da assinatura.
    - Se você não for o proprietário da assinatura, trabalhe com o proprietário para atribuir a função.

## <a name="create-a-project-and-add-a-tool"></a>Criar um projeto e adicionar uma ferramenta

Configure um novo projeto de migrações para Azure em uma assinatura do Azure e adicione uma ferramenta.

- Um projeto de migrações para Azure é usado para armazenar metadados de descoberta, avaliação e migração coletados do ambiente que você está avaliando ou migrando. 
- Em um projeto, você pode acompanhar os ativos descobertos e orquestrar a avaliação e a migração.

1. No portal do Azure > **Todos os serviços**, pesquise **Migrações para Azure**.
2. Em **Serviços**, selecione **Migrações para Azure**.

    ![Configurar Migrações para Azure](./media/how-to-add-tool-first-time/azure-migrate-search.png)

3. Em **Visão Geral**, clique em **Avaliar emigrar servidores**.
4. Em **Descobrir, avaliar e migrar servidores**, clique em **Avaliar e migrar servidores**.

    ![Descobrir e avaliar servidores](./media/how-to-add-tool-first-time/assess-migrate.png)

1. Em **Descobrir, avaliar e migrar servidores**, clique em **Adicionar ferramentas**.
2. Em **Migrar projeto**, selecione sua assinatura do Azure e crie um grupo de recursos, caso não tenha um.
3. Em **detalhes do projeto**, especifique o nome do projeto e a geografia em que você deseja criar o projeto.  Examine as geografias compatíveis para [nuvens públicas](migrate-support-matrix.md#supported-geographies-public-cloud) e [governamentais](migrate-support-matrix.md#supported-geographies-azure-government).

    ![Criar um projeto das Migrações para Azure](./media/how-to-add-tool-first-time/migrate-project.png)

    - A localização geográfica especificada para o projeto só é usada para armazenar os metadados coletados das VMs locais. Você pode selecionar qualquer região de destino para a migração real.
    - Se você precisar implantar um projeto dentro de uma região específica em uma geografia, use a API a seguir para criar um projeto. Especifique a ID da assinatura, o nome do grupo de recursos e o nome do projeto, juntamente com o local. Examine as geografias/regiões para nuvens [públicas](migrate-support-matrix.md#supported-geographies-public-cloud) e [governamentais](migrate-support-matrix.md#supported-geographies-azure-government).

        `PUT /subscriptions/<subid>/resourceGroups/<rg>/providers/Microsoft.Migrate/MigrateProjects/<mymigrateprojectname>?api-version=2018-09-01-preview "{location: 'centralus', properties: {}}"`   


4. Clique em **Avançar**e adicione uma ferramenta de avaliação ou migração.

    > [!NOTE]
    > Ao criar um projeto, você precisa adicionar pelo menos uma ferramenta de avaliação ou migração.

5. Em **selecionar ferramenta de avaliação**, adicione uma ferramenta de avaliação. Se você não precisar de uma ferramenta de avaliação, selecione **ignorar a adição de uma ferramenta de avaliação para agora** > **em seguida**. 
2. Em **selecionar ferramenta de migração**, adicione uma ferramenta de migração conforme necessário. Se você não precisar de uma ferramenta de migração no momento, selecione **ignorar a adição de uma ferramenta de migração para agora** > **em seguida**.
3. Em **Examinar + adicionar ferramentas**, examine as configurações e clique em **Adicionar ferramentas**.

Depois de criar o projeto, você pode selecionar ferramentas adicionais para avaliação e migração de servidores e cargas de trabalho, bancos de dados e aplicativos Web.

## <a name="create-additional-projects"></a>Criar projetos adicionais

Em algumas circunstâncias, talvez seja necessário criar projetos adicionais de migrações para Azure. Por exemplo, se você tiver data centers em geografias diferentes, ou se precisar armazenar metadados em uma geografia diferente. Crie um projeto adicional da seguinte maneira:

1. No projeto atual de migrações para Azure, clique em **servidores** ou **bancos de dados**.
2. No canto superior direito, clique em **alterar** ao lado do nome do projeto atual.
3. Em **configurações**, selecione **clique aqui para criar um novo projeto**.
4. Crie um novo projeto e adicione uma nova ferramenta, conforme descrito no procedimento anterior.

## <a name="next-steps"></a>Próximas etapas

- Introdução às [migrações para Azure: avaliação de servidor](migrate-services-overview.md#azure-migrate-server-assessment-tool)ou [migrações para Azure: migração de servidor](migrate-services-overview.md#azure-migrate-server-migration-tool).
- Se você adicionou uma ferramenta ISV, ou mover, [examine as etapas](prepare-isv-movere.md) para se preparar para vincular a ferramenta para migrações para o Azure.
- Saiba como adicionar ferramentas adicionais de [avaliação](how-to-assess.md) e [migração](how-to-migrate.md) . 
