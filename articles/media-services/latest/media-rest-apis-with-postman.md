---
title: Configurar o postmaster para chamadas de API REST de Serviços de Mídia do Azure
description: Saiba como configurar o postmaster para chamadas à API REST dos serviços de mídia.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/11/2019
ms.author: juliako
ms.openlocfilehash: c402381534087f1e8cdab711bd1b2a34c78417f4
ms.sourcegitcommit: 9a4296c56beca63430fcc8f92e453b2ab068cc62
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/20/2019
ms.locfileid: "72675719"
---
# <a name="configure-postman-for-media-services-rest-api-calls"></a>Configurar o postmaster para chamadas da API REST dos serviços de mídia

Este artigo mostra como configurar o **postmaster** para que ele possa ser usado para chamar as APIs REST dos serviços de mídia do Azure (AMS). O artigo mostra como importar arquivos de ambiente e de coleção para o **postmaster**. A coleção contém definições agrupadas de solicitações HTTP que chamam as APIs REST dos serviços de mídia do Azure (AMS). O arquivo de ambiente contém variáveis que são usadas pela coleção.

Antes de começar a desenvolver, examine o [desenvolvimento com as APIs dos serviços de mídia v3](media-services-apis-overview.md).

## <a name="prerequisites"></a>Pré-requisitos

- [Crie uma conta dos serviços de mídia](create-account-cli-how-to.md). Lembre-se de lembrar o nome do grupo de recursos e o nome da conta dos serviços de mídia. 
- Obter as informações necessárias para [acessar as APIs](access-api-cli-how-to.md)
- Instale o cliente REST do [postmaster](https://www.getpostman.com/) para executar as APIs REST mostradas em alguns dos tutoriais REST do AMS. 

    Estamos usando o **postmaster** , mas qualquer ferramenta REST seria adequada. Outras alternativas são: **Visual Studio Code** com o plug-in REST ou o **Fiddler Telerik**. 

> [!IMPORTANT]
> Examine as [convenções de nomenclatura](media-services-apis-overview.md#naming-conventions).

## <a name="download-postman-files"></a>Baixar arquivos do postmaster

Clone um repositório GitHub que contém a coleção e os arquivos de ambiente do postmaster.

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-rest-postman.git
 ```

## <a name="configure-postman"></a>Configurar o postmaster

### <a name="configure-the-environment"></a>Configurar o ambiente 

1. Abra o aplicativo do **postmaster** .
2. À direita da tela, selecione a opção **gerenciar ambiente** .

    ![Gerenciar env](./media/develop-with-postman/postman-import-env.png)
4. Na caixa de diálogo **gerenciar ambiente** , clique em **importar**.
2. Navegue até o arquivo de `Azure Media Service v3 Environment.postman_environment.json` que foi baixado quando clonado `https://github.com/Azure-Samples/media-services-v3-rest-postman.git`.
6. O ambiente de **ambiente do serviço de mídia do Azure v3** é adicionado.

    > [!Note]
    > Atualize as variáveis de acesso com os valores obtidos na seção **acessar a API dos serviços de mídia** acima.

7. Clique duas vezes no arquivo selecionado e insira os valores que você obteve seguindo as etapas de API de acesso.
8. Feche a caixa de diálogo.
9. Selecione o ambiente de **ambiente do serviço de mídia do Azure v3** na lista suspensa.

    ![Escolha env](./media/develop-with-postman/choose-env.png)
   
### <a name="configure-the-collection"></a>Configurar a coleção

1. Clique em **importar** para importar o arquivo de coleção.
1. Navegue até o arquivo de `Media Services v3.postman_collection.json` que foi baixado quando clonado `https://github.com/Azure-Samples/media-services-v3-rest-postman.git`
3. Escolha o arquivo **dos serviços de mídia v3. postman_collection. JSON** .

    ![Importar um arquivo](./media/develop-with-postman/postman-import-collection.png)

## <a name="get-azure-ad-token"></a>Obter token do Azure AD 

Antes de começar a manipular os recursos do AMS v3, você precisa obter e definir o token do Azure AD para a autenticação de entidade de serviço.

1. Na janela à esquerda do aplicativo do postmaster, selecione "etapa 1: obter token de autenticação do AAD".
2. Em seguida, selecione "obter token do Azure AD para autenticação de entidade de serviço".
3. Pressione **Enviar**.

    A seguinte operação **post** é enviada.

    ```
    https://login.microsoftonline.com/:tenantId/oauth2/token
    ```

4. A resposta é retornada com o token e define a variável de ambiente "AccessToken" para o valor do token.  

    ![Obter token do AAD](./media/develop-with-postman/postman-get-aad-auth-token.png)

## <a name="see-also"></a>Consulte também

- [Carregar arquivos em uma conta dos serviços de mídia-REST](upload-files-rest-how-to.md)
- [Criar filtros com os serviços de mídia-REST](filters-dynamic-manifest-rest-howto.md)
- [API REST baseada em Azure Resource Manager](https://github.com/Azure-Samples/media-services-v3-arm-templates)

## <a name="next-steps"></a>Próximos passos

- [Transmita arquivos com REST](stream-files-tutorial-with-rest.md).  
- [Tutorial: codificar um arquivo remoto com base na URL e transmitir o vídeo-REST](stream-files-tutorial-with-rest.md)
