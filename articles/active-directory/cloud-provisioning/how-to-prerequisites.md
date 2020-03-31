---
title: Pré-requisitos para provisionamento de nuvem Azure AD Connect no Azure AD
description: Este artigo descreve os pré-requisitos e requisitos de hardware necessários para o provisionamento em nuvem.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/06/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 45648170f69d513b15e79cdd76f56e66bbc88bfa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80332076"
---
# <a name="prerequisites-for-azure-ad-connect-cloud-provisioning"></a>Pré-requisitos para provisionamento de nuvem Azure AD Connect
Este artigo fornece orientações sobre como escolher e usar o Azure Active Directory (Azure AD) Conectar o provisionamento em nuvem como sua solução de identidade.



## <a name="cloud-provisioning-agent-requirements"></a>Requisitos do agente de provisionamento em nuvem
Você precisa usar o provisionamento de nuvem do Azure AD Connect:
    
- Uma conta de administrador global para o seu inquilino Azure AD que não é um usuário convidado.
- Um servidor local para o agente de provisionamento com Windows 2012 R2 ou posterior.
- Configurações de firewall no local.

>[!NOTE]
>Atualmente, o agente de provisionamento só pode ser instalado em servidores de língua inglesa. Instalar um pacote de idioma inglês em um servidor não inglês não é uma solução alternativa válida e resultará na não instalação do agente. 

O restante do documento fornece instruções passo a passo para esses pré-requisitos.

### <a name="in-the-azure-active-directory-admin-center"></a>No centro de administração do Azure Active Directory

1. Crie uma conta de administrador global somente em nuvem no seu locatário do Azure AD. Dessa forma, você pode gerenciar a configuração do seu inquilino se seus serviços no local falharem ou ficarem indisponíveis. Saiba como [adicionar uma conta de administrador global somente](../active-directory-users-create-azure-portal.md)em nuvem . Terminar esta etapa é fundamental para garantir que você não fique trancado fora do seu inquilino.
1. Adicione um ou mais [nomes de domínio personalizados](../active-directory-domains-add-azure-portal.md) ao seu locatário do Azure AD. Os usuários podem entrar com um desses nomes de domínio.

### <a name="in-your-directory-in-active-directory"></a>Em seu diretório em Active Directory

Execute a [ferramenta IdFix](https://docs.microsoft.com/office365/enterprise/prepare-directory-attributes-for-synch-with-idfix) para preparar os atributos do diretório para sincronização.

### <a name="in-your-on-premises-environment"></a>Em seu ambiente local

1. Identifique um servidor host com um domínio executando o Windows Server 2012 R2 ou superior com um mínimo de 4 GB de RAM e o tempo de execução .NET 4.7.1+.

1. Se houver um firewall entre seus servidores e o Azure AD, configure os seguintes itens:
   - Certifique-se de que os agentes possam fazer solicitações de *saída* para o Azure AD nas seguintes portas:

        | Número da porta | Como ele é usado |
        | --- | --- |
        | **80** | Baixa as listas de revogação de certificados (CRLs) enquanto valida o certificado TLS/SSL.  |
        | **443** | Lida com toda a comunicação de saída com o serviço. |
        | **8080** (opcional) | Agentes relatarão seu status a cada 10 minutos através da porta 8080, se a porta 443 não estiver disponível. Este status é exibido no portal Azure AD. |
     
   - Se o firewall impõe as regras de acordo com os usuários originadores, abra essas portas para o tráfego proveniente dos serviços Windows que são executados como um serviço de rede.
   - Se o firewall ou proxy permitir que você especifique sufixos seguros, adicione conexões a \*.msappproxy.net e \*.servicebus.windows.net. Caso contrário, permita o acesso aos [Intervalos de IP do datacenter do Azure](https://www.microsoft.com/download/details.aspx?id=41653), os quais são atualizados semanalmente.
   - Seus agentes devem acessar login.windows.net e login.microsoftonline.com para o registro inicial. Abra seu firewall para essas URLs também.
   - Para validação de certificados, desbloqueie os seguintes\.URLs: mscrl.microsoft.com:80, crl.microsoft.com:80, ocsp.msocsp.com:80 e www microsoft.com:80. Esses URLs são usados para validação de certificados com outros produtos da Microsoft, então você pode já ter esses URLs desbloqueados.

### <a name="verify-the-port"></a>Verifique a porta
Para verificar se o Azure está ouvindo na porta 443 e se seu agente pode se comunicar com ele, use a seguinte URL:

https://aadap-portcheck.connectorporttest.msappproxy.net/ 

Este teste verifica se seus agentes podem se comunicar com o Azure pela porta 443. Abra um navegador e vá para a URL anterior do servidor onde o agente está instalado.

![Verificação da acessibilidade da porta](media/how-to-install/verify2.png)

### <a name="additional-requirements"></a>Requisitos adicionais
- [Microsoft .NET Framework 4.7.1](https://www.microsoft.com/download/details.aspx?id=56116) 

#### <a name="tls-requirements"></a>Requisitos de TLS

>[!NOTE]
>O TLS (Transport Layer Security, segurança da camada de transporte) é um protocolo que prevê comunicações seguras. A alteração das configurações do TLS afeta toda a floresta. Para obter mais informações, consulte [Atualizar para ativar o TLS 1.1 e o TLS 1.2 como protocolos seguros padrão no WinHTTP no Windows](https://support.microsoft.com/help/3140245/update-to-enable-tls-1-1-and-tls-1-2-as-default-secure-protocols-in-wi).

O servidor Windows que hospeda o agente de provisionamento em nuvem Azure AD Connect deve ter o TLS 1.2 ativado antes de instalá-lo.

Para habilitar o TLS 1.2, siga estas etapas.

1. Defina as seguintes chaves do registro:
    
    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319] "SchUseStrongCrypto"=dword:00000001
    ```

1. Reinicie o servidor.


## <a name="next-steps"></a>Próximas etapas 

- [O que é provisionamento?](what-is-provisioning.md)
- [O que é o provisionamento em nuvem do Azure AD Connect?](what-is-cloud-provisioning.md)

