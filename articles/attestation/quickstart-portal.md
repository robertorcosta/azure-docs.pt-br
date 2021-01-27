---
title: Configurar Atestado do Azure com o portal do Azure
description: Como configurar um provedor de atestado usando o portal do Azure.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: 3ab1e6011a1c127c9ac5a2c7652a4bf458372e1e
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98733930"
---
# <a name="quickstart-set-up-azure-attestation-with-azure-portal"></a>Início rápido: Configurar Atestado do Azure com o portal do Azure

Siga as etapas abaixo para gerenciar um provedor de atestado usando o portal do Azure.

## <a name="attestation-provider"></a>Provedor de atestado

### <a name="create-an-attestation-provider"></a>Criar um provedor de atestado

#### <a name="to-configure-the-provider-with-unsigned-policies"></a>Para configurar o provedor com políticas não assinadas

1.  No menu do portal do Azure ou na home page, selecione **Criar um recurso**
2.  Na caixa de pesquisa, insira **atestado**
3.  Na lista de resultados, escolha **Atestado do Microsoft Azure**
4.  Na página do Atestado do Microsoft Azure, escolha **Criar**
5.  Na página Criar provedor de atestado, forneça as seguintes entradas:
    
    **Assinatura**: Escolha uma assinatura
    
    **Grupo de recursos**: selecione um grupo de recursos existente ou escolha **Criar** e insira um nome para o grupo de recursos
    
    **Nome**: um nome exclusivo é necessário

    **Localização**: escolha uma localização 
    
    **Arquivo de certificado de signatário de política**: não carregue o arquivo de certificado de signatário de política para configurar o provedor com políticas não assinadas 
6.  Depois de fornecer as entradas necessárias, clique em **Examinar+Criar**
7.  Corrija problemas de validação, se houver, e clique em **Criar**.

#### <a name="to-configure-the-provider-with-signed-policies"></a>Para configurar o provedor com políticas assinadas

1.  No menu do portal do Azure ou na home page, selecione **Criar um recurso**
2.  Na caixa de pesquisa, insira **atestado**
3.  Na lista de resultados, escolha **Atestado do Microsoft Azure**
4.  Na página do Atestado do Microsoft Azure, escolha **Criar**
5.  Na página Criar provedor de atestado, forneça as seguintes informações:
    
    a. **Assinatura**: Escolha uma assinatura
    
    b. **Grupo de recursos**: selecione um grupo de recursos existente ou escolha **Criar** e insira um nome para o grupo de recursos
    
    c. **Nome**: um nome exclusivo é necessário

    d. **Localização**: escolha uma localização 
    
    e. **Arquivo de certificado de signatário de política**: Para configurar o provedor de atestado com certificados de assinatura de política, carregue o arquivo de certificados. Veja exemplos [aqui](./policy-signer-examples.md) 
6.  Depois de fornecer as entradas necessárias, clique em **Examinar+Criar**
7.  Corrija problemas de validação, se houver, e clique em **Criar**.

### <a name="view-attestation-provider"></a>Exibir provedor de atestado

1.  No menu do portal do Azure ou na home page, selecione **Todos os recursos**
2.  Na caixa de filtro, digite nome do provedor de atestado e selecione-o

### <a name="delete-attestation-provider"></a>Excluir provedor de atestado

1.  No menu do portal do Azure ou na home page, selecione **Todos os recursos**
2.  Na caixa de filtro, digite nome do provedor de atestado
3.  Marque a caixa de seleção e clique em **Excluir**
4.  Digite Sim e clique em **Excluir** [OU]
1.  No menu do portal do Azure ou na home page, selecione **Todos os recursos**
2.  Na caixa de filtro, digite nome do provedor de atestado
3.  Selecione o provedor de atestado e navegue até a página de visão geral
4.  Clique em **Excluir** no menu superior e clique em **Sim**


## <a name="attestation-policy-signers"></a>Signatários de política de atestado

### <a name="view-policy-signer-certificates"></a>Exibir certificados de signatário de política

1.  No menu do portal do Azure ou na home page, selecione **Todos os recursos**
2.  Na caixa de filtro, digite nome do provedor de atestado
3.  Selecione o provedor de atestado e navegue até a página de visão geral
4.  Clique em **Certificados de signatário de política** no menu de recursos esquerdo ou no painel inferior
5.  Clique em **Baixar certificados de signatário de política** (o botão será desabilitado para os provedores de atestado criados sem o requisito de assinatura de política)
6.  O arquivo de texto baixado terá todos os certificados em um formato JWS.
a.  Verifique a contagem dos certificados e se eles foram baixados.

### <a name="add-policy-signer-certificate"></a>Adicionar certificado de signatário de política

