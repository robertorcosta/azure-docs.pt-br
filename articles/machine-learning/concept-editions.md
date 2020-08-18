---
title: Azure Machine Learning edições Enterprise e Basic
description: Saiba mais sobre as diferenças entre as edições do Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: j-martens
ms.author: jmartens
ms.date: 06/11/2020
ms.openlocfilehash: fbe3f0109ca3af2cf9ff44061c7882e6bd72c8a6
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88508595"
---
# <a name="enterprise-preview-and-basic-editions-of-azure-machine-learning"></a>Enterprise (visualização) e edições básicas do Azure Machine Learning 

O Azure Machine Learning oferece duas edições personalizadas para suas necessidades de aprendizado de máquina. Essas edições determinam quais ferramentas de aprendizado de máquina estão disponíveis para desenvolvedores e cientistas de dados no workspace deles.

## <a name="choose-an-edition"></a>Escolher uma edição

Você atribui a edição sempre que cria um workspace. Os clientes são responsáveis pelos custos relacionados a computação e a outros recursos do Azure durante esse período. Saiba como [gerenciar custos para Azure Machine Learning](concept-plan-manage-cost.md).

Saiba como [atualizar um espaço de trabalho básico para a edição Enterprise (versão prévia)](how-to-manage-workspace.md#upgrade). 

## <a name="whats-in-each-edition"></a>O que há em cada edição

### <a name="data-for-machine-learning-capabilities"></a>Dados para recursos de Machine Learning  

| Funcionalidades                     | Edition                 |
|------------------------------------------------------------------------------------|:-----------:|
| Rotulagem: [criar e gerenciar projetos de rotulagem](tutorial-labeling.md) no Studio (Web)                                                | Tudo                     |
| Rotulagem: Labeler no estúdio (Web)                                    | Tudo                     |
| Rotulagem: usar força de funcionários privada                               | Tudo                     |
| Rotulagem: [classificação de imagem assistida por ml e detecção de objeto](how-to-label-images.md)                  | Somente Enterprise Edition |
| Conjuntos de itens + repositórios de armazenamento: criar e gerenciar em Python                       | Tudo                     |
| Conjuntos de itens + repositórios de armazenamento: criar e gerenciar no Studio (Web)                         | Tudo                     |
| Descompasso: exibir e gerenciar monitores de DataSet em Python                           | Tudo                     |
| Descompasso: exibir e gerenciar monitores de DataSet no Studio (Web)                            | Somente Enterprise Edition |


<br/>
<br/>

### <a name="automated-training-capabilities-automl"></a>Recursos de treinamento automatizado (AutoML)

| Funcionalidades    | Edition                 |
|------------------------------------------------------------------------------------|:-----------:|
| Criar e executar [experimentos do AutoML em blocos de anotações](how-to-configure-auto-train.md)               | Tudo                     |
| Criar e executar  [experimentos do AutoML no Studio (Web)](how-to-use-automated-ml-for-ml-models.md)   | Somente Enterprise Edition |
| Recursos de previsão de AutoML líderes do setor             | Somente Enterprise Edition |
| Suporte para aprendizado profundo e outros aprendizes avançados | Somente Enterprise Edition |
| A classificação de suporte a dados grandes e as tarefas de regressão (até 100 GB)                     | Somente Enterprise Edition |


<br/>
<br/>

### <a name="responsible-machine-learning"></a>Machine Learning responsável

| Funcionalidades    | Edition                 |
|------------------------------------------------------------------------------------|:-----------:|
| [Explicabilidade do modelo](how-to-machine-learning-interpretability-automl.md)                                              | Tudo                     |
| [Privacidade diferencial](how-to-differential-privacy.md)                          | Tudo                     |
| Marcas personalizadas para implementar folhas de os    | Tudo                     |
| Integração do AzureML de imparcialidade                                      | Tudo                     |

<br/>
<br/>


### <a name="build-and-train-capabilities"></a>Recursos de desenvolvimento e treinamento

| Funcionalidades    | Edition                 |
|------------------------------------------------------------------------------------|:-----------:|
| Integração do Visual Studio Code                                                     | Tudo                     |
| Aprendizado de reforço                                                             | Tudo                     |
| IU de experimentação                                                                 | Tudo                     |
| Integração do Jupyter, JupyterLab                                                    | Tudo                     |
| Suporte ao SDK do Python                                                                 | Tudo                     |
| Suporte do SDK do R                                                                      | Tudo                     |
| Pipelines de ML: criar, executar e publicar em Python                           | Tudo                     |
| Pipelines de ML: criar, editar e excluir execuções agendadas de pipelines no Python| Tudo                     |
| Pipelines de ML: criar pontos de extremidade de pipeline no Python SDK                                   | Tudo                     |
| Pipelines de ML: Exibir detalhes da execução no estúdio (Web)                                              | Tudo                     |
| Pipelines de ML: criar, executar, Visualizar e publicar no designer                  | Somente Enterprise Edition |
| Pipelines de ML: criar pontos de extremidade de pipeline no designer | Somente Enterprise Edition |
| Instâncias de computação gerenciadas para blocos de anotações integrados                                 | Tudo                     |


<br/>
<br/>

### <a name="deployment-and-model-management-capabilities"></a>Recursos de implantação e gerenciamento de modelos

| Funcionalidades                            | Edition                 |
|------------------------------------------------------------------------------------|:-----------:|
| A extensão DevOps do Azure para Machine Learning e o CLI do Azure ML                 | Tudo                     |
| [Integração da Grade de Eventos](how-to-use-event-grid.md)                                                             | Tudo                     |
| Integrar o Azure Stream Analytics com o Azure Machine Learning                       | Tudo                     |
| Criar pipelines de ML no SDK                                                         | Tudo                     |
| Inferência do lote                                                                  | Tudo                     |
| Modelos de Aceleração de Hardware baseado em FPGA                                             | Tudo                     |
| Criação de perfil de modelo                                                                    | Tudo                     |

<br/>
<br/>

### <a name="security-governance-and-control-capabilities"></a>Recursos de segurança, governança e controle

| Funcionalidades     | Edition                 |
|------------------------------------------------------------------------------------|:-----------:|
| Suporte ao [controle de acesso baseado em função do Azure (RBAC do Azure)](how-to-assign-roles.md)                                           | Tudo                     |
| Suporte à [VNet (rede virtual)](how-to-enable-virtual-network.md) para computação                                         | Tudo                     |
| Autenticação de ponto de extremidade de Pontuação                                                    | Tudo                     |
| [Link privado do espaço de trabalho](how-to-configure-private-link.md)                                                            | Tudo                     |
| [Gerenciamento de cotas](how-to-manage-quotas.md) entre espaços de trabalho                                                 | Somente Enterprise Edition |

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o que está disponível na [visão geral do Azure Machine Learning Edition e na página de preços](https://azure.microsoft.com/pricing/details/machine-learning/). 

Saiba como [atualizar um workspace de Básico para Enterprise](how-to-manage-workspace.md#upgrade). 
