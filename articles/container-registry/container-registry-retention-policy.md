---
title: Política de retenção de manifestos não marcados
description: Saiba como ativar uma política de retenção no registro de contêiner do Azure, para exclusão automática de manifestos não marcados após um período definido.
ms.topic: article
ms.date: 10/02/2019
ms.openlocfilehash: 912616b6ab95cdff91e70477c7d6de476ccfdfa7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74454819"
---
# <a name="set-a-retention-policy-for-untagged-manifests"></a>Defina uma política de retenção para manifestos não marcados

O Azure Container Registry oferece a opção de definir uma *política de retenção* para manifestos de imagem armazenados que não tenham nenhuma tag *(manifestos não marcados).* Quando uma política de retenção é ativada, os manifestos não marcados no registro são automaticamente excluídos após vários dias definidos. Esse recurso impede que o registro se enchia de artefatos que não são necessários e ajuda a economizar nos custos de armazenamento. Se `delete-enabled` o atributo de um manifesto `false`não marcado for definido para , o manifesto não pode ser excluído e a política de retenção não se aplica.

Você pode usar o Azure Cloud Shell ou uma instalação local do Azure CLI para executar os exemplos de comando neste artigo. Se você quiser usá-lo localmente, a versão 2.0.74 ou posterior é necessária. Execute `az --version` para encontrar a versão. Se você precisar instalar ou atualizar, consulte [Install Azure CLI][azure-cli].

