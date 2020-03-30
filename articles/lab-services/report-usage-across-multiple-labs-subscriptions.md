---
title: Uso do Azure DevTest Labs em vários laboratórios e assinaturas
description: Saiba como relatar o uso do Azure DevTest Labs em vários laboratórios e assinaturas.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: tanmayeekamath
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: takamath
ms.openlocfilehash: 912f510f6380c0ba1eb92b7c485091801123558e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169176"
---
# <a name="report-azure-devtest-labs-usage-across-multiple-labs-and-subscriptions"></a>Reporte o uso do Azure DevTest Labs em vários laboratórios e assinaturas

A maioria das grandes organizações quer rastrear o uso de recursos para ser mais eficaz com esses recursos, visualizando tendências e outliers no uso. Com base no uso de recursos, os proprietários ou gerentes do laboratório podem personalizar os laboratórios para melhorar o [uso e os custos dos recursos.](https://docs.microsoft.com/azure/billing/billing-getting-started) No Azure DevTest Labs, você pode baixar o uso de recursos por laboratório, permitindo uma visão histórica mais profunda dos padrões de uso. Esses padrões de uso podem ajudar a identificar mudanças para melhorar a eficiência. A maioria das empresas quer o uso de laboratório individual e o uso geral em [vários laboratórios e assinaturas.](https://docs.microsoft.com/azure/architecture/cloud-adoption/decision-guides/subscriptions/) 

Este artigo discute como lidar com informações de uso de recursos em vários laboratórios e assinaturas.

![Uso de relatórios](./media/report-usage-across-multiple-labs-subscriptions/report-usage.png)

## <a name="individual-lab-usage"></a>Uso individual de laboratório

Esta seção discute como exportar o uso de recursos para um único laboratório.

Antes de exportar o uso de recursos do DevTest Labs, você tem que configurar uma conta do Azure Storage para permitir que os diferentes arquivos que contêm os dados de uso sejam armazenados. Existem duas maneiras comuns de executar a exportação de dados:

* [DevTest Labs REST API](https://docs.microsoft.com/rest/api/dtl/labs/exportresourceusage) 
* O módulo PowerShell Az.Resource [Invoke-AzResourceAction](https://docs.microsoft.com/powershell/module/az.resources/invoke-azresourceaction?view=azps-2.5.0&viewFallbackFrom=azps-2.3.2) com a ação de , o ID de `exportResourceUsage`recurso de laboratório e os parâmetros necessários. 

    O artigo [de exportação ou exclusão de dados pessoais](personal-data-delete-export.md) contém um script de amostra PowerShell com informações detalhadas sobre os dados exportados. 

    > [!NOTE]
    > O parâmetro de data não inclui um carimbo de horário, então os dados incluem tudo a partir da meia-noite com base no fuso horário onde o laboratório está localizado.

Uma vez que a exportação esteja concluída, haverá vários arquivos CSV no armazenamento blob com as diferentes informações de recursos.
  
Atualmente existem dois arquivos CSV:

* *virtualmachines.csv* - contém informações sobre as máquinas virtuais no laboratório
* *disks.csv* - contém informações sobre os diferentes discos no laboratório 

Esses arquivos são armazenados no recipiente *blob de uso de recursos de laboratório* o nome do laboratório, ID exclusivo do laboratório, data executada e total ou a data de início que foi baseada na solicitação de exportação. Um exemplo de estrutura de bolhas seria:

* `labresourceusage/labname/1111aaaa-bbbb-cccc-dddd-2222eeee/<End>DD26-MM6-2019YYYY/full/virtualmachines.csv`
* `labresourceusage/labname/1111aaaa-bbbb-cccc-dddd-2222eeee/<End>DD-MM-YYYY/26-6-2019/20-6-2019<Start>DD-MM-YYYY/virtualmachines.csv`

## <a name="exporting-usage-for-all-labs"></a>Exportação de uso para todos os laboratórios

Para exportar as informações de uso para vários laboratórios considere usar 

* [Funções do Azure,](https://docs.microsoft.com/azure/azure-functions/)disponíveis em muitos idiomas, incluindo PowerShell, ou 
* [Runbook azure Automation](https://docs.microsoft.com/azure/automation/), use PowerShell, Python ou um designer gráfico personalizado para escrever o código de exportação.

Usando essas tecnologias, você pode executar as exportações individuais de laboratório em todos os laboratórios em uma data e hora específicas. 

A função Azure deve empurrar os dados para o armazenamento a longo prazo. Ao exportar dados para vários laboratórios, a exportação pode levar algum tempo. Para ajudar no desempenho e reduzir a possibilidade de duplicação de informações, recomendamos executar cada laboratório em paralelo. Para realizar o paralelismo, execute as funções do Azure de forma assíncrona. Aproveite também o gatilho do temporizador que o Azure Functions oferece.

## <a name="using-a-long-term-storage"></a>Usando um armazenamento de longo prazo

Um armazenamento de longo prazo consolida as informações de exportação de diferentes laboratórios em uma única fonte de dados. Outro benefício do uso do armazenamento a longo prazo é ser capaz de remover os arquivos da conta de armazenamento para reduzir a duplicação e o custo. 

O armazenamento a longo prazo pode ser usado para fazer qualquer manipulação de texto, por exemplo: 

* adicionando nomes amigáveis
* criando agrupamentos complexos
* agregando os dados.

Algumas soluções comuns de armazenamento são: [SQL Server,](https://azure.microsoft.com/services/sql-database/) [Azure Data Lake](https://azure.microsoft.com/services/storage/data-lake-storage/)e [Cosmos DB](https://azure.microsoft.com/services/cosmos-db/). Escolher qual solução de armazenamento de longo prazo você escolhe depende da preferência. Você pode considerar a escolha da ferramenta dependendo do que ela oferece em termos de disponibilidade de interação ao visualizar os dados.

## <a name="visualizing-data-and-gathering-insights"></a>Visualização de dados e coleta de insights

Use uma ferramenta de visualização de dados de sua escolha para se conectar ao seu armazenamento de longo prazo para exibir os dados de uso e coletar insights para verificar a eficiência do uso. Por exemplo, [o Power BI](https://docs.microsoft.com/power-bi/power-bi-overview) pode ser usado para organizar e exibir os dados de uso. 

Você pode usar [a Fábrica de Dados do Azure](https://azure.microsoft.com/services/data-factory/) para criar, vincular e gerenciar seus recursos em uma única interface de localização. Se for necessário um controle maior, o recurso individual pode ser criado dentro de um único grupo de recursos e gerenciado independentemente do serviço Data Factory.  

## <a name="next-steps"></a>Próximas etapas

Uma vez que o sistema é configurado e os dados estão se movendo para o armazenamento de longo prazo, o próximo passo é chegar às perguntas que os dados precisam responder. Por exemplo:  

-   Qual é o uso do tamanho da VM?

    Os usuários estão selecionando tamanhos de VM de alto desempenho (mais caros)?
-   Quais imagens do Marketplace estão sendo usadas?

    São imagens personalizadas a base de VM mais comum, caso uma loja de imagens comum seja construída como [galeria de imagens compartilhadas](../virtual-machines/windows/shared-image-galleries.md) ou [fábrica de imagens](image-factory-create.md).
-   Quais imagens personalizadas estão sendo usadas ou não?
