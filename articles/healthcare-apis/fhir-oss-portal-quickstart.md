---
title: 'Portal do Azure: Implantar o servidor FHIR de software livre para o Azure – API do Azure para FHIR'
description: Este guia de início rápido explica como implantar o servidor FHIR de software livre da Microsoft usando o portal do Azure.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 02/07/2019
ms.author: matjazl
ms.openlocfilehash: 57ab6bca820c4c25a9a56e4a801aa7d917d317ec
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2020
ms.locfileid: "90978593"
---
# <a name="quickstart-deploy-open-source-fhir-server-using-azure-portal"></a>Início Rápido: Implantar o servidor FHIR de software livre usando o portal do Azure

Neste guia de início rápido, você aprenderá a implantar um servidor FHIR de software livre no Azure usando o portal do Azure. Usaremos links de implantação fáceis no [Repositório de software livre](https://github.com/Microsoft/fhir-server)

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="github-open-source-repository"></a>Repositório de software livre do GitHub

Navegue até a [página de Implantação do GitHub](https://github.com/Microsoft/fhir-server/blob/master/docs/DefaultDeployment.md) e localize os botões "Implantar no Azure":

>[!div class="mx-imgBorder"]
>![Página de Implantação de Software Livre](media/quickstart-oss-portal/deployment-page-oss.png)

Clique no botão implantação e a portal do Azure será aberto.

## <a name="fill-in-deployment-parameters"></a>Preencher parâmetros de implantação

Escolha criar um grupo de recursos e dê a ele um nome. Somente outros parâmetros obrigatórios são o nome do serviço e a senha de administrador do SQL.

>[!div class="mx-imgBorder"]
>![Parâmetros de implantação personalizados](media/quickstart-oss-portal/deployment-custom-parameters.png)

Depois de preencher os detalhes, você pode iniciar a implantação.

## <a name="validate-fhir-server-is-running"></a>Validar se o servidor FHIR está em execução

Depois que a implantação estiver concluída, você poderá apontar seu navegador para `https://SERVICENAME.azurewebsites.net/metadata` para obter uma declaração de capacidade. Levará um minuto para que o servidor responda pela primeira vez.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando o grupo de recursos e todos os recursos relacionados não forem mais necessários, exclua-os. Para fazer isso, selecione o grupo de recursos que contém os recursos provisionados, selecione **Excluir grupo de recursos** e confirme o nome do grupo de recursos a ser excluído.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você implantou o servidor FHIR de software livre da Microsoft para o Azure em sua assinatura. Para saber como acessar a API do FHIR usando o Postman, prossiga para o tutorial do Postman.
 
>[!div class="nextstepaction"]
>[Acessar a API do FHIR usando o Postman](access-fhir-postman-tutorial.md)