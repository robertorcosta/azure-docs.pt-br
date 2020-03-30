---
title: O que é a borda do banco de dados SQL do Azure? | Microsoft Docs
description: Saiba mais sobre a borda do banco de dados SQL do Azure
keywords: introdução à borda do banco de dados sql, o que é a borda do banco de dados sql, visão geral da borda do banco de dados sql
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 03/24/2020
ms.openlocfilehash: d5c48b6036065f6182912c21c144cab80fc3cfbf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246697"
---
# <a name="what-is-azure-sql-database-edge-preview"></a>O que é a visualização de borda do banco de dados Azure SQL?

O Azure SQL Database Edge Preview é um mecanismo de banco de dados relacional otimizado voltado para implantações de IoT e IoT Edge. Ele fornece recursos para criar uma camada de armazenamento e processamento de dados de alto desempenho para aplicativos e soluções de IoT. O Azure SQL Database Edge fornece recursos para transmitir, processar e analisar dados relacionais e não relacionais, como dados JSON, gráficos e séries tempométricas, o que o torna a escolha certa para uma variedade de aplicativos de IoT modernos.

O Azure SQL Database Edge é construído sobre as versões mais recentes do [Microsoft SQL Server Database Engine](/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database-edge/toc.json), que fornece recursos de processamento de desempenho, segurança e consulta líderes do setor. Uma vez que o Azure SQL Database Edge é construído sobre o mesmo motor que o SQL Server e o Azure SQL Database, ele fornece a mesma área de superfície de programação T-SQL que torna o desenvolvimento de aplicativos ou soluções mais fácil e rápido, e ao mesmo tempo torna a aplicação portabilidade entre dispositivos IoT Edge, data centers e a nuvem diretamente.

## <a name="deployment-models"></a>Modelos de implantação

O Azure SQL Database Edge está disponível no Azure Marketplace e pode ser implantado como um módulo para [o Azure IoT Edge](../iot-edge/about-iot-edge.md). Para obter mais informações, consulte [Deploy Azure SQL Database Edge](deploy-portal.md).<br>

![Diagrama de visão geral da borda do banco de dados SQL](media/overview/overview.png)

## <a name="editions-of-sql-database-edge"></a>Edições de SQL Database Edge

O SQL Database Edge está disponível com três edições ou planos de software diferentes. Essas edições têm conjuntos de recursos idênticos e só diferem em termos de seus direitos de uso e da quantidade de cpu/memória que suportam.

   |**Plano**  |**Descrição**  |
   |---------|---------|
   |Desenvolvedor de borda de banco de dados Azure SQL  |  Apenas sku de desenvolvimento, cada contêiner SQL Database Edge é limitado a até 4 núcleos e memória de 32 GB  |
   |Banco de Dados SQL do Azure no Edge    |  Sku de produção, cada contêiner SQL Database Edge é limitado a até 8 núcleos e memória de 64 GB . |

## <a name="pricing-and-availability"></a>Preços e Disponibilidade

O Banco de Dados SQL do Azure está atualmente em pré-visualização. Para obter mais informações sobre os preços e a disponibilidade, consulte [Azure SQL Database Edge](https://azure.microsoft.com/services/sql-database-edge/).

> [!IMPORTANT]
> Para entender as diferenças de recursos entre o Azure SQL Database Edge e o SQL Server, bem como as diferenças entre as diferentes opções de Borda do Banco de Dados Do Azure SQL, consulte os [recursos do SQL Database Database Database .](https://azure.microsoft.com/services/sql-database-edge/)

## <a name="streaming-capabilities"></a>Recursos de streaming  

O Azure SQL Database Edge fornece recursos de streaming incorporados para análise sustais em tempo real e processamento complexo de eventos. O recurso de streaming é construído usando os mesmos construtos [do Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) e fornece recursos semelhantes [aos do Azure Stream Analytics no IoT Edge](../stream-analytics/stream-analytics-edge.md).

O motor de streaming do Azure SQL Database Edge foi projetado para baixa latência, resiliência, uso eficiente de largura de banda e conformidade.

## <a name="machine-learning-and-artificial-intelligence-capabilities"></a>Recursos de Machine Learning e Inteligência Artificial

O Azure SQL Database Edge oferece recursos integrados de aprendizado de máquina e análise, integrando o tempo de execução ONNX (Open Neural Network Exchange), que permite a troca de modelos de deep learning e de rede neural entre diferentes frameworks. Para obter mais informações sobre o ONNX, consulte [aqui](https://onnx.ai/). O tempo de execução do ONNX oferece a flexibilidade para desenvolver modelos em uma linguagem ou ferramentas de sua escolha, que podem então ser convertidas para o formato ONNX para execução dentro do SQL Database Edge. Para obter mais informações, consulte [Machine Learning e Inteligência Artificial com ONNX no SQL Database Edge](onnx-overview.md).

## <a name="working-with-azure-sql-database-edge"></a>Trabalhando com a borda do banco de dados SQL do Azure

O Azure SQL Database Edge torna o desenvolvimento e a manutenção de aplicativos mais fáceis e produtivos. Os usuários podem usar todas as ferramentas e habilidades familiares para construir ótimos aplicativos e soluções para suas necessidades de IoT Edge. O usuário pode desenvolver-se no SQL Database Edge usando ferramentas como as seguintes:

- [O portal Azure](https://portal.azure.com/) - um aplicativo baseado na Web para gerenciar todos os serviços do Azure.
- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms/) - Um aplicativo cliente gratuito e para download para gerenciar qualquer infra-estrutura SQL, do SQL Server ao SQL Database.
- [SQL Server Data Tools in Visual Studio](/sql/ssdt/download-sql-server-data-tools-ssdt/) - Um aplicativo cliente gratuito e para download para o desenvolvimento de bancos de dados relacionais do SQL Server, bancos de dados SQL, pacotes de serviços de integração, modelos de dados de serviços de análise e relatórios de serviços de relatórios.
- [Azure Data Studio](/sql/azure-data-studio/what-is/) - Uma ferramenta de banco de dados gratuita, para download e multiplataforma para profissionais de dados usando a família Microsoft de plataformas de dados on-premises e cloud no Windows, MacOS e Linux.
- [Visual Studio Code](https://code.visualstudio.com/docs) - Um editor de código aberto gratuito, para download e código aberto para Windows, macOS e Linux. Ele suporta extensões, incluindo a [extensão mssql](https://aka.ms/mssql-marketplace) para consulta ao Microsoft SQL Server, Azure SQL Database e Azure SQL Data Warehouse.


## <a name="next-steps"></a>Próximas etapas

- Para obter detalhes relacionados a preços e disponibilidade, consulte [Azure SQL Database Edge](https://azure.microsoft.com/services/sql-database-edge/).
- Solicite ativar o Azure SQL Database Edge para sua assinatura.
- Para começar, veja o seguinte:
  - [Implantar a borda do banco de dados SQL através do portal Azure](deploy-portal.md)
  - [Machine Learning e Inteligência Artificial com vantagem de banco de dados SQL](onnx-overview.md)