> [!IMPORTANT]
> Esse recurso está na versão prévia no momento; algumas [limitações se aplicam](#preview-limitations). As visualizações são disponibilizadas para você com a condição de que você concorde com os [termos de uso suplementar][terms-of-use]. Alguns aspectos desse recurso podem alterar antes da GA (disponibilidade geral).

> [!WARNING]
> Defina uma política de retenção com cuidado - os dados de imagem excluídos são IRRECUPERÁVEIS. Se você tem sistemas que puxam imagens por digestão manifesto (ao contrário do nome da imagem), você não deve definir uma política de retenção para manifestos não marcados. A exclusão de imagens não marcadas impedirá esses sistemas de puxar as imagens do seu registro. Em vez de receber pelo manifesto, considerar a adoção de uma *marcação exclusiva* esquema, um [melhor prática recomendada](container-registry-image-tag-version.md).

## <a name="preview-limitations"></a>Limitações de visualização

* Apenas um registro de contêiner **Premium** pode ser configurado com uma política de retenção. Para obter informações sobre os níveis de serviço de registro, consulte [As SKUs de registro de contêiner do Azure](container-registry-skus.md).
* Você só pode definir uma política de retenção para manifestos não marcados.
* Atualmente, a política de retenção se aplica apenas a manifestações que não são marcadas *após* a ativação da diretiva. Os manifestos não marcados existentes no registro não estão sujeitos à política. Para excluir manifestos não marcados existentes, consulte exemplos em [Excluir imagens de contêiner no Registro de Contêineres do Azure](container-registry-delete.md).

## <a name="about-the-retention-policy"></a>Sobre a política de retenção

O Registro de Contêineres do Azure faz a contagem de referência para manifestações no registro. Quando um manifesto é desmarcado, ele verifica a política de retenção. Se uma política de retenção estiver ativada, uma operação de exclusão manifesto será enfileirada, com uma data específica, de acordo com o número de dias definido na diretiva.

Um trabalho separado de gerenciamento de filas processa constantemente mensagens, dimensionando conforme necessário. Como exemplo, suponha que você desmarcou dois manifestos, com uma hora de intervalo, em um registro com uma política de retenção de 30 dias. Duas mensagens estariam na fila. Então, 30 dias depois, aproximadamente 1 hora de intervalo, as mensagens seriam recuperadas da fila e processadas, assumindo que a apólice ainda estava em vigor.

## <a name="set-a-retention-policy---cli"></a>Defina uma política de retenção - CLI

O exemplo a seguir mostra como usar o Azure CLI para definir uma política de retenção para manifestos não marcados em um registro.

### <a name="enable-a-retention-policy"></a>Habilite uma política de retenção

Por padrão, nenhuma política de retenção é definida em um registro de contêiner. Para definir ou atualizar uma política de retenção, execute o comando [az acr config retention update][az-acr-config-retention-update] no Azure CLI. Você pode especificar um número de dias entre 0 e 365 para manter os manifestos não marcados. Se você não especificar um número de dias, o comando definirá um padrão de 7 dias. Após o período de retenção, todos os manifestos não marcados no registro são automaticamente excluídos.

O exemplo a seguir estabelece uma política de retenção de 30 dias para manifestos não marcados no *meu registro*de registro :

```azurecli
az acr config retention update --registry myregistry --status enabled --days 30 --type UntaggedManifests
```

O exemplo a seguir define uma política para excluir qualquer manifesto no registro assim que ele não for marcado. Crie esta diretiva definindo um período de retenção de 0 dias. 

```azurecli
az acr config retention update --registry myregistry --status enabled --days 0 --type UntaggedManifests
```

### <a name="validate-a-retention-policy"></a>Validar uma política de retenção

Se você habilitar a diretiva anterior com um período de retenção de 0 dias, você poderá verificar rapidamente se os manifestos não marcados são excluídos:

1. Empurre uma `hello-world:latest` imagem de teste para o seu registro ou substitua outra imagem de teste de sua escolha.
1. Desmarcar `hello-world:latest` a imagem, por exemplo, usando o comando [az acr repositório untag.][az-acr-repository-untag] O manifesto não marcado permanece no registro.
    ```azurecli
    az acr repository untag --name myregistry --image hello-world:latest
    ```
1. Em poucos segundos, o manifesto não marcado é excluído. Você pode verificar a exclusão listando manifestos no repositório, por exemplo, usando o comando [az acr repositório show-manifests.][az-acr-repository-show-manifests] Se a imagem de teste foi a única no repositório, o próprio repositório será excluído.

### <a name="disable-a-retention-policy"></a>Desativar uma política de retenção

Para ver a política de retenção definida em um registro, execute o comando [az acr config retention show:][az-acr-config-retention-show]

```azurecli
az acr config retention show --registry myregistry
```

Para desativar uma diretiva de retenção em um registro, execute o `--status disabled`comando [az acr config retention update][az-acr-config-retention-update] e set :

```azurecli
az acr config retention update --registry myregistry --status disabled --type UntaggedManifests
```

## <a name="set-a-retention-policy---portal"></a>Definir uma política de retenção - portal

Você também pode definir a política de retenção de um registro no [portal Azure](https://portal.azure.com). O exemplo a seguir mostra como usar o portal para definir uma política de retenção de manifestos não marcados em um registro.

### <a name="enable-a-retention-policy"></a>Habilite uma política de retenção

1. Navegue até o registro de contêineres do Azure. Em **Políticas,** selecione **Retenção** (Visualização).
1. Em **Status,** **selecione Ativado**.
1. Selecione um número de dias entre 0 e 365 para manter os manifestos não marcados. Selecione **Salvar**.

![Habilite uma política de retenção no portal Azure](media/container-registry-retention-policy/container-registry-retention-policy01.png)

### <a name="disable-a-retention-policy"></a>Desativar uma política de retenção

1. Navegue até o registro de contêineres do Azure. Em **Políticas,** selecione **Retenção** (Visualização).
1. Em **Status,** **selecione Disabled**. Selecione **Salvar**.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre opções para [excluir imagens e repositórios](container-registry-delete.md) no Registro de Contêineres do Azure

* Saiba como [limpar automaticamente](container-registry-auto-purge.md) imagens selecionadas e manifestos de um registro

* Saiba mais sobre opções de [bloqueio de imagens e manifestos](container-registry-image-lock.md) em um registro

<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/


<!-- LINKS - internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-config-retention-update]: /cli/azure/acr/config/retention#az-acr-config-retention-update
[az-acr-config-retention-show]: /cli/azure/acr/config/retention#az-acr-config-retention-show
[az-acr-repository-untag]: /cli/azure/acr/repository#az-acr-repository-untag
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