1.  No menu do portal do Azure ou na home page, selecione **Todos os recursos**
2.  Na caixa de filtro, digite nome do provedor de atestado
3.  Selecione o provedor de atestado e navegue até a página de visão geral
4.  Clique em **Certificados de signatário de política** no menu de recursos esquerdo ou no painel inferior
5.  Clique em **Adicionar** no menu superior (o botão será desabilitado para os provedores de atestado criados sem o requisito de assinatura de política)
6.  Carregue o arquivo de certificado de signatário de política e clique em **Adicionar**. Veja exemplos [aqui](./policy-signer-examples.md)

### <a name="delete-policy-signer-certificate"></a>Excluir certificado de signatário de política

1.  No menu do portal do Azure ou na home page, selecione **Todos os recursos**
2.  Na caixa de filtro, digite nome do provedor de atestado
3.  Selecione o provedor de atestado e navegue até a página de visão geral
4.  Clique em **Certificados de signatário de política** no menu de recursos esquerdo ou no painel inferior
5.  Clique em **Excluir** no menu superior (o botão será desabilitado para os provedores de atestado criados sem o requisito de assinatura de política)
6.  Carregue o arquivo de certificado de signatário de política e clique em **Excluir**. Veja exemplos [aqui](./policy-signer-examples.md) 

## <a name="attestation-policy"></a>Política de atestado

### <a name="view-attestation-policy"></a>Exibir política de atestado

1.  No menu do portal do Azure ou na home page, selecione **Todos os recursos**
2.  Na caixa de filtro, digite nome do provedor de atestado
3.  Selecione o provedor de atestado e navegue até a página de visão geral
4.  Clique em **Política** no menu de recursos esquerdo ou no painel inferior
5.  Selecione o **Tipo de Atestado** e veja a **Política atual**

### <a name="configure-attestation-policy"></a>Configurar política de atestado

#### <a name="when-attestation-provider-is-created-without-policy-signing-requirement"></a>Quando o provedor de atestado é criado sem o requisito de assinatura de política

##### <a name="upload-policy-in-jwt-format"></a>Carregar política no formato JWT

1.  No menu do portal do Azure ou na home page, selecione **Todos os recursos**
2.  Na caixa de filtro, digite nome do provedor de atestado
3.  Selecione o provedor de atestado e navegue até a página de visão geral
4.  Clique em **Política** no menu de recursos esquerdo ou no painel inferior
5.  Clique em **Configurar** no menu superior
6.  Quando o provedor de atestado é criado sem requisito de assinatura de política, o usuário pode carregar uma política em formato **JWT** ou de **Texto**
7.  Selecione o **Formato de Política** como **JWT**
8.  Carregue o arquivo de política com o conteúdo da política em um formato **JWT não assinado/assinado** e clique em **Salvar**. Veja exemplos [aqui](./policy-examples.md)
    
    Para a opção de upload de arquivo, a versão prévia da política será mostrada em formato de texto e a versão prévia da política não pode ser editada.

7.  Clique em **Atualizar** no menu superior para ver a política configurada

##### <a name="upload-policy-in-text-format"></a>Carregar política no formato de texto

1.  No menu do portal do Azure ou na home page, selecione **Todos os recursos**
2.  Na caixa de filtro, digite nome do provedor de atestado
3.  Selecione o provedor de atestado e navegue até a página de visão geral
4.  Clique em **Política** no menu de recursos esquerdo ou no painel inferior
5.  Clique em **Configurar** no menu superior
6.  Quando o provedor de atestado é criado sem requisito de assinatura de política, o usuário pode carregar uma política em formato **JWT** ou de **Texto**
7.  Selecione o **Formato de Política** como **Texto**
8.  Carregue o arquivo de política com conteúdo em formato de **Texto** ou insira o conteúdo da política na área de texto e clique em **Salvar**. Veja exemplos [aqui](./policy-examples.md)

    Para a opção de upload de arquivo, a versão prévia da política será mostrada em formato de texto e a versão prévia da política não pode ser editada.

8.  Clique em **Atualizar** para ver a política configurada

#### <a name="when-attestation-provider-is-created-with-policy-signing-requirement"></a>Quando o provedor de atestado é criado com o requisito de assinatura de política

##### <a name="upload-policy-in-jwt-format"></a>Carregar política no formato JWT

1.  No menu do portal do Azure ou na home page, selecione **Todos os recursos**
2.  Na caixa de filtro, digite nome do provedor de atestado
3.  Selecione o provedor de atestado e navegue até a página de visão geral
4.  Clique em **Política** no menu de recursos esquerdo ou no painel inferior
5.  Clique em **Configurar** no menu superior
6.  Quando o provedor de atestado é criado sem requisito de assinatura de política, o usuário pode carregar uma política somente no formato **JWT assinado**
7.  Carregue o arquivo de política em **formato JWT assinado** e clique em **Salvar**. Veja exemplos [aqui](./policy-examples.md)

    Para a opção de upload de arquivo, a versão prévia da política será mostrada em formato de texto e a versão prévia da política não pode ser editada.
    
8.  Clique em **Atualizar** para ver a política configurada

