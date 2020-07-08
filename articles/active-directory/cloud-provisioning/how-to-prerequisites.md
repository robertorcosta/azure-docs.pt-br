---
title: Pré-requisitos para provisionamento de nuvem do Azure AD Connect no Azure Active Directory
description: Este artigo descreve os pré-requisitos e os requisitos de hardware para o provisionamento de nuvem.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/06/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: b7eb632405ef17ef4100503f30168c1207179f48
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85373855"
---
# <a name="prerequisites-for-azure-ad-connect-cloud-provisioning"></a>Pré-requisitos para o provisionamento em nuvem do Azure AD Connect
Este artigo oferece diretrizes sobre como escolher e usar o provisionamento de nuvem do Azure AD Connect como solução de identidade.



## <a name="cloud-provisioning-agent-requirements"></a>Requisitos do agente de provisionamento de nuvem
Você precisa dos itens a seguir para usar o provisionamento de nuvem do Azure AD Connect:
    
- Uma conta de administrador de identidade híbrida para seu locatário do Azure AD que não é um usuário convidado.
- Um servidor local para o agente de provisionamento com Windows 2012 R2 ou posterior.  Esse servidor deve ser um servidor de camada 0 com base no [modelo de camada administrativa Active Directory](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material).
- Configurações de firewall local.

>[!NOTE]
>Atualmente, o agente de provisionamento só pode ser instalado em servidores de idioma inglês. Não será possível instalar um pacote de idiomas em inglês em um servidor que não seja em inglês, pois essa não é uma solução alternativa válida. 

O restante do documento mostra instruções passo a passo para esses pré-requisitos.

### <a name="in-the-azure-active-directory-admin-center"></a>No centro de administração do Azure Active Directory

1. Crie uma conta de administrador de identidade híbrida somente em nuvem no seu locatário do Azure AD. Dessa forma, você pode gerenciar a configuração do seu locatário se seus serviços locais falhem ou ficarem indisponíveis. Saiba mais sobre como [Adicionar uma conta de administrador de identidade híbrida somente em nuvem](../active-directory-users-create-azure-portal.md). Finalizar essa etapa é essencial para garantir que você não seja bloqueado de seu locatário.
1. Adicione um ou mais [nomes de domínio personalizados](../active-directory-domains-add-azure-portal.md) ao seu locatário do Azure AD. Os usuários podem entrar com um desses nomes de domínio.

### <a name="in-your-directory-in-active-directory"></a>No seu diretório no Active Directory

Execute a [ferramenta IdFix](https://docs.microsoft.com/office365/enterprise/prepare-directory-attributes-for-synch-with-idfix) para preparar os atributos de diretório para sincronização.

### <a name="in-your-on-premises-environment"></a>Em seu ambiente local

1. Identificar um servidor de host ingressado no domínio que executa o Windows Server 2012 R2 ou superior com o mínimo de 4 GB de RAM e o runtime do .NET 4.7.1+.

1. A política de execução do PowerShell no servidor local deve ser definida como Undefined ou RemoteSigned.

1. Se houver um firewall entre os servidores e o Azure Active Directory, configure os seguintes itens:
   - Certifique-se de que os agentes possam fazer solicitações de *saída* para o Azure AD nas seguintes portas:

        | Número da porta | Como ele é usado |
        | --- | --- |
        | **80** | Baixa as listas de CRLs (certificados revogados) enquanto valida o certificado TLS/SSL.  |
        | **443** | Lida com toda a comunicação de saída com o serviço. |
        | **8080** (opcional) | Agentes relatarão seu status a cada 10 minutos através da porta 8080, se a porta 443 não estiver disponível. Esse status é exibido no portal do Azure Active Directory. |
     
   - Se o firewall impõe as regras de acordo com os usuários originadores, abra essas portas para o tráfego proveniente dos serviços Windows que são executados como um serviço de rede.
   - Se o firewall ou proxy permitir especificar sufixos seguros, adicione conexões a \*.msappproxy.net e \*.servicebus.windows.net. servicebus.windows.net. Caso contrário, permita o acesso aos [Intervalos de IP do datacenter do Azure](https://www.microsoft.com/download/details.aspx?id=41653), os quais são atualizados semanalmente.
   - Seus agentes devem acessar login.windows.net e login.microsoftonline.com para o registro inicial. Abra seu firewall para essas URLs também.
   - Para validação do certificado, desbloqueie as seguintes URLs: mscrl.microsoft.com:80, crl.microsoft.com:80, ocsp.msocsp.com:80 e www\.microsoft.com:80. Essas URLs são usadas para a validação de certificado com outros produtos da Microsoft, então você talvez já tenha essas URLs desbloqueadas.

>[!NOTE]
> Não há suporte para a instalação do agente de provisionamento de nuvem no Windows Server Core.


### <a name="additional-requirements"></a>Requisitos adicionais
- [Microsoft .NET Framework 4.7.1](https://www.microsoft.com/download/details.aspx?id=56116) 

#### <a name="tls-requirements"></a>Requisitos de TLS

>[!NOTE]
>O protocolo TLS fornece comunicações seguras. A alteração das configurações de TLS afeta toda a floresta. Para obter mais informações, consulte [Atualizar para habilitar TLS 1.1 e TLS 1.2 como protocolos seguros padrão no WinHTTP no Windows](https://support.microsoft.com/help/3140245/update-to-enable-tls-1-1-and-tls-1-2-as-default-secure-protocols-in-wi).

O Windows Server que hospeda o agente de provisionamento de nuvem do Azure AD Connect deve ter o TLS 1.2 habilitado antes da instalação.

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

