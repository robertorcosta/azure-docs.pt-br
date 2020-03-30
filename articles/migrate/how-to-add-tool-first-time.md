---
title: Adicione uma ferramenta de avaliação/migração no Azure Migrate
description: Descreve como criar um projeto do Azure Migrate e adicionar uma ferramenta de avaliação/migração.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 11/19/2019
ms.author: raynew
ms.openlocfilehash: 319d97d96bd054aed90079777e2ff83d0e308e5e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74185945"
---
# <a name="add-an-assessmentmigration-tool-for-the-first-time"></a>Adicionar uma ferramenta de avaliação/migração pela primeira vez

Este artigo descreve como adicionar uma ferramenta de avaliação ou migração a um projeto [do Azure Migrate](migrate-overview.md) pela primeira vez.  
O Azure Migrate fornece um hub central para rastrear a descoberta, avaliação e migração de seus aplicativos e cargas de trabalho no local e VMs de nuvem privada/pública para o Azure. O hub fornece ferramentas do Azure Migrate para avaliação e migração, bem como outras ferramentas e [ofertas independentes](migrate-services-overview.md#isv-integration) de fornecedores de software (ISV). 

## <a name="create-a-project-and-add-a-tool"></a>Crie um projeto e adicione uma ferramenta

Configure um novo projeto do Azure Migrate em uma assinatura do Azure e adicione uma ferramenta.

- Um projeto do Azure Migrate é usado para armazenar metadados de descoberta, avaliação e migração coletados do ambiente que você está avaliando ou migrando. 
- Em um projeto, você pode rastrear ativos descobertos e orquestrar avaliação e migração.

1. No portal do Azure > **Todos os serviços**, pesquise **Migrações para Azure**.
2. Em **Serviços**, selecione **Migrações para Azure**.

    ![Configurar Migrações para Azure](./media/how-to-add-tool-first-time/azure-migrate-search.png)

3. Em **Visão Geral**, clique em **Avaliar emigrar servidores**.
4. Em **Descobrir, avaliar e migrar servidores**, clique em **Avaliar e migrar servidores**.

    ![Descobrir e avaliar servidores](./media/how-to-add-tool-first-time/assess-migrate.png)

1. Em **Descobrir, avaliar e migrar servidores**, clique em **Adicionar ferramentas**.
2. Em **Migrar projeto**, selecione sua assinatura do Azure e crie um grupo de recursos, caso não tenha um.
3. Em **Detalhes do projeto,** especifique o nome do projeto e a geografia em que deseja criar o projeto. 

    ![Criar um projeto das Migrações para Azure](./media/how-to-add-tool-first-time/migrate-project.png)

    Você pode criar um projeto de Migrações para Azure em qualquer uma dessas regiões.

   **Geografia** | **Região de localização de armazenamento**
    --- | ---
    Ásia   | Sudeste Asiático ou Leste da Ásia
    Europa | Norte da Europa ou Europa Ocidental
    Japão  | Japão Leste ou Japão Oeste
    United Kingdom | Sul do Reino Unido ou Oeste do Reino Unido
    Estados Unidos | Eua Central ou Oeste dos EUA 2
    Canada | Canadá Central
    Índia  | Índia Central ou Índia Sul
    Austrália | Austrália Sudeste

    A localização geográfica especificada para o projeto só é usada para armazenar os metadados coletados das VMs locais. Você pode selecionar qualquer região de destino para a migração real.

    Se você quiser especificar uma região específica dentro de uma geografia para implantar o projeto migração e seus recursos associados (as restrições de diretiva em sua assinatura podem permitir a implantação de recursos do Azure apenas para uma região Azure específica), você pode usar a API abaixo para criar um projeto de migração. Especifique o ID de assinatura, nome do grupo de recursos, nome do projeto Migrar junto com a localização (qualquer uma das regiões do Azure mencionadas na tabela onde o Azure Migrate é implantado.)

    `PUT /subscriptions/<subid>/resourceGroups/<rg>/providers/Microsoft.Migrate/MigrateProjects/<mymigrateprojectname>?api-version=2018-09-01-preview "{location: 'centralus', properties: {}}"`   


4. Clique **em Next**e adicione uma ferramenta de avaliação ou migração.

    > [!NOTE]
    > Ao criar um projeto, você precisa adicionar pelo menos uma ferramenta de avaliação ou migração.

5. Na **ferramenta de avaliação Select,** adicione uma ferramenta de avaliação. Se você não precisar de uma ferramenta de avaliação, selecione **Skip adicionando uma ferramenta de avaliação por enquanto** > **Next**. 
2. Na **ferramenta De seleção de migração,** adicione uma ferramenta de migração conforme necessário. Se você não precisar de uma ferramenta de migração agora, **selecione Skip adicionando uma ferramenta de migração para agora** > **Next**.
3. Em **'Revisar + adicionar ferramentas',** revise as configurações e clique **em Adicionar ferramentas**.

Depois de criar o projeto, você pode selecionar ferramentas adicionais para avaliação e migração de servidores e cargas de trabalho, bancos de dados e aplicativos web.

## <a name="create-additional-projects"></a>Criar projetos adicionais

Em algumas circunstâncias, você pode precisar criar projetos adicionais do Azure Migrate. Por exemplo, se você tem datacenters em diferentes geografias, ou precisa armazenar metadados em uma geografia diferente. Crie um projeto adicional da seguinte forma:

1. No projeto Azure Migrate atual, clique **em Servidores** ou **Bancos de Dados**.
2. No canto superior direito, clique em **Alterar** ao lado do nome atual do projeto.
3. Em **Configurações,** **selecione Clique aqui para criar um novo projeto**.
4. Crie um novo projeto e adicione uma nova ferramenta como descrito no procedimento anterior.

## <a name="next-steps"></a>Próximas etapas

Saiba como adicionar ferramentas adicionais de [avaliação](how-to-assess.md) e [migração.](how-to-migrate.md) 
