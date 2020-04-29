---
title: O que é a borda do banco de dados SQL do Azure? | Microsoft Docs
description: Saiba mais sobre a borda do banco de dados SQL do Azure
keywords: Introdução à borda do banco de dados SQL, o que é borda do banco de dados SQL, visão geral do SQL Database Edge
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 03/24/2020
ms.openlocfilehash: d5c48b6036065f6182912c21c144cab80fc3cfbf
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80246697"
---
# <a name="what-is-azure-sql-database-edge-preview"></a>O que é a visualização de borda do banco de dados SQL do Azure?

A visualização de borda do banco de dados SQL do Azure é um mecanismo de banco de dados relacional otimizado para implantações de IoT e IoT Edge. Ele fornece recursos para criar uma camada de processamento e armazenamento de dados de alto desempenho para aplicativos e soluções de IoT. A borda do banco de dados SQL do Azure fornece recursos para transmitir, processar e analisar relacionais e não relacionais, como JSON, grafo e dados de série temporal, o que o torna a escolha certa para uma variedade de aplicativos IoT modernos.

A borda do banco de dados SQL do Azure se baseia nas versões mais recentes do [mecanismo de banco de dados do Microsoft SQL Server](/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database-edge/toc.json), que fornece recursos de desempenho, segurança e processamento de consulta líderes do setor. Desde que o Azure SQL Database Edge se baseia no mesmo mecanismo que SQL Server e no banco de dados SQL do Azure, ele fornece a mesma área de superfície de programação T-SQL que torna o desenvolvimento de aplicativos ou soluções mais fácil e rápido e, ao mesmo tempo, torna a portabilidade do aplicativo entre dispositivos IoT Edge, data centers e a nuvem diretamente em frente.

## <a name="deployment-models"></a>Modelos de implantação

A borda do banco de dados SQL do Azure está disponível no Azure Marketplace e pode ser implantada como um módulo para [Azure IOT Edge](../iot-edge/about-iot-edge.md). Para obter mais informações, consulte [implantar a borda do banco de dados SQL do Azure](deploy-portal.md).<br>

![Diagrama de visão geral da borda do banco de dados SQL](media/overview/overview.png)

## <a name="editions-of-sql-database-edge"></a>Edições do Edge do banco de dados SQL

A borda do banco de dados SQL está disponível com três diferentes edições ou planos de software. Essas edições têm conjuntos de recursos idênticos e diferem apenas em termos de seus direitos de uso e da quantidade de CPU/memória que eles dão suporte.

   |**Plano**  |**Descrição**  |
   |---------|---------|
   |Desenvolvedor de borda do banco de dados SQL do Azure  |  SKU somente de desenvolvimento, cada contêiner de borda do banco de dados SQL é limitado a até 4 núcleos e 32 GB de memória  |
   |Banco de Dados SQL do Azure no Edge    |  SKU de produção, cada contêiner de borda do banco de dados SQL é limitado a até 8 núcleos e 64 GB de memória. |

## <a name="pricing-and-availability"></a>Preços e disponibilidade

O banco de dados SQL do Azure está atualmente em visualização. Para obter mais informações sobre os preços e a disponibilidade, consulte [borda do banco de dados SQL do Azure](https://azure.microsoft.com/services/sql-database-edge/).

> [!IMPORTANT]
> Para entender as diferenças de recursos entre o Edge e o SQL Server do banco de dados SQL do Azure, bem como as diferenças entre diferentes opções de borda do banco de dados SQL do Azure, consulte recursos do banco de dados SQL do [banco de dados](https://azure.microsoft.com/services/sql-database-edge/)

## <a name="streaming-capabilities"></a>Recursos de streaming  

A borda do banco de dados SQL do Azure fornece recursos de streaming internos para análise em tempo real e processamento de eventos complexos. O recurso de streaming é criado usando as mesmas construções que [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) e fornece recursos semelhantes aos [Azure Stream Analytics no IOT Edge](../stream-analytics/stream-analytics-edge.md).

O mecanismo de streaming para a borda do banco de dados SQL do Azure foi projetado para baixa latência, resiliência, uso eficiente de largura de banda e conformidade.

## <a name="machine-learning-and-artificial-intelligence-capabilities"></a>Recursos de inteligência artificial e Machine Learning

A borda do banco de dados SQL do Azure fornece recursos internos de aprendizado de máquina e análise, integrando o tempo de execução do Open Format ONNX (Open Neural Network Exchange), que permite o intercâmbio de modelos de rede neural e de aprendizado profundo entre diferentes estruturas. Para obter mais informações sobre o ONNX, consulte [aqui](https://onnx.ai/). O tempo de execução do ONNX fornece a flexibilidade para desenvolver modelos em uma linguagem ou ferramentas de sua escolha, que podem então ser convertidas para o formato ONNX para execução na borda do banco de dados SQL. Para obter mais informações, consulte [Machine Learning e inteligência artificial com ONNX na borda do banco de dados SQL](onnx-overview.md).

## <a name="working-with-azure-sql-database-edge"></a>Trabalhando com a borda do banco de dados SQL do Azure

A borda do banco de dados SQL do Azure torna o desenvolvimento e a manutenção de aplicativos mais fáceis e produtivos. Os usuários podem usar todas as ferramentas e habilidades conhecidas para criar ótimos aplicativos e soluções para suas necessidades de IoT Edge. O usuário pode desenvolver na borda do banco de dados SQL usando ferramentas como a seguinte:

- [O portal do Azure](https://portal.azure.com/) -um aplicativo baseado na Web para gerenciar todos os serviços do Azure.
- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms/) -um aplicativo cliente gratuito e que pode ser baixado para gerenciar qualquer infraestrutura do SQL, de SQL Server para o banco de dados SQL.
- [SQL Server Data Tools no Visual Studio](/sql/ssdt/download-sql-server-data-tools-ssdt/) – um aplicativo cliente gratuito e que pode ser baixado para o desenvolvimento de SQL Server bancos de dados relacionais, bancos de dados SQL, pacotes de Integration Services, modelos de Analysis Services e relatórios de Reporting Services.
- [Azure Data Studio](/sql/azure-data-studio/what-is/) -uma ferramenta de banco de dados de plataforma cruzada gratuita, que pode ser baixada para o data Professional, usando a família de plataformas de dados locais e na nuvem da Microsoft no Windows, no MacOS e no Linux.
- [Visual Studio Code](https://code.visualstudio.com/docs) -um editor de código aberto gratuito, que pode ser baixado para Windows, MacOS e Linux. Ele dá suporte a extensões, incluindo a [extensão MSSQL](https://aka.ms/mssql-marketplace) para consultar Microsoft SQL Server, banco de dados SQL do azure e SQL data warehouse do Azure.


## <a name="next-steps"></a>Próximas etapas

- Para obter os preços e os detalhes relacionados à disponibilidade, consulte [borda do banco de dados SQL do Azure](https://azure.microsoft.com/services/sql-database-edge/).
- Solicitação para habilitar a borda do banco de dados SQL do Azure para sua assinatura.
- Para começar, consulte o seguinte:
  - [Implantar a borda do banco de dados SQL por meio do portal do Azure](deploy-portal.md)
  - [Machine Learning e inteligência artificial com a borda do banco de dados SQL](onnx-overview.md)
