---
title: Implantar gateway de gerenciamento de API azure auto-hospedado para o Docker | Microsoft Docs
description: Saiba como implantar um gateway de gerenciamento de API do Azure auto-hospedado para o Docker
services: api-management
documentationcenter: ''
author: vladvino
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/31/2019
ms.author: apimpm
ms.openlocfilehash: b5acda30f7f4a01e1b7b6ac82652452c3c40e3bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75768496"
---
# <a name="deploy-an-azure-api-management-self-hosted-gateway-to-docker"></a>Implantar um gateway auto-hospedado de gerenciamento de API do Azure para o Docker

Este artigo fornece as etapas para implantar o gateway de gerenciamento de API do Azure auto-hospedado em um ambiente Docker.

> [!NOTE]
> O recurso gateway auto-hospedado está em visualização. Durante a visualização, o gateway auto-hospedado está disponível apenas nos níveis Desenvolvedor e Premium sem nenhum custo adicional. O nível do desenvolvedor está limitado a uma única implantação de gateway auto-hospedado.

## <a name="prerequisites"></a>Pré-requisitos

- Complete o seguinte quickstart: [Crie uma instância de gerenciamento de API do Azure](get-started-create-service-instance.md)
- Crie um ambiente Docker. [Docker for Desktop](https://www.docker.com/products/docker-desktop) é uma boa opção para fins de desenvolvimento e avaliação. Consulte a [documentação do Docker](https://docs.docker.com) para obter informações sobre todas as edições do Docker, seus recursos e documentação abrangente sobre o próprio Docker.
- [Provisão um recurso gateway na instância de gerenciamento de API](api-management-howto-provision-self-hosted-gateway.md)

> [!NOTE]
> O gateway auto-hospedado é embalado como um contêiner Docker baseado em Linux x86-64.

## <a name="deploy-the-self-hosted-gateway-to-docker"></a>Implantar o gateway auto-hospedado no Docker

1. Selecione **Gateways** em **Configurações**.
2. Selecione o recurso gateway que você pretende implantar.
3. Selecione **Implantação**.
4. Observe que um novo token na caixa de texto **Token** foi gerado automaticamente para você usando os valores padrão **Expiry** e **Secret Key.** Ajuste um ou ambos, se desejar e selecione **Gerar** para criar um novo token.
4. Certifique-se de que **o Docker** esteja selecionado em **scripts de implantação**.
5. Selecione o link de arquivo **env.conf** ao lado do **Ambiente** para baixar o arquivo.
6. Selecione o ícone **de cópia** localizado na extremidade direita da caixa de texto **Executar** para salvar o comando Docker na área de transferência.
7. Cole o comando na janela terminal (ou comando). Ajuste os mapeamentos da porta e o nome do contêiner conforme necessário. Observe que o comando espera que o arquivo de ambiente baixado esteja presente no diretório atual.
```
    docker run -d -p 80:8080 -p 443:8081 --name <gateway-name> --env-file env.conf mcr.microsoft.com/azure-api-management/gateway:<tag>
```
8. Execute o comando. O comando instrui o ambiente Docker a executar o contêiner, usando a imagem do gateway auto-hospedado baixada do Registro de Contêineres da Microsoft e para mapear as portas HTTP (8080) e HTTPS (8081) do contêiner para as portas 80 e 443 no host.
9. Execute o comando abaixo para verificar se o gateway pod está sendo executado:
```console
docker ps
CONTAINER ID        IMAGE                                                 COMMAND                  CREATED             STATUS              PORTS                                         NAMES
895ef0ecf13b        mcr.microsoft.com/azure-api-management/gateway:beta   "/bin/sh -c 'dotnet …"   5 seconds ago       Up 3 seconds        0.0.0.0:80->8080/tcp, 0.0.0.0:443->8081/tcp   my-gateway
```
10. Volte para o portal Azure e confirme que o nó de gateway que você acabou de implantar está relatando status saudável.

![status do gateway](media/api-management-howto-deploy-self-hosted-gateway-to-docker/status.png)

> [!TIP]
> Use <code>console docker container logs <gateway-name></code> o comando para exibir um instantâneo do registro de gateway auto-hospedado.
>
> Use <code>docker container logs --help</code> o comando para ver todas as opções de visualização de log.

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre o gateway auto-hospedado, consulte a visão geral do gateway autohospedado do [AZURE API Management](self-hosted-gateway-overview.md).
* [Configure o nome de domínio personalizado para o gateway auto-hospedado](api-management-howto-configure-custom-domain-gateway.md).
