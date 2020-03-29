---
title: Como implantar a ferramenta de rotulagem de amostra do Form Recognizer
titleSuffix: Azure Cognitive Services
description: Aprenda as diferentes maneiras pelas quais você pode implantar a ferramenta de rotulagem de amostra do Form Recognizer para ajudar no aprendizado supervisionado.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: how-to
ms.date: 03/20/2020
ms.author: pafarley
ms.openlocfilehash: 795d21e05ade652b52c06d597ca4c5fef85e7245
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80152785"
---
# <a name="deploy-the-sample-labeling-tool"></a>Implantar a ferramenta de rotulagem de amostras

A ferramenta de rotulagem de amostra do Form Recognizer é um aplicativo que fornece uma interface de usuário simples (UI), que você pode usar para rotular manualmente formulários (documentos) com o propósito de aprendizagem supervisionada. Neste artigo, forneceremos links e instruções que ensinam como:

* [Execute a ferramenta de rotulagem de amostra localmente](#run-the-sample-labeling-tool-locally)
* [Implante a ferramenta de rotulagem de amostra em uma ACI (ACI) de controle de contêiner azure](#deploy-with-azure-container-instances-aci)
* [Use e contribua para a ferramenta de rotulagem de formulários OCR de código aberto](#open-source-on-github)

## <a name="run-the-sample-labeling-tool-locally"></a>Execute a ferramenta de rotulagem de amostra localmente

A maneira mais rápida de começar a rotular dados é executar a ferramenta de rotulagem de amostras localmente. O quickstart a seguir usa a API De reconhecimento de formulário e a ferramenta de rotulagem de amostra para treinar um modelo personalizado com dados rotulados manualmente. 

* [Quickstart: Etiquetar forma, treinar um modelo e analisar um formulário usando a ferramenta de rotulagem da amostra](./quickstarts/label-tool.md).

## <a name="deploy-with-azure-container-instances-aci"></a>Implantar com a ACI (ACI) 'Acis'

Antes de começarmos, é importante notar que existem duas maneiras de implantar a ferramenta de rotulagem de amostras em uma ACI (AC). Ambas as opções são usadas para executar a ferramenta de rotulagem de amostra com ACI: 

* [Usando o portal Azure](#azure-portal)
* [Usando o Cli Azure](#azure-cli)

### <a name="azure-portal"></a>Portal do Azure

Siga estas etapas para criar um novo recurso usando o portal Azure: 

1. Faça login no [portal Azure](https://portal.azure.com/signin/index/).
2. Selecione **Criar um recurso**. 
3. Em seguida, selecione **Web App**. 

   > [!div class="mx-imgBorder"]
   > ![Selecionar aplicativo Web](./media/quickstarts/formre-create-web-app.png)
   
4. Primeiro, certifique-se de que a guia **Básico** está selecionada. Agora, você vai precisar fornecer algumas informações: 

   > [!div class="mx-imgBorder"]
   > ![Selecione noções básicas](./media/quickstarts/formre-select-basics.png)
   * Assinatura - Selecione uma assinatura existente do Azure
   * Grupo de recursos - Você pode reutilizar um grupo de recursos existente ou criar um novo para este projeto. Recomenda-se a criação de um novo grupo de recursos.
   * Nome - Dê um nome ao seu aplicativo web. 
   * Publicar - Selecionar **contêiner Docker**
   * Sistema Operacional - Selecione **O Linux**
   * Região - Escolha uma região que faça sentido para você.
   * Plano Linux - Selecione um nível/plano de preços para o seu serviço de aplicativo. 

   > [!div class="mx-imgBorder"]
   > ![Configure seu aplicativo web](./media/quickstarts/formre-select-docker-linux.png)

5. Em seguida, selecione a guia **Docker.** 

   > [!div class="mx-imgBorder"]
   > ![Selecione Docker](./media/quickstarts/formre-select-docker.png)

6. Agora vamos configurar seu recipiente Docker. Todos os campos são necessários, a menos que seja observado de outra forma:

   * Opções - Selecione **o único contêiner**
   * Fonte de imagem - Selecione **Registro Privado** 
   * URL do servidor - Defina isso para`https://mcr.microsoft.com`
   * Nome de usuário (Opcional) - Crie um nome de usuário. 
   * Senha (Opcional) - Crie uma senha segura que você se lembrará.
   * Imagem e tag - Definir isso para`mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool:latest`
   * Comando de inicialização - Defina isso para`./run.sh eula=accept`

   > [!div class="mx-imgBorder"]
   > ![Configurar o Docker](./media/quickstarts/formre-configure-docker.png)

7. É isso. Em seguida, **selecione 'Revisar + Criar'** e **criar** para implantar seu aplicativo web. Quando concluído, você pode acessar seu aplicativo web na URL fornecida na **Visão Geral** para o seu recurso.

> [!NOTE]
> Ao criar seu aplicativo web, você também pode configurar a autorização/autenticação. Isso não é necessário para começar. 

### <a name="azure-cli"></a>CLI do Azure

Como alternativa para usar o portal Azure, você pode criar um recurso usando o Cli do Azure. Antes de continuar, você precisará instalar o [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Você pode pular este passo se você já estiver trabalhando com o Azure CLI. 

Há algumas coisas que você precisa saber sobre este comando:

* `DNS_NAME_LABEL=aci-demo-$RANDOM`gera um nome DNS aleatório. 
* Esta amostra pressupõe que você tem um grupo de recursos que você pode usar para criar um recurso. Substitua por `<resource_group_name>` um grupo de recursos válido associado à sua assinatura. 
* Você precisará especificar onde deseja criar o recurso. Substitua pela `<region name>` região desejada para o aplicativo web. 
* Este comando aceita automaticamente EULA.

A partir do Azure CLI, execute este comando para criar um recurso de aplicativo web para a ferramenta de rotulagem de amostras: 

```azurecli
DNS_NAME_LABEL=aci-demo-$RANDOM

az container create \
  --resource-group <resorunce_group_name> \
  --name <name> \
  --image mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool \
  --ports 3000 \
  --dns-name-label $DNS_NAME_LABEL \
  --location <region name> \
  --cpu 2 \
  --memory 8
  --command-line "./run.sh eula=accept"
```

### <a name="connect-to-azure-ad-for-authorization"></a>Conecte-se ao Azure AD para autorização

Recomenda-se conectar seu aplicativo web ao Azure Active Directory. Isso garante que apenas usuários com credenciais válidas possam fazer login e usar seu aplicativo web. Siga as instruções em [Configure seu aplicativo App Service](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-aad) para se conectar ao Azure Active Directory.

## <a name="open-source-on-github"></a>Código aberto no GitHub

A Ferramenta de Rotulagem de Formulários OCR também está disponível como um projeto de código aberto no GitHub. A ferramenta é um aplicativo web construído usando React + Redux, e é escrito no TypeScript. Para saber mais ou contribuir, consulte [a ferramenta de rotulagem de formulários OCR](https://github.com/microsoft/OCR-Form-Tools/blob/master/README.md).

## <a name="next-steps"></a>Próximas etapas

Use o [Trem com etiquetas](./quickstarts/label-tool.md) quickstart para aprender a usar a ferramenta para rotular manualmente dados de treinamento e realizar aprendizado supervisionado.
