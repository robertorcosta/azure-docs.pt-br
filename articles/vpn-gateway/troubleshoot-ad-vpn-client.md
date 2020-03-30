---
title: 'VPN Gateway: Solucionar problemas cliente VPN - Autenticação Azure AD'
description: Solucionar problemas CLIENTES de autenticação DO VPN Gateway P2S Azure
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: 8871e92f0911c4d3cbcc1772bef1daeb5c70b5d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74151961"
---
# <a name="troubleshoot-an-azure-ad-authentication-vpn-client"></a>Solucionar problemas de um cliente VPN de autenticação Azure AD

Este artigo ajuda você a solucionar problemas para que um cliente VPN se conecte a uma rede virtual usando VPN point-to-site e autenticação do Azure Active Directory.

## <a name="view-status-log"></a><a name="status"></a>Exibir registro de status

Exibir o registro de status para mensagens de erro.

![logs](./media/troubleshoot-ad-vpn-client/1.png)

1. Clique no ícone setas no canto inferior direito da janela do cliente para mostrar os **registros de status**.
2. Verifique se há erros nos registros que possam indicar o problema.
3. As mensagens de erro são exibidas em vermelho.

## <a name="clear-sign-in-information"></a><a name="clear"></a>Informações claras sobre login

Limpe as informações de login.

![entrar](./media/troubleshoot-ad-vpn-client/2.png)

1. Selecione o ... ao lado do perfil que você deseja solucionar problemas. Selecione **Configurar -> Conta Clara Salva.**
2. Selecione **Salvar**.
3. Tente se conectar.
4. Se a conexão ainda falhar, continue até a próxima seção.

## <a name="run-diagnostics"></a><a name="diagnostics"></a>Executar diagnóstico

Execute diagnósticos no cliente VPN.

![diagnóstico](./media/troubleshoot-ad-vpn-client/3.png)

1. Clique no **...** ao lado do perfil que você deseja executar diagnósticos. Selecione **Diagnosticar -> executar diagnóstico**.
2. O cliente executará uma série de testes e exibirá o resultado do teste

   * Acesso à Internet – Verifica se o cliente tem conectividade com a Internet
   * Credenciais do cliente – Verifique se o ponto final de autenticação do Diretório Ativo do Azure é acessível
   * Servidor Resolutável – Contate o servidor DNS para resolver o endereço IP do servidor VPN configurado
   * Servidor Acessível – Verifica se o servidor VPN está respondendo ou não
3. Se algum dos testes falhar, entre em contato com o administrador da rede para resolver o problema.
4. A próxima seção mostra como coletar os registros, se necessário.

## <a name="collect-client-log-files"></a><a name="logfiles"></a>Coletar arquivos de registro de clientes

Colete os arquivos de registro do cliente VPN. Os arquivos de log podem ser enviados para suporte/administrador através de um método de sua escolha. Por exemplo, e-mail.

1. Clique no "..." ao lado do perfil que você deseja executar diagnósticos. Selecione **O diretório 'Exibir >'s Show Logs .**

   ![mostrar logs](./media/troubleshoot-ad-vpn-client/4.png)
2. O Windows Explorer abre para a pasta que contém os arquivos de log.

   ![ver arquivo](./media/troubleshoot-ad-vpn-client/5.png)

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte [Criar um inquilino do Azure Active Directory para conexões P2S Open VPN que usam autenticação Azure AD](openvpn-azure-ad-tenant.md).