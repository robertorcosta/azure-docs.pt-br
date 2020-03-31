---
title: incluir arquivo
description: incluir arquivo
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 04/25/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: f74d4cbc17e49345534a37e9e6612a36e19be295
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73903690"
---
Mas, se você quiser compartilhar imagens fora do seu inquilino do Azure, em escala, você deve criar um registro de aplicativo para facilitar o compartilhamento.  O uso de um registro de aplicativo pode permitir cenários de compartilhamento mais complexos, como: 

* Gerenciamento de imagens compartilhadas quando uma empresa adquire outra, e a infra-estrutura do Azure é espalhada por inquilinos separados. 
* Os Azure Partners gerenciam a infra-estrutura do Azure em nome de seus clientes. A personalização das imagens é feita dentro do inquilino parceiro, mas as implantações de infraestrutura acontecerão no inquilino do cliente. 


## <a name="create-the-app-registration"></a>Crie o registro do aplicativo

Crie um registro de aplicativo que será usado por ambos os inquilinos para compartilhar os recursos da galeria de imagens.
1. Abra as inscrições do [App (pré-visualização) no portal Azure](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType//sourceType/).    
1. Selecione **Novo registro** no menu no topo da página.
1. Em **Nome,** digite *myGalleryApp*.
1. Em **tipos de conta suportados,** selecione Contas em qualquer diretório organizacional e contas pessoais da **Microsoft**.
1. Em **Redirecionar URI,** digite *https://www.microsoft.com* e, em seguida, selecione **Registrar**. Depois que o registro do aplicativo tiver sido criado, a página de visão geral será aberta.
1. Na página de exibição geral, copie o **ID do aplicativo (cliente)** e salve para uso posteriormente.   
1. Selecione **Certificados & segredos**e selecione **Novo segredo do cliente**.
1. Em **Description**, *digite o segredo do aplicativo cross-tenant da galeria de imagens compartilhadas*.
1. Em **Expirações,** deixe o padrão de **Em 1 ano** e, em seguida, **selecione Adicionar**.
1. Copie o valor do segredo e guarde-o para um lugar seguro. Você não pode recuperá-lo depois de sair da página.


Dê ao aplicativo permissão de registro para usar a galeria de imagens compartilhadas.
1. No portal Azure, selecione a Galeria de Imagens Compartilhadas que você deseja compartilhar com outro inquilino.
1. Selecione **selecionar controle de acesso (IAM)** e, em Adicionar **atribuição de função,** *selecione Adicionar*. 
1. Em **Função,** selecione **'Leitor'.**
1. Em **Atribuir acesso a:**, deixe-o como **usuário, grupo ou principal de serviço do Azure AD**.
1. Em **Select**, digite *myGalleryApp* e selecione-o quando ele aparecer na lista. Quando terminar, selecione **Salvar**.


## <a name="give-tenant-2-access"></a>Dar acesso ao Inquilino 2

Dê ao Inquilino 2 acesso ao aplicativo solicitando um login usando um navegador. Substitua o * \<I>D do Inquilino2* pelo ID do inquilino para o inquilino com o quais você gostaria de compartilhar sua galeria de imagens. Substituir o ID do * \<aplicativo (cliente)>* com o ID do aplicativo do registro do aplicativo que você criou. Quando terminar de fazer as substituições, cole a URL em um navegador e siga as instruções de login para entrar no Inquilino 2.

```
https://login.microsoftonline.com/<Tenant 2 ID>/oauth2/authorize?client_id=<Application (client) ID>&response_type=code&redirect_uri=https%3A%2F%2Fwww.microsoft.com%2F 
```

No [portal Azure](https://portal.azure.com) faça login como Inquilino 2 e dê ao aplicativo acesso de registro ao grupo de recursos onde você deseja criar a VM.

1. Selecione o grupo de recursos e selecione **Controle de acesso (IAM)**. Em **Adicionar atribuição de função,** **selecione Adicionar**. 
1. Em **Função**, tipo **Contribuinte**.
1. Em **Atribuir acesso a:**, deixe-o como **usuário, grupo ou principal de serviço do Azure AD**.
1. Em **Selecionar** o tipo *myGalleryApp,* selecione-o quando ele aparecer na lista. Quando terminar, selecione **Salvar**.

> [!NOTE]
> Você precisa esperar que a versão da imagem termine completamente de ser construída e replicada antes de poder usar a mesma imagem gerenciada para criar outra versão de imagem.

