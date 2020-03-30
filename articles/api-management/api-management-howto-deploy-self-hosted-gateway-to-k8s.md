---
title: Implantar um gateway de gerenciamento de API azure auto-hospedado para kubernetes | Microsoft Docs
description: Saiba como implantar um gateway de gerenciamento de API azure auto-hospedado no Kubernetes
services: api-management
documentationcenter: ''
author: miaojiang
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/31/2019
ms.author: apimpm
ms.openlocfilehash: 109316acb73d3c5f00186298c1f8840c516e5790
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73513829"
---
# <a name="deploy-a-self-hosted-azure-api-management-gateway-to-kubernetes"></a>Implantar um gateway de gerenciamento de API azure auto-hospedado no Kubernetes

Este artigo fornece as etapas para implantar o gateway de gerenciamento de API azure auto-hospedado em um Cluster Kubernetes.

> [!NOTE]
> O recurso gateway auto-hospedado está em visualização. Durante a visualização, o gateway auto-hospedado está disponível apenas nos níveis Desenvolvedor e Premium sem nenhum custo adicional. O nível do desenvolvedor está limitado a uma única implantação de gateway auto-hospedado.


## <a name="prerequisites"></a>Pré-requisitos

- Complete o seguinte quickstart: [Crie uma instância de gerenciamento de API do Azure](get-started-create-service-instance.md)
- Crie um cluster Kubernetes. [Minikube](https://kubernetes.io/docs/tasks/tools/install-minikube/) é uma boa opção para fins de desenvolvimento e avaliação. Para cargas de trabalho de produção, você pode usar [o Azure Kubernetes Service](https://azure.microsoft.com/services/kubernetes-service/) ou um cluster Kubernetes em uma nuvem estrangeira.
- [Provisione um recurso gateway na instância de gerenciamento de API](api-management-howto-provision-self-hosted-gateway.md).

## <a name="deploy-the-gateway-to-kubernetes"></a>Implantar o gateway para Kubernetes

1. Selecione **Gateways** em **Configurações**.
2. Selecione o recurso gateway que você pretende implantar.
3. Selecione **Implantação**.
4. Observe que um novo token na caixa de texto **Token** foi gerado automaticamente para você usando os valores padrão **Expiry** e **Secret Key.** Ajuste um ou ambos, se desejar e selecione **Gerar** para criar um novo token.
5. Certifique-se de que **o Kubernetes** esteja selecionado em **scripts de implantação**.
6. Selecione **<nome do gateway>.yml** link de arquivo ao lado de **Deployment** para baixar o arquivo.
7. Ajuste os mapeamentos da porta e o nome do contêiner no arquivo yml conforme necessário.
8. Selecione o ícone **de cópia** localizado na extremidade direita da caixa de texto **Implantar** para salvar o `kubectl` comando na área de transferência. 
9. Cole o comando na janela terminal (ou comando). Observe que o comando espera que o arquivo de ambiente baixado esteja presente no diretório atual.
```console
    kubectl apply -f <gateway-name>.yaml
```
10. Execute o comando. O comando instrui o cluster Kubernetes a executar o contêiner, usando a imagem do gateway auto-hospedado baixada do Registro de Contêineres da Microsoft e para configurar o contêiner para expor as portas HTTP (8080) e HTTPS (443).
11. Execute o comando abaixo para verificar se a cápsula de gateway está em execução. Note que seu nome de pod será diferente. 
```console
kubectl get pods
NAME                              READY   STATUS    RESTARTS   AGE
local-gateway-55f774f844-bv9wt    1/1     Running   0          1m
```
12. Execute o comando abaixo para verificar se o serviço de gateway está sendo executado. Observe que seu nome de serviço será diferente. 
```console
kubectl get services
NAME             TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
localgateway     NodePort    10.110.230.87   <none>        80:32504/TCP,443:30043/TCP   1m
```
13. Volte para o portal Azure e confirme que o nó de gateway que você acabou de implantar está relatando status saudável.

![status do gateway](media/api-management-howto-deploy-self-hosted-gateway-to-k8s/status.png)

> [!TIP]
> Use <code>kubectl logs <gateway-pod-name></code> o comando para exibir um instantâneo do registro de gateway auto-hospedado.

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre o gateway auto-hospedado, consulte a visão geral do gateway autohospedado do [Azure API Management](self-hosted-gateway-overview.md)
* Saiba mais sobre [o Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/intro-kubernetes)


