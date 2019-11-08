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
ms.date: 03/01/2019
ms.openlocfilehash: a2af1fdd1ee461e3b3db613ff4a575649da2dfdc
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827428"
---
# <a name="azure-sql-database-machine-learning-services-with-r-preview"></a>Banco de dados SQL do Azure Serviços de Machine Learning com R (visualização)

Os Serviços do Machine Learning são um recurso do Banco de Dados SQL do Azure, usado para executar scripts do R no banco de dados. O recurso inclui pacotes R da Microsoft para análise preditiva de alto desempenho e machine learning. Os dados relacionais podem ser usados em scripts R por meio de procedimentos armazenados, do script T-SQL que contém instruções do R ou do código R que contém o T-SQL.

> [!IMPORTANT]
> O banco de dados SQL do Azure Serviços de Machine Learning (com R) está atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> A visualização pública está disponível para bancos de dados individuais e pools elásticos usando o modelo de compra baseado em vCore nas camadas de serviço de **uso geral** e crítico para os **negócios** . Nesta versão prévia pública inicial, não há suporte para a camada de serviço **hiperescala** e para a opção de implantação **instância gerenciada**. Atualmente, R é a única linguagem com suporte. No momento, não há suporte para Python.
>
> A versão prévia está disponível atualmente nas seguintes regiões: Europa Ocidental, Europa Setentrional, oeste dos EUA 2, leste dos EUA, Sul EUA Central, norte EUA Central, Canadá central, Sudeste Asiático, sul da Índia e sudeste da Austrália.
>
> [Inscreva-se na versão prévia](#signup) abaixo.

## <a name="what-you-can-do-with-r"></a>O que você pode fazer com o R

Use o poder da linguagem R para fornecer análise avançada e aprendizado de máquina no banco de dados. Essa capacidade leva os cálculos e o processamento para o local em que os dados residem, eliminando a necessidade de efetuar pull de dados na rede. Além disso, você pode aproveitar o poder dos pacotes de R corporativos para fornecer análises avançadas em escala.

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

Para se inscrever na versão prévia pública, siga estas etapas:

1. Caso não tenha uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/free/) antes de começar.

2. Envie um email para a Microsoft pelo endereço [sqldbml@microsoft.com](mailto:sqldbml@microsoft.com) a fim de se inscrever na versão prévia pública. A versão prévia pública dos Serviços de Machine Learning (com R) no Banco de Dados SQL não está habilitada por padrão.

Quando você estiver inscrito no programa, a Microsoft integrará você à visualização pública e habilitará o R para seu banco de dados novo ou existente.

Serviços de Machine Learning com R não é recomendável para a carga de trabalho de produção durante a visualização pública.

## <a name="next-steps"></a>Próximas etapas

- Consulte as [principais diferenças de SQL Server serviços de Machine Learning](sql-database-machine-learning-services-differences.md).
- Para saber como usar o R para consultar o Serviços de Machine Learning do banco de dados SQL do Azure (versão prévia), consulte o [Guia de início rápido](sql-database-connect-query-r.md).
- Para começar com alguns scripts R simples, consulte [criar e executar scripts r simples no banco de dados SQL do Azure serviços de Machine Learning (versão prévia)](sql-database-quickstart-r-create-script.md).
