---
title: Usar um firewall
titleSuffix: Azure Machine Learning
description: Controle o acesso a espaços de trabalho do Azure Machine Learning com firewalls do Azure. Saiba mais sobre os hosts que você deve permitir por meio do firewall para que Azure Machine Learning funcionem corretamente.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 07/17/2020
ms.custom: how-to, tracking-python
ms.openlocfilehash: 23ec12daa2e5c236da482615228b7c44037282fb
ms.sourcegitcommit: 85eb6e79599a78573db2082fe6f3beee497ad316
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/05/2020
ms.locfileid: "87808109"
---
# <a name="use-workspace-behind-a-firewall-for-azure-machine-learning"></a>Usar o espaço de trabalho por trás de um firewall para Azure Machine Learning

Neste artigo, saiba como configurar o Firewall do Azure para uso com um espaço de trabalho Azure Machine Learning.

> [!IMPORTANT]
> Embora as informações neste documento se baseiam no uso do firewall do Azure, você deve ser capaz de usá-lo com outros produtos de firewall. Se você tiver dúvidas sobre como permitir a comunicação por meio do firewall, consulte a documentação do firewall que você está usando.

O Firewall do Azure pode ser usado para controlar o acesso ao seu espaço de trabalho do Azure Machine Learning e à Internet pública. Se não estiver configurado corretamente, o firewall poderá causar problemas ao usar seu espaço de trabalho. Há uma variedade de nomes de host que são usados pelo espaço de trabalho Azure Machine Learning, que são descritos neste artigo.

## <a name="network-rules"></a>Regras de rede

No firewall, crie uma regra de rede permitindo o tráfego de e para os endereços neste artigo.

> [!TIP]
> Ao adicionar a regra de rede, defina o __protocolo__ como qualquer e as portas para `*` .
>
> Para obter mais informações sobre como configurar o Firewall do Azure, consulte [implantar e configurar o Firewall do Azure](../firewall/tutorial-firewall-deploy-portal.md#configure-an-application-rule).

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
| **your-acr-server-name.azurecr.io** | Necessário somente se o registro de contêiner do Azure estiver por trás da rede virtual. Nessa configuração, um link privado é criado do ambiente da Microsoft para a instância do ACR em sua assinatura. Use o nome do servidor ACR para seu espaço de trabalho Azure Machine Learning. |
| **\*. notebooks.azure.net** | Necessário para os notebooks no Azure Machine Learning Studio. |
## <a name="python-hosts"></a>Hosts do Python

Os hosts nesta seção são usados para instalar pacotes do Python. Eles são necessários durante o desenvolvimento, o treinamento e a implantação. 

| **Nome do host** | **Finalidade** |
| ---- | ---- |
| **anaconda.com** | Usado para instalar pacotes padrão. |
| **\*. anaconda.org** | Usado para obter dados do repositório. |
| **pypi.org** | Usado para listar as dependências do índice padrão, se houver, e o índice não é substituído pelas configurações do usuário. Se o índice for substituído, você também deverá permitir ** \* . pythonhosted.org**. |

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
