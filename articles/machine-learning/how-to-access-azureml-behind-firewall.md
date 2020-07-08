---
title: Usar um firewall
titleSuffix: Azure Machine Learning
description: Controle o acesso a espaços de trabalho do Azure Machine Learning com firewalls do Azure. Saiba mais sobre os hosts que você deve permitir por meio do firewall para que Azure Machine Learning funcionem corretamente.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 04/27/2020
ms.custom: tracking-python
ms.openlocfilehash: 31daec93352c0e142075a55c61f2b8d3a6d56fab
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85080241"
---
# <a name="use-workspace-behind-azure-firewall-for-azure-machine-learning"></a>Usar o espaço de trabalho por trás do firewall do Azure para Azure Machine Learning

Neste artigo, saiba como configurar o Firewall do Azure para uso com um espaço de trabalho Azure Machine Learning.

O Firewall do Azure pode ser usado para controlar o acesso ao seu espaço de trabalho do Azure Machine Learning e à Internet pública. Se não estiver configurado corretamente, o firewall poderá causar problemas ao usar seu espaço de trabalho. Há uma variedade de nomes de host que são usados pelo espaço de trabalho Azure Machine Learning, que são descritos neste artigo.

## <a name="network-rules"></a>Regras de rede

No firewall, crie uma regra de rede permitindo o tráfego de e para os endereços neste artigo.

> [!TIP]
> Ao adicionar a regra de rede, defina o __protocolo__ como qualquer e as portas para `*` .
>
> Para obter mais informações sobre como configurar o Firewall do Azure, consulte [implantar e configurar o Firewall do Azure](../firewall/tutorial-firewall-deploy-portal.md#configure-a-network-rule).

## <a name="microsoft-hosts"></a>Hosts da Microsoft

Os hosts nesta seção são de propriedade da Microsoft e fornecem os serviços necessários para o funcionamento adequado do seu espaço de trabalho.

| **Nome do host** | **Finalidade** |
| ---- | ---- |
| **\*. batchai.core.windows.net** | Clusters de treinamento |
| **ml.azure.com** | Azure Machine Learning Studio |
| **default.exp-tas.com** | Usado pelo Azure Machine Learning Studio |
| **\*. azureml.ms** | Usado por APIs de Azure Machine Learning |
| **\*. experiments.azureml.net** | Usado por experimentos em execução no Azure Machine Learning |
| **\*. modelmanagement.azureml.net** | Usado para registrar e implantar modelos|
| **mlworkspace.azure.ai** | Usado pelo portal do Azure ao exibir um espaço de trabalho |
| **\*. aether.ms** | Usado ao executar pipelines de Azure Machine Learning |
| **\*. instances.azureml.net** | Azure Machine Learning instâncias de computação |
| **\*. instances.azureml.ms** | Azure Machine Learning instâncias de computação quando o espaço de trabalho tem o link privado habilitado |
| **windows.net** | Armazenamento do Blobs do Azure |
| **vault.azure.net** | Cofre de Chave do Azure |
| **azurecr.io** | Registro de Contêiner do Azure |
| **mcr.microsoft.com** | Registro de contêiner da Microsoft para imagens base do Docker |

## <a name="python-hosts"></a>Hosts do Python

Os hosts nesta seção são usados para instalar pacotes do Python. Eles são necessários durante o desenvolvimento, o treinamento e a implantação. 

| **Nome do host** | **Finalidade** |
| ---- | ---- |
| **anaconda.com** | Usado ao instalar pacotes do Conda |
| **pypi.org** | Usado ao instalar pacotes Pip |

## <a name="r-hosts"></a>Hosts de R

Os hosts nesta seção são usados para instalar pacotes do R. Eles são necessários durante o desenvolvimento, o treinamento e a implantação.

> [!IMPORTANT]
> Internamente, o SDK do R para Azure Machine Learning usa pacotes do Python. Portanto, você também deve permitir hosts Python por meio do firewall.

| **Nome do host** | **Finalidade** |
| ---- | ---- |
| **cloud.r-project.org** | Usado ao instalar pacotes do CRAN. |

Próximas etapas

* [[Implantar e configurar o Firewall do Azure](../firewall/tutorial-firewall-deploy-portal.md)]
* [Proteger trabalhos de experimentação e de inferência do Azure ML em uma rede virtual do Azure](how-to-enable-virtual-network.md)
