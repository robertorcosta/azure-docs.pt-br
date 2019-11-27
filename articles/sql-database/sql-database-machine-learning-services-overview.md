---
title: Serviços de Machine Learning com R (visualização)
description: Este artigo descreve o Serviços de Machine Learning do banco de dados SQL do Azure (com R) e explica como ele funciona.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dphansen
ms.author: davidph
ms.reviewer: carlrab
manager: cgronlun
ms.date: 11/20/2019
ms.openlocfilehash: ca223de2bc0b26e4968d400ea418761a399dacae
ms.sourcegitcommit: 95931aa19a9a2f208dedc9733b22c4cdff38addc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2019
ms.locfileid: "74462343"
---
# <a name="azure-sql-database-machine-learning-services-with-r-preview"></a>Banco de dados SQL do Azure Serviços de Machine Learning com R (visualização)

Os Serviços do Machine Learning são um recurso do Banco de Dados SQL do Azure, usado para executar scripts do R no banco de dados. O recurso inclui pacotes R da Microsoft para análise preditiva de alto desempenho e machine learning. Os dados relacionais podem ser usados em scripts R por meio de procedimentos armazenados, do script T-SQL que contém instruções do R ou do código R que contém o T-SQL.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

> [!NOTE]
> A versão prévia está disponível para bancos de dados individuais e pools elásticos usando o modelo de compra baseado em vCore nas camadas de serviço de **uso geral** e crítico para os **negócios** . Nesta visualização inicial, não há suporte para a camada de serviço de **hiperescala** e para a opção de implantação de **instância gerenciada** . Atualmente, R é a única linguagem com suporte. No momento, não há suporte para Python.
>
> A versão prévia está disponível atualmente nas seguintes regiões: Europa Ocidental, Europa Setentrional, oeste dos EUA 2, leste dos EUA, Sul EUA Central, norte EUA Central, Canadá central, Sudeste Asiático, sul da Índia e sudeste da Austrália.

## <a name="what-you-can-do-with-r"></a>O que você pode fazer com o R

Use o poder da linguagem R para fornecer análise avançada e machine learning no banco de dados. Essa capacidade leva os cálculos e o processamento para o local em que os dados residem, eliminando a necessidade de efetuar pull de dados na rede. Além disso, você pode aproveitar o poder dos pacotes de R corporativos para fornecer análises avançadas em escala.

Os Serviços do Machine Learning incluem uma distribuição base do R, sobreposta com pacotes empresariais do R da Microsoft. As funções e os algoritmos do R da Microsoft foram desenvolvidos para escala e utilidade, fornecendo análise preditiva, modelagem estatística, visualizações de dados e algoritmos de machine learning de ponta.

### <a name="r-packages"></a>Pacotes R

Os pacotes de R de software livre mais comuns são pré-instalados em Serviços de Machine Learning. Os seguintes pacotes R da Microsoft também estão incluídos:

| Pacote R | DESCRIÇÃO|
|-|-|
| [Microsoft R Open](https://mran.microsoft.com/rro) | O Microsoft R Open é a distribuição aprimorada de R da Microsoft. É uma plataforma de software livre completa para análise estatística e ciência de dados. É baseada no R e totalmente compatível com ele, além de incluir outras funcionalidades para um melhor desempenho e capacidade de reprodução. |
| [RevoScaleR](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-revoscaler) | O RevoScaleR é a biblioteca principal para o R escalonável. As funções nessa biblioteca estão entre mais amplamente usadas. As transformações e a manipulação de dados, resumo estatístico, visualização e muitas formas de modelagem e de análises são encontrados nessas bibliotecas. Além disso, as funções nessas bibliotecas distribuem automaticamente as cargas de trabalho entre os núcleos disponíveis para processamento paralelo, com a capacidade de trabalhar em partes de dados coordenadas e gerenciadas pelo mecanismo de cálculo. |
| [MicrosoftML (R)](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-microsoftml) | O MicrosoftML adiciona algoritmos de machine learning para criar modelos personalizados para análise de texto, análise de imagem e de sentimento. |

Além dos pacotes pré-instalados, você pode [instalar pacotes adicionais](sql-database-machine-learning-services-add-r-packages.md).

<a name="signup"></a>

## <a name="sign-up-for-the-preview"></a>Inscrever-se para obter a visualização

> [!IMPORTANT]
> Inscreva-se no Serviços de Machine Learning do banco de dados SQL do Azure (versão prévia) está fechado no momento.

Serviços de Machine Learning com R não é recomendável para a carga de trabalho de produção durante a visualização.

## <a name="next-steps"></a>Próximas etapas

- Consulte as [principais diferenças de SQL Server serviços de Machine Learning](sql-database-machine-learning-services-differences.md).
- Para saber como usar o R para consultar o Serviços de Machine Learning do banco de dados SQL do Azure (versão prévia), consulte o [Guia de início rápido](sql-database-connect-query-r.md).
- Para começar com alguns scripts R simples, consulte [criar e executar scripts r simples no banco de dados SQL do Azure serviços de Machine Learning (versão prévia)](sql-database-quickstart-r-create-script.md).
