---
title: Solucionar problemas de imagens de ambiente
titleSuffix: Azure Machine Learning
description: Saiba como solucionar problemas com Builds de imagem de ambiente e instalações de pacote.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: saachigopal
ms.author: sagopal
ms.date: 12/3/2020
ms.topic: troubleshooting
ms.custom: devx-track-python
ms.openlocfilehash: b452c24b4b2ed6021910f267b9941f3829acccd8
ms.sourcegitcommit: a89a517622a3886b3a44ed42839d41a301c786e0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/22/2020
ms.locfileid: "97733264"
---
# <a name="troubleshoot-environment-image-builds"></a>Solucionar problemas de compilação de imagem de ambiente
Saiba como solucionar problemas com Builds de imagem de ambiente do Docker e instalações de pacote.

## <a name="prerequisites"></a>Pré-requisitos

* Uma **assinatura do Azure**. Experimente a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree).
* O [SDK do Azure Machine Learning](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py).
* O [CLI do Azure](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest).
* A [Extensão da CLI do Azure Machine Learning](reference-azure-machine-learning-cli.md).
* Para depurar localmente, você deve ter uma instalação do Docker em funcionamento no sistema local.

## <a name="docker-image-build-failures"></a>Falhas de Build de imagem do Docker
 
Para a maioria das falhas de compilação de imagem, a causa raiz pode ser encontrada no log de compilação de imagem.
Você pode encontrar o log de compilação de imagem no portal de Azure Machine Learning (20 \_ \_log.txt de compilação de imagem \_ ) ou em suas tarefas de ACR executa logs
 
Na maioria dos casos, é mais fácil reproduzir erros localmente. Verifique o tipo de erro e tente um dos seguintes `setuptools` :

- Instalar dependência do Conda localmente `conda install suspicious-dependency==X.Y.Z`
- Instalar dependência Pip localmente `pip install suspicious-dependency==X.Y.Z`
- Tente materializar todo o ambiente `conda create -f conda-specification.yml`

> [!IMPORTANT]
> Verifique se a plataforma e o intérprete em seu computador local correspondem aos do remoto. 

### <a name="timeout"></a>Tempo limite 
 
Problemas de tempo limite podem ocorrer para vários problemas de rede:
- Baixa largura de banda da Internet
- Problemas do servidor
- Grande dependência que não pode ser baixada com as configurações de tempo limite de Conda ou Pip fornecidas
 
Mensagens semelhantes às mostradas abaixo indicarão o problema:
 
```
('Connection broken: OSError("(104, \'ECONNRESET\')")', OSError("(104, 'ECONNRESET')"))
```
```
ReadTimeoutError("HTTPSConnectionPool(host='****', port=443): Read timed out. (read timeout=15)",)
```

Soluções possíveis:
 
- Tente uma fonte diferente para a dependência, se disponível, como espelhos, armazenamento de BLOBs ou outros feeds do Python.
- Atualize Conda ou Pip. Se um arquivo do Docker personalizado for usado, atualize as configurações de tempo limite.
- Algumas versões do PIP têm problemas conhecidos. Considere adicionar uma versão específica do Pip em dependências de ambiente.

### <a name="package-not-found"></a>Pacote não encontrado

Esse é o caso mais comum para falhas de compilação de imagem.

- O pacote Conda não pôde ser encontrado
        ```
        ResolvePackageNotFound: 
          - not-existing-conda-package
        ```

- O pacote Pip especificado ou a versão não foi encontrada
        ```
        ERROR: Could not find a version that satisfies the requirement invalid-pip-package (from versions: none)
        ERROR: No matching distribution found for invalid-pip-package
        ```

- Dependência de Pip aninhada inadequada
        ```
        ERROR: No matching distribution found for bad-backage==0.0 (from good-package==1.0)
        ```

