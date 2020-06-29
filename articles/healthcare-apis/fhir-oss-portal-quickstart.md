---
title: 'Portal do Azure: Implantar o servidor FHIR de software livre para o Azure – API do Azure para FHIR'
description: Este guia de início rápido explica como implantar o servidor FHIR de software livre da Microsoft usando o portal do Azure.
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: 5109c9a7c6432e42c6841b89cc28bde3e92c74aa
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2019
ms.locfileid: "84819893"
---
# <a name="quickstart-deploy-open-source-fhir-server-using-azure-portal"></a>Início Rápido: Implantar o servidor FHIR de software livre usando o portal do Azure

Neste guia de início rápido, você aprenderá a implantar um servidor FHIR de software livre no Azure usando o portal do Azure. Usaremos links de implantação fáceis no [Repositório de software livre](https://github.com/Microsoft/fhir-server)

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="github-open-source-repository"></a>Repositório de software livre do GitHub

Navegue até a [página de Implantação do GitHub](https://github.com/Microsoft/fhir-server/blob/master/docs/DefaultDeployment.md) e localize os botões "Implantar no Azure":

![Página de Implantação de Software Livre](media/quickstart-oss-portal/deployment-page-oss.png)

Clique no botão implantação e a portal do Azure será aberto.

## <a name="fill-in-deployment-parameters"></a>Preencher parâmetros de implantação

Escolha criar um grupo de recursos e dê a ele um nome. Somente outro parâmetro obrigatório é um nome para o serviço.

![Parâmetros de implantação personalizados](media/quickstart-oss-portal/deployment-custom-parameters.png)

Observe que a implantação efetuará pull do código-fonte diretamente do repositório de software livre no GitHub. Se você tiver bifurcado o repositório, poderá apontar para o seu próprio para uma ramificação específica.

Depois de preencher os detalhes, você pode iniciar a implantação.

## <a name="validate-fhir-server-is-running"></a>Validar se o servidor FHIR está em execução

Depois que a implantação estiver concluída, você poderá apontar seu navegador para `https://SERVICENAME.azurewebsites.net/metadata` para obter uma declaração de capacidade. Levará um minuto para que o servidor responda pela primeira vez.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando o grupo de recursos e todos os recursos relacionados não forem mais necessários, exclua-os. Para fazer isso, selecione o grupo de recursos que contém os recursos provisionados, selecione **Excluir grupo de recursos** e confirme o nome do grupo de recursos a ser excluído.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você implantou o servidor FHIR de software livre da Microsoft para o Azure em sua assinatura. Para saber como acessar a API do FHIR usando o Postman, prossiga para o tutorial do Postman.
 
>[!div class="nextstepaction"]
>[Acessar a API do FHIR usando o Postman](access-fhir-postman-tutorial.md)