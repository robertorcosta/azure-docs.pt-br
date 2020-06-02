---
title: Início rápido – Criar registro no portal
description: Aprenda rapidamente a criar um registro de Docker privado no Registro de Contêiner do Azure com o portal do Azure.
ms.topic: quickstart
ms.date: 03/03/2020
ms.custom: seodec18, mvc
ms.openlocfilehash: 0875e5be628ddfe47696a9d4fc537a8a07122804
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/20/2020
ms.locfileid: "83682810"
---
# <a name="quickstart-create-a-private-container-registry-using-the-azure-portal"></a>Início Rápido: criar um registro de contêiner privado usando o portal do Azure

Um registro de contêiner do Azure é um registro particular do Docker no Azure no qual você pode armazenar e gerenciar imagens de contêiner particulares do Docker e artefatos relacionados. Neste guia de início rápido, você criará um registro de contêiner com o Portal do Azure. Em seguida, use os comandos do Docker para efetuar push de uma imagem de contêiner no Registro e, por fim, efetuar pull e executar a imagem do seu Registro.

Para fazer logon no Registro para trabalhar com imagens de contêiner, este início rápido exige que você esteja executando a CLI do Azure (versão 2.0.55 ou posterior recomendada). Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure][azure-cli].

Você também deve ter o Docker instalado localmente. O Docker fornece pacotes que configuram facilmente o Docker em qualquer sistema [Mac][docker-mac], [Windows][docker-windows] ou [Linux][docker-linux].

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Portal do Azure em https://portal.azure.com.

## <a name="create-a-container-registry"></a>Criar um registro de contêiner

Selecione **Criar um recurso** > **Contêineres** > **Registro de Contêiner**.

![Criar um registro de contêiner no Portal do Azure][qs-portal-01]

Na guia **Noções básicas**, insira valores para **Grupo de recursos** e **Nome do registro**. O nome do registro deve ser exclusivo no Azure e conter de 5 a 50 caracteres alfanuméricos. Para este início rápido crie um novo grupo de recursos no local `West US` denominado `myResourceGroup` e para **SKU**, selecione “Basic”. 

![Criar registro de contêiner no portal do Azure][qs-portal-03]

Aceite os valores padrão para as configurações restantes. Em seguida, selecione **Examinar + criar**. Depois de revisar as configurações, selecione **Criar**.

Neste início rápido você criará um Registro *Básico*, que é uma opção com otimização de custo para desenvolvedores que estão aprendendo sobre o Registro de Contêiner do Azure. Para obter detalhes sobre as camadas de serviço disponíveis, confira [Camadas de serviço do registro de contêiner][container-registry-skus].

Quando a mensagem **Implantação bem-sucedida** é exibida, selecione o registro de contêiner no portal. 

![Visão geral do registro de contêiner no portal do Azure][qs-portal-05]

Anote o valor do **Servidor de logon**. Você usa esse valor nas etapas a seguir ao enviar por push e efetuar pull de imagens com o Docker.

## <a name="log-in-to-registry"></a>Fazer logon no registro

Antes de enviar por push e pull imagens de contêiner, você deverá fazer logon na instância ACR. Abra um shell de comando em seu sistema operacional e use o comando [az acr login][az-acr-login] na CLI do Azure. (Especifique somente o nome do registro ao fazer logon. Não inclua o sufixo 'azurecr.io'.)

```azurecli
az acr login --name <acrName>
```

O comando retorna `Login Succeeded` na conclusão. 

[!INCLUDE [container-registry-quickstart-docker-push](../../includes/container-registry-quickstart-docker-push.md)]

## <a name="list-container-images"></a>Listar imagens de contêiner

Para listar as imagens no seu Registro, navegue até o seu Registro no portal e selecione **Repositórios**, então selecione o repositório que você criou com `docker push`.

Neste exemplo, selecionamos o repositório **hello-world** e podemos ver a imagem marcada com `v1` em **Marcas**.

![Listar imagens de contêiner no portal do Azure][qs-portal-09]

[!INCLUDE [container-registry-quickstart-docker-pull](../../includes/container-registry-quickstart-docker-pull.md)]

## <a name="clean-up-resources"></a>Limpar os recursos

Para limpar seus recursos, navegue até o grupo de recursos **myResourceGroup** no portal. Depois que o grupo de recursos for carregado, clique em **Excluir grupo de recursos** para remover o grupo de recursos, o registro de contêiner e todas as imagens armazenadas lá.

![Excluir um grupo de recursos no portal do Azure][qs-portal-08]

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você criou um Registro de Contêiner do Azure com o portal do Azure, efetuou push de uma imagem de contêiner e extraiu e executou a imagem do Registro. Prossiga para os tutoriais de Registro de Contêiner do Azure para uma análise mais profunda do ACR.

> [!div class="nextstepaction"]
> [Tutoriais de Registro de Contêiner do Azure][container-registry-tutorial-quick-task]

<!-- IMAGES -->
[qs-portal-01]: ./media/container-registry-get-started-portal/qs-portal-01.png
[qs-portal-02]: ./media/container-registry-get-started-portal/qs-portal-02.png
[qs-portal-03]: ./media/container-registry-get-started-portal/qs-portal-03.png
[qs-portal-05]: ./media/container-registry-get-started-portal/qs-portal-05.png
[qs-portal-08]: ./media/container-registry-get-started-portal/qs-portal-08.png
[qs-portal-09]: ./media/container-registry-get-started-portal/qs-portal-09.png

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-rmi]: https://docs.docker.com/engine/reference/commandline/rmi/
[docker-run]: https://docs.docker.com/engine/reference/commandline/run/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[container-registry-tutorial-quick-task]: container-registry-tutorial-quick-task.md
[container-registry-skus]: container-registry-skus.md
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-login]: /cli/azure/acr#az-acr-login
