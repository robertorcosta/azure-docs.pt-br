---
title: Habilitando um piloto de SSPR do Azure AD
description: Neste tutorial, você habilitará a redefinição de senha de autoatendimento do Azure AD para um grupo piloto de usuários
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 08/16/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: ecdde4ef12c6991fad53f2286ee462fec31606ae
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74846276"
---
# <a name="tutorial-complete-an-azure-ad-self-service-password-reset-pilot-roll-out"></a>Tutorial: Concluir uma distribuição piloto de redefinição de senha self-service do Azure AD

Neste tutorial, você habilitará uma distribuição piloto de SSPR (redefinição de senha de autoatendimento) do Azure AD em sua organização e testá-la usando uma conta que não seja de administrador.

É importante que todos os testes de redefinição de senha self-service sejam feitos com contas que não sejam de administrador. A Microsoft gerencia a política de redefinição de senha das contas de administrador e exige o uso de métodos de autenticação mais fortes. Essa política não permite o uso de perguntas e respostas de segurança e requer o uso de dois métodos para redefinição.

> [!div class="checklist"]
> * Habilitar a redefinição de senha por autoatendimento
> * Testar SSPR como usuário

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta de Administrador Global

## <a name="enable-self-service-password-reset"></a>Habilitar a redefinição de senha por autoatendimento

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta de Administrador Global.
1. Navegue até o **Azure Active Directory** e selecione **Redefinição de senha**.
1. Comece com um grupo piloto habilitando a senha de autoatendimento para um subconjunto de usuários de sua organização.
   * Na página **Propriedades**, na opção **Redefinição de Senha Self-Service Habilitada**, escolha **Selecionado** e selecione um grupo piloto.
      * Somente os membros de um grupo específico do Azure AD escolhido podem usar a funcionalidade de SSPR. É recomendável definir um grupo de usuários e usar essa configuração ao implantar essa funcionalidade para uma prova de conceito. Há suporte para o aninhamento de grupos de segurança.
      * Verifique se os usuários do grupo que você escolheu foram licenciados adequadamente.
   * Clique em **Salvar**
1. Na página **Métodos de autenticação**
   * Defina o **Número de métodos necessários para a redefinição** como **1**
   * Escolha quais **métodos disponíveis para os usuários** sua organização deseja habilitar. Para este tutorial, marque as caixas para habilitar **Email**, **Celular**, **Telefone do escritório**, **Notificação de aplicativo móvel** e **Código de aplicativo móvel**.
   * Clique em **Salvar**
1. Na página **Registro**
   * Selecione **Sim** em **Exigir que os usuários se registrem ao entrar**.
   * Defina **Número de dias antes que os usuários precisem reconfirmar suas informações de autenticação** como **180**.
   * Clique em **Salvar**
1. Na página **Notificações**
   * Defina a opção **Notificar os usuários sobre as redefinições de senha** como **Sim**.
   * Defina **Notificar todos os administradores quando outros administradores redefinirem suas próprias senhas** como **Sim**.
1. Na página **Personalização**
   * A Microsoft recomenda que você defina **Personalizar link de assistência técnica** como **Sim** e forneça um endereço de email ou URL da página da Web onde os usuários possam obter mais ajuda de sua organização no campo **URL ou email de assistência técnica personalizados**.
   * Neste tutorial, manteremos **Personalizar link de assistência técnica** definido como **Não**.

A redefinição de senha de autoatendimento agora está configurada para usuários de nuvem em seu grupo piloto.

## <a name="test-sspr-as-a-user"></a>Testar SSPR como usuário

Teste a redefinição de senha de autoatendimento usando um usuário de teste que não seja administrador e seja membro do grupo piloto. **Se você usar uma conta que tenha uma função Administrador atribuída, a quantidade e os métodos de autenticação poderão ser diferentes dos selecionados, pois a Microsoft gerencia a política de administrador.**

1. Abra uma nova janela do navegador no modo InPrivate ou anônimo.
1. Usando um registro de usuário de teste para redefinição de senha de autoatendimento usando o portal de registro localizado em [ https://aka.ms/ssprsetup](https://aka.ms/ssprsetup).
1. Usando o mesmo usuário de teste, navegue até o portal de redefinição de senha de autoatendimento [ https://aka.ms/sspr ](https://aka.ms/sspr) e tente redefinir sua senha usando as informações fornecidas na etapa anterior.
1. Você provavelmente conseguirá redefinir sua senha com êxito.

## <a name="clean-up-resources"></a>Limpar recursos

Se você decidir que não deseja mais usar a funcionalidade configurada como parte deste tutorial, faça a alteração a seguir.

1. Entre no [Portal do Azure](https://portal.azure.com).
1. Navegue até o **Azure Active Directory** e selecione **Redefinição de senha**.
1. Na página **Propriedades**, na opção **Redefinição da Senha de Autoatendimento Habilitada**, escolha **Nenhum**.
1. Clique em **Salvar**

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você habilitou a redefinição de senha de autoatendimento do Azure AD. Continue com o próximo tutorial para ver como uma infraestrutura do Active Directory Domain Services local pode ser integrada à experiência de redefinição de senha de autoatendimento.

> [!div class="nextstepaction"]
> [Habilitar integração local de write-back de SSPR](tutorial-enable-writeback.md)
