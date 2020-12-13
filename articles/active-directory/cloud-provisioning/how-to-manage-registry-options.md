---
title: 'Agente de provisionamento de nuvem Azure AD Connect: Gerenciar opções de registro | Microsoft Docs'
description: Este artigo descreve como gerenciar opções de registro no agente de provisionamento de Azure AD Connect Cloud.
services: active-directory
documentationcenter: ''
author: cmmdesai
manager: daveba
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/11/2020
ms.subservice: hybrid
ms.author: chmutali
ms.collection: M365-identity-device-management
ms.openlocfilehash: ad3bd938355d138e660958e34d046d7af03e75c7
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/13/2020
ms.locfileid: "97371072"
---
# <a name="manage-agent-registry-options"></a>Gerenciar opções de registro do agente

Esta seção descreve as opções de registro que você pode definir para controlar o comportamento de processamento em tempo de execução do agente de provisionamento do Azure AD Connect. 

## <a name="configure-ldap-connection-timeout"></a>Configurar tempo limite de conexão LDAP
Ao executar operações LDAP em Active Directory controladores de domínio configurados, por padrão, o agente de provisionamento usa o valor de tempo limite de conexão padrão de 30 segundos. Se o controlador de domínio levar mais tempo para responder, você poderá ver a seguinte mensagem de erro no arquivo de log do agente: 

`
System.DirectoryServices.Protocols.LdapException: The operation was aborted because the client side timeout limit was exceeded.
`

As operações de pesquisa LDAP podem levar mais tempo se o atributo de pesquisa não estiver indexado. Como uma primeira etapa, se você receber o erro acima, primeiro verifique se o atributo de pesquisa/pesquisa está [indexado](https://docs.microsoft.com/windows/win32/ad/indexed-attributes). Se os atributos de pesquisa forem indexados e o erro persistir, você poderá aumentar o tempo limite de conexão LDAP usando as seguintes etapas: 

1. Faça logon como administrador no Windows Server que executa o agente de provisionamento do Azure AD Connect.
1. Use o item de menu *executar* para abrir o editor do registro (regedit.exe) 
1. Localize a pasta de chave **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure AD Connect Agents\Azure AD Connect Provisioning Agent**
1. Clique com o botão direito do mouse e selecione "novo > valor da cadeia de caracteres"
1. Forneça o nome: `LdapConnectionTimeoutInMilliseconds`
1. Clique duas vezes no **nome do valor** e insira os dados do valor como `60000` milissegundos.
    > [!div class="mx-imgBorder"]
    > ![Tempo limite de conexão LDAP](media/how-to-manage-registry-options/ldap-connection-timeout.png)
1. Reinicie o serviço de provisionamento de Azure AD Connect no console de *Serviços* .
1. Se você tiver implantado vários agentes de provisionamento, aplique essa alteração de registro a todos os agentes para fins de consistência. 

## <a name="configure-referral-chasing"></a>Configurar a caça de referência
Por padrão, o agente de provisionamento do Azure AD Connect não procura [referências](https://docs.microsoft.com/windows/win32/ad/referrals). Talvez você queira habilitar a busca de referência, para dar suporte a determinados cenários de provisionamento de entrada de RH, como: 
* Verificando a exclusividade do UPN em vários domínios
* Resolvendo referências do Gerenciador de domínio cruzado

Use as etapas a seguir para ativar a busca de referência:

1. Faça logon como administrador no Windows Server que executa o agente de provisionamento do Azure AD Connect.
1. Use o item de menu *executar* para abrir o editor do registro (regedit.exe) 
1. Localize a pasta de chave **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure AD Connect Agents\Azure AD Connect Provisioning Agent**
1. Clique com o botão direito do mouse e selecione "novo > valor da cadeia de caracteres"
1. Forneça o nome: `ReferralChasingOptions`
1. Clique duas vezes no **nome do valor** e insira os dados do valor como `96` . Esse valor corresponde ao valor constante para `ReferralChasingOptions.All` e especifica que a subárvore e as referências de nível base serão seguidas pelo agente. 
    > [!div class="mx-imgBorder"]
    > ![Caça de referência](media/how-to-manage-registry-options/referral-chasing.png)
1. Reinicie o serviço de provisionamento de Azure AD Connect no console de *Serviços* .
1. Se você tiver implantado vários agentes de provisionamento, aplique essa alteração de registro a todos os agentes para fins de consistência.

> [!NOTE]
> Você pode confirmar se as opções de registro foram definidas habilitando o [log detalhado](how-to-troubleshoot.md#log-files). Os logs emitidos durante a inicialização do agente exibirão os valores de configuração selecionados do registro. 

## <a name="next-steps"></a>Próximas etapas 

- [O que é provisionamento?](what-is-provisioning.md)
- [O que é o provisionamento em nuvem do Azure AD Connect?](what-is-cloud-provisioning.md)

