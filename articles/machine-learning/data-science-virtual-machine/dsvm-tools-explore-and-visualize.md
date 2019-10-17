---
title: Ferramentas de visualização e exploração de dados
titleSuffix: Azure Data Science Virtual Machine
description: Ferramentas de exploração e visualização de dados para a Máquina Virtual de Ciência de Dados.
keywords: ferramentas de ciência de dados, máquina virtual de ciência de dados, ferramentas para ciência de dados, ciência de dados do linux
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: conceptual
ms.date: 10/11/2019
ms.openlocfilehash: a18c374df0cad7994af855089f1096d52fe30ce2
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72330696"
---
# <a name="data-exploration-and-visualization-tools-on-the-azure-data-science-virtual-machine"></a>Ferramentas de exploração e visualização de dados no Máquina Virtual de Ciência de Dados do Azure

Na ciência de dados, a chave é entender os dados. Ferramentas de visualização e exploração de dados ajudam a acelerar a compreensão dos dados. As ferramentas a seguir, que são fornecidas no Máquina Virtual de Ciência de Dados (DSVM), facilitam a etapa dessa chave.

## <a name="apache-drill"></a>Análise do Apache
|    |           |
| ------------- | ------------- |
| O que é?   | Mecanismo de consulta SQL de código aberto no Big Data    |
| Versões do DSVM com suporte      | Windows, Linux  |
| Como ele é configurado e instalado no DSVM?      |  Instalado em `/dsvm/tools/drill*` somente no modo inserido   |
| Usos típicos      |  Para a exploração de dados in-loco sem a necessidade de extração, transformação, carregamento (ETL). Consulte diferentes fontes de dados e formatos, incluindo CSV, JSON, tabelas relacionais e Hadoop.     |
| Como usá-lo e executá-lo      | Atalho da área de trabalho  <br/> [Introdução a Análise em 10 minutos](https://drill.apache.org/docs/drill-in-10-minutes/)  |
| Ferramentas relacionadas no DSVM      |   Rattle, Weka, SQL Server Management Studio      |

## <a name="power-bi-desktop"></a>Power BI Desktop 
|    |           |
| ------------- | ------------- |
| O que é?   | Ferramenta de BI e visualização de dados interativa    |
| Versões do DSVM com suporte      | Windows  |
| Usos típicos      |  Visualização de dados e criação de painéis   |
| Como usá-lo e executá-lo      | Atalho da área de trabalho (`C:\Program Files\Microsoft Power BI Desktop\bin\PBIDesktop.exe`) ou simplesmente execute no menu **Iniciar** .      |
| Ferramentas relacionadas no DSVM      |   Visual Studio 2019, Visual Studio Code, Juno      |

> [!NOTE]
> É necessária uma conta do Microsoft Office 365 para acessar o Power BI.


## <a name="rattle"></a>Rattle
|    |           |
| ------------- | ------------- |
| O que é?   |   Uma GUI (interface gráfica do usuário) para Data Mining usando R   |
| Edições do DSVM com suporte     | Windows, Linux     |
| Usos típicos      | Ferramenta de Mineração de Dados da Interface do Usuário Geral para R    |
| Como usá-lo e executá-lo      | Ferramenta da interface do usuário. No Windows, abra um prompt de comando, execute R e, dentro do R, execute `rattle()`. No Linux, conecte-se com o X2Go, inicie um terminal, execute R e, em R, execute `rattle()`. |
| Links para exemplos      | [Rattle](https://togaware.com/onepager/) |
| Ferramentas relacionadas no DSVM      |LightGBM, Weka, Xgboost   |


## <a name="weka"></a>Weka
|    |           |
| ------------- | ------------- |
| O que é?   |  Uma coleção de algoritmos de aprendizado de máquina para tarefas de Data Mining. Esses algoritmos podem ser aplicados diretamente a um DataSet ou chamados de seu próprio código Java. Weka contém ferramentas para pré-processamento de dados, classificação, regressão, clustering, regras de associação e visualização. |
| Edições do DSVM com suporte     | Windows, Linux     |
| Usos típicos      | Ferramenta de aprendizado de máquina geral     |
| Como usá-lo e executá-lo      | No Windows, pesquise Weka no menu Iniciar. No Linux, entre com X2Go e vá para aplicativos > desenvolvimento > weka. |
| Links para exemplos      | [Exemplos de Weka](https://www.cs.waikato.ac.nz/ml/weka/documentation.html) |
| Ferramentas relacionadas no DSVM      |LightGBM, Rattle, Xgboost   |