Verifique se o pacote existe nas fontes especificadas. Use a [pesquisa de Pip](https://pip.pypa.io/en/stable/reference/pip_search/) para verificar as dependências de Pip.

`pip search azureml-core`

Para dependências do Conda, use a [pesquisa do Conda](https://docs.conda.io/projects/conda/en/latest/commands/search.html).

`conda search conda-forge::numpy`

Para obter mais opções:
- `pip search -h`
- `conda search -h`

#### <a name="installer-notes"></a>Notas do instalador

Verifique se a distribuição necessária existe para a plataforma especificada e a versão do intérprete do Python.

Para dependências Pip, navegue até `https://pypi.org/project/[PROJECT NAME]/[VERSION]/#files` para ver se a versão necessária está disponível. Por exemplo, https://pypi.org/project/azureml-core/1.11.0/#files

Para dependências do Conda, verifique o pacote no repositório do canal.
Para os canais mantidos pela Anaconda, Inc., marque [aqui](https://repo.anaconda.com/pkgs/).

### <a name="pip-package-update"></a>Atualização do pacote Pip

Durante a instalação ou atualização de um pacote Pip, o resolvedor pode precisar atualizar um pacote já instalado para atender aos novos requisitos.
A desinstalação pode falhar por vários motivos relacionados à versão do PIP ou à maneira como a dependência foi instalada.
O cenário mais comum é que uma dependência instalada pelo Conda não pôde ser desinstalada por Pip.
Para este cenário, considere desinstalar a dependência usando `conda remove mypackage` .

```
  Attempting uninstall: mypackage
    Found existing installation: mypackage X.Y.Z
ERROR: Cannot uninstall 'mypackage'. It is a distutils installed project and thus we cannot accurately determine which files belong to it which would lead to only a partial uninstall.
``` 
### <a name="installer-issues"></a>Problemas do instalador

Certas versões do instalador têm problemas nos resolvedores de pacotes que podem levar a uma falha de compilação.

Se uma imagem base personalizada ou dockerfile for usada, recomendamos o uso do Conda versão 4.5.4 ou superior.

O pacote Pip é necessário para instalar dependências de Pip e se uma versão não for especificada no ambiente, a versão mais recente será usada.
É recomendável usar uma versão conhecida do Pip para evitar problemas transitórios ou alterações significativas que podem ser causadas pela versão mais recente da ferramenta.

Considere fixar a versão de Pip em seu ambiente se você vir qualquer uma das mensagens abaixo:

`Warning: you have pip-installed dependencies in your environment file, but you do not list pip itself as one of your conda dependencies. Conda may not use the correct pip to install your packages, and they may end up in the wrong place. Please add an explicit pip dependency. I'm adding one for you, but still nagging you.`

`Pip subprocess error:
ERROR: THESE PACKAGES DO NOT MATCH THE HASHES FROM THE REQUIREMENTS FILE. If you have updated the package versions, update the hashes as well. Otherwise, examine the package contents carefully; someone may have tampered with them.`

Além disso, a instalação do Pip pode ser paralisada em um loop infinito se houver conflitos não resolvidos nas dependências. Se estiver trabalhando localmente, faça downgrade da versão Pip para < 20,3. Em um ambiente Conda criado a partir de um arquivo YAML, esse problema só será visto se Conda-forjar for o canal de prioridade mais alta. Para atenuar o problema, especifique explicitamente Pip < 20,3 (! = 20,3 ou = 20.2.4 pin para outra versão) como uma dependência Conda no arquivo de especificação Conda.

## <a name="service-side-failures"></a>Falhas do lado do serviço

### <a name="unable-to-pull-image-from-mcraddress-could-not-be-resolved-for-container-registry"></a>Não foi possível resolver a imagem de MCR/address para o registro de contêiner.
Possíveis problemas:
- O nome do caminho para o registro de contêiner não pode ser resolvido corretamente. Verifique se os nomes de imagem usam barras duplas e se a direção das barras nos hosts Linux e Windows está correta.
- Se o ACR por trás de uma vnet estiver usando um ponto de extremidade privado em [uma região sem suporte](https://docs.microsoft.com/azure/private-link/private-link-overview#availability), configure o ACR por trás de uma vnet usando o ponto de extremidade de serviço (acesso público) do portal e tente novamente.
- Depois de colocar o ACR por trás de uma VNet, verifique se o [modelo ARM](https://docs.microsoft.com/azure/machine-learning/how-to-enable-virtual-network#azure-container-registry) é executado. Isso permite que o espaço de trabalho se comunique com a instância do ACR.

### <a name="401-error-from-workspace-acr"></a>erro 401 do ACR do espaço de trabalho
Ressincronizar chaves de armazenamento usando [WS.sync_keys ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#sync-keys--)

### <a name="environment-keeps-throwing-waiting-for-other-conda-operations-to-finish-error"></a>O ambiente mantém o lançamento "aguardando a conclusão de outras operações de Conda..." Ao
Quando um Build de imagem está em andamento, o Conda é bloqueado pelo cliente do SDK. Se o processo falhou ou foi cancelado incorretamente pelo usuário-Conda permanece no estado bloqueado. Para resolver isso, exclua manualmente o arquivo de bloqueio. 

### <a name="custom-docker-image-not-in-registry"></a>Imagem personalizada do Docker não está no registro
Verifique se a [marca correta](https://docs.microsoft.com/azure/machine-learning/how-to-use-environments#create-an-environment) é usada e isso `user_managed_dependencies = True` . `Environment.python.user_managed_dependencies = True` desabilita o Conda e usa os pacotes instalados do usuário.

### <a name="common-vnet-issues"></a>Problemas comuns de VNet

1. Verifique se a conta de armazenamento, o cluster de computação e o registro de contêiner do Azure estão todos na mesma sub-rede da rede virtual.
2. Quando o ACR está atrás de uma VNet, ele não pode ser usado diretamente para criar imagens. O cluster de computação precisa ser usado para criar imagens.
3. O armazenamento pode precisar ser colocado atrás de uma VNet quando:
    - Usando inferência ou roda privada
    - Vendo erros de serviço 403 (não autorizado)
    - Não é possível obter detalhes da imagem do ACR/MCR

### <a name="image-build-fails-when-trying-to-access-network-protected-storage"></a>Falha na criação de imagem ao tentar acessar o armazenamento protegido de rede
- As tarefas ACR não funcionam por trás da VNet. Se o usuário tiver seu ACR por trás da VNet, ele precisará usar o cluster de computação para criar uma imagem.
- O armazenamento deve estar por trás da VNet para poder extrair dependências dele. 

### <a name="cannot-run-experiments-when-storage-has-network-security-enabled"></a>Não é possível executar experimentos quando o armazenamento tem segurança de rede habilitada
Ao usar imagens padrão do Docker e habilitar dependências gerenciadas pelo usuário, você deve usar as marcas de [serviço](https://docs.microsoft.com/azure/machine-learning/how-to-enable-virtual-network) MicrosoftContainerRegistry e AzureFrontDoor. FirstParty para permitir alist MCR e suas dependências.

 Consulte [habilitando VNET](https://docs.microsoft.com/azure/machine-learning/how-to-enable-virtual-network#azure-container-registry) para obter mais informações.

### <a name="creating-an-icm"></a>Criando um ICM

Ao criar/atribuir um ICM ao metastore, inclua o tíquete de suporte do CSS para que possamos entender melhor o problema.

## <a name="next-steps"></a>Próximas etapas

- [Treinar um modelo de aprendizado de máquina para categorizar flores](how-to-train-scikit-learn.md)
- [Treinar um modelo de aprendizado de máquina usando uma imagem personalizada do Docker](how-to-train-with-custom-image.md)