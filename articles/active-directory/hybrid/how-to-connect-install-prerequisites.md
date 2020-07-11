---
title: 'Azure AD Connect: pré-requisitos e hardware | Microsoft Docs'
description: Este tópico descreve os pré-requisitos e requisitos de hardware para o Azure AD Connect
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 91b88fda-bca6-49a8-898f-8d906a661f07
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 06/25/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9bd19093034b4427d9e1b637a653a90e0568cddf
ms.sourcegitcommit: f844603f2f7900a64291c2253f79b6d65fcbbb0c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2020
ms.locfileid: "86223917"
---
# <a name="prerequisites-for-azure-ad-connect"></a>Pré-requisitos do Azure AD Connect
Este tópico descreve os pré-requisitos e requisitos de hardware para o Azure AD Connect.

## <a name="before-you-install-azure-ad-connect"></a>Antes de instalar o Azure AD Connect
Antes de instalar o Azure AD Connect, aqui estão algumas coisas de que você precisará.

### <a name="azure-ad"></a>Azure AD
* Um locatário do Azure AD. Você recebe uma [avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/). Você pode usar um dos seguintes portais para gerenciar o Azure AD Connect:
  * O [portal do Azure](https://portal.azure.com).
  * O [portal do Office](https://portal.office.com).  
* [Adicione e verifique o domínio](../active-directory-domains-add-azure-portal.md) que você planeja usar no AD do Azure. Por exemplo, se você planeja usar contoso.com para os usuários, em seguida, verifique se este domínio foi verificado e se não está usando apenas o domínio padrão contoso.onmicrosoft.com.
* Um locatário do Azure AD pode ter, por padrão, 50 mil objetos. Quando você verificar seu domínio, o limite aumentará para 300 mil objetos. Se precisar de mais objetos no Azure AD, precisará abrir um caso de suporte para aumentar ainda mais o limite. Se você precisar de mais de 500 mil objetos, precisará de uma licença, como Office 365, Azure AD Básico, Azure AD Premium ou Enterprise Mobility and Security.

### <a name="prepare-your-on-premises-data"></a>Preparar seus dados locais
* Usar [IdFix](https://support.office.com/article/Install-and-run-the-Office-365-IdFix-tool-f4bd2439-3e41-4169-99f6-3fabdfa326ac) para identificar erros como duplicatas e problemas de formatação no diretório antes de sincronizar para o Azure AD e o Office 365.
* Consulte os [recursos de sincronização opcionais que você pode habilitar no Azure AD](how-to-connect-syncservice-features.md) e avalie quais recursos você deve habilitar.

### <a name="on-premises-active-directory"></a>Active Directory local
* A versão de esquema do AD e o nível funcional de floresta devem ser o Windows Server 2003 ou posterior. Os controladores de domínio podem executar qualquer versão, desde os requisitos de nível de floresta e de esquema sejam atendidos.
* Se você pretende usar o recurso **write-back de senha**, os Controladores de Domínio devem estar no Windows Server 2008 R2 ou posterior.
* O controlador de domínio usado pelo Azure AD deve ser gravável. **Não há suporte** para o uso de um RODC (controlador de domínio somente leitura) e Azure ad Connect não segue redirecionamentos de gravação.
* **Não há suporte** para o uso de florestas/domínios locais que usam nomes NetBios “com pontos” (nome que contém um ponto “.”).
* É recomendável [habilitar a lixeira do Active Directory](how-to-connect-sync-recycle-bin.md).

### <a name="azure-ad-connect-server"></a>Servidor do Azure AD Connect
O servidor de Azure AD Connect contém dados de identidade críticos. É importante que o acesso administrativo a esse servidor seja devidamente protegido, seguindo as diretrizes conforme documentado em [protegendo o acesso privilegiado](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access). 

O servidor de Azure AD Connect deve ser tratado como um componente de camada 0, conforme documentado no [modelo de camada administrativa Active Directory](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material).  

Para saber mais sobre como proteger seu ambiente de Active Directory, consulte [práticas recomendadas para proteger Active Directory](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/best-practices-for-securing-active-directory).

#### <a name="installation-prerequisites"></a>Pré-requisitos de instalação 

- Azure AD Connect deve ser instalado em um domínio ingressado no Windows Server 2012 ou posterior. É altamente recomendável que esse servidor seja um controlador de domínio. 
- O Azure AD Connect não pode ser instalado no Small Business Server ou no Windows Server Essentials anteriores a 2019 (o Windows Server Essentials 2019 é compatível). O servidor deve estar usando o Windows Server standard ou superior.  
- O servidor do Azure AD Connect deve ter uma GUI completa instalada. Não há suporte para a instalação do Azure AD Connect no Windows Server Core. 
- O servidor de Azure AD Connect não deve ter a transcrição do PowerShell Política de Grupo habilitada se você estiver usando Azure AD Connect assistente para gerenciar a configuração do ADFS. Você poderá habilitar a transcrição do PowerShell se estiver usando o assistente do Azure AD Connect para gerenciar a configuração de sincronização. 
- Se Serviços de Federação do Active Directory (AD FS) estiver sendo implantado, então: 
    - os servidores nos quais AD FS ou proxy de aplicativo Web estão instalados devem ser Windows Server 2012 R2 ou posterior. O gerenciamento remoto do Windows deve estar habilitado nesses servidores para instalação remota. 
    - Você deve configurar certificados TLS/SSL.  Consulte [Gerenciando protocolos SSL/TLS e conjuntos de codificação para AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/manage-ssl-protocols-in-ad-fs) e [gerenciar certificados SSL no AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/manage-ssl-certificates-ad-fs-wap).
    - Você deve configurar a resolução de nome. 
- Se os administradores globais tiverem a MFA habilitada, a URL https://secure.aadcdn.microsoftonline-p.com **deverá** estar na lista de sites confiáveis. Você deverá adicionar esse site à lista de sites confiáveis quando receber um desafio de MFA e ele não tiver sido adicionado antes. Você pode usar o Internet Explorer para adicioná-la aos seus sites confiáveis.  

#### <a name="hardening-your-azure-ad-connect-server"></a>Protegendo seu servidor de Azure AD Connect 
A Microsoft recomenda fortalecer o servidor do Azure AD Connect para diminuir a superfície de ataque de segurança desse componente crítico de seu ambiente de TI. Seguir as recomendações abaixo ajudará a reduzir alguns riscos de segurança para sua organização.

- Você deve tratar o Azure AD Connect o mesmo que um controlador de domínio e outros recursos da camada 0:https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material 
- Você deve restringir o acesso administrativo ao servidor de Azure AD Connect somente a administradores de domínio ou a outros grupos de segurança rigidamente controlados.
- Você deve criar uma [conta dedicada para todos os funcionários com acesso privilegiado](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access). Os administradores não devem navegar na Web, verificar emails e realizar tarefas de produtividade cotidianas com contas altamente privilegiadas.
- Você deve seguir as diretrizes conforme fornecido em [protegendo o acesso privilegiado](https://docs.microsoft.com/windows-server/security/credentials-protection-and-management/how-to-configure-protected-accounts). 
- Você deve garantir que cada computador tenha uma senha de administrador local exclusiva. A [solução de senha de administrador local (LAPs)](https://support.microsoft.com/help/3062591/microsoft-security-advisory-local-administrator-password-solution-laps) pode configurar senhas aleatórias exclusivas em cada estação de trabalho e servidor armazená-las no Active Directory (AD) protegido por uma ACL. Somente usuários autorizados qualificados podem ler ou solicitar a redefinição dessas senhas de conta de administrador local. Você pode obter as interrupções para uso em estações de trabalho e servidores do [centro de download da Microsoft](https://www.microsoft.com/download/details.aspx?id=46899#:~:text=The%20%22Local%20Administrator%20Password%20Solution,it%20or%20request%20its%20reset.). Diretrizes adicionais para operar um ambiente com LAPSos e PAWs podem ser encontradas em [padrões operacionais com base no princípio de origem limpa](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material#operational-standards-based-on-clean-source-principle). 
- Você deve implementar [Paw (estações de trabalho de acesso privilegiado)](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations) dedicadas para todos os funcionários com acesso privilegiado aos sistemas de informações da sua organização. 
- Você deve seguir estas [diretrizes adicionais](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/reducing-the-active-directory-attack-surface) para reduzir a superfície de ataque do ambiente Active Directory.


### <a name="sql-server-used-by-azure-ad-connect"></a>SQL Server usado pelo Azure AD Connect
* O Azure AD Connect requer um banco de dados do SQL Server para armazenar dados de identidade. Por padrão, um SQL Server 2012 Express LocalDB (uma versão leve do SQL Server Express) é instalado. O SQL Server Express tem um limite de tamanho de 10GB que permite que você gerencie aproximadamente 100.000 objetos. Se precisar gerenciar um volume maior de objetos de diretório, você precisa apontar o assistente de instalação para uma instalação diferente do SQL Server. O tipo de instalação SQL Server pode afetar o [desempenho do Azure ad Connect](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-performance-factors#sql-database-factors).
* Se você usar uma instalação diferente do SQL Server, esses requisitos se aplicarão:
  * O Azure AD Connect dá suporte a todas as versões do Microsoft SQL Server do 2012 (com o Service Pack mais recente) para SQL Server 2019. O Banco de Dados SQL do Microsoft Azure **não tem suporte** como banco de dados.
  * Deve usar uma ordenação de SQL que não diferencia maiúsculas de minúsculas. Essas ordenações são identificadas com um \_CI_ no nome. **Não há suporte** para a utilização de uma ordenação de maiúsculas e minúsculas, identificado por \_CS_ em seu nome.
  * Você só pode ter um mecanismo de sincronização por instância do SQL. **Não há suporte** para compartilhar uma instância do SQL com FIM/MIM Sync, DirSync ou Azure AD Sync.

### <a name="accounts"></a>Contas
* Uma conta de Administrador Global do Azure AD para o locatário do Azure AD ao qual você deseja se integrar. Essa conta deve ser uma **conta corporativa ou de estudante** e não pode ser uma **conta da Microsoft**.
* Se você usar [as configurações expressas](reference-connect-accounts-permissions.md#express-settings-installation) ou atualizar do DirSync, deverá ter uma conta de administrador corporativo para seu Active Directory local.
* Se você usar o caminho de instalação de configurações personalizadas, terá mais opções. Para obter mais informações, consulte [configurações de instalação personalizadas](reference-connect-accounts-permissions.md#custom-installation-settings).

### <a name="connectivity"></a>Conectividade
* O servidor do Azure AD Connect precisa da resolução de DNS para a intranet e a Internet. O servidor DNS deve conseguir resolver nomes tanto para o Active Directory local quanto para os pontos de extremidade do Azure AD.
* Se você tiver firewalls na Intranet e precisar abrir portas entre os servidores do Azure AD Connect e seus controladores de domínio, confira [Portas de conexão do Azure AD](reference-connect-ports.md) para saber mais.
* Se o proxy ou o firewall limitar as URLs que podem ser acessadas, as URLs documentadas em [URLs e intervalos de endereços IP do Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) deverão ser abertas.
  * Se você estiver usando o Microsoft Cloud na Alemanha ou a nuvem do Microsoft Azure Governamental, veja [Considerações de instâncias do serviço de sincronização do Azure AD Connect](reference-connect-instances.md) para URLs.
* O Azure AD Connect (versão 1.1.614.0 ou superior) usa o TLS 1.2 por padrão para criptografar a comunicação entre o mecanismo de sincronização e o Azure AD. Se TLS 1.2 não estiver disponível no sistema operacional subjacente, o Azure AD Connect reverterá progressivamente para protocolos mais antigos (TLS 1.1 e TLS 1.0).
* Antes da versão 1.1.614.0, o Azure AD Connect usa TLS 1.0 por padrão para criptografar a comunicação entre o mecanismo de sincronização e o Azure AD. Para mudar para TLS 1.2, siga as etapas em [Habilitar TLS 1.2 no Azure Connect AD](#enable-tls-12-for-azure-ad-connect).
* Se você estiver usando um proxy de saída para conectar-se à Internet, a seguinte configuração no arquivo **C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config** deverá ser adicionada para que o assistente de instalação e a sincronização do Azure AD Connect possam se conectar à Internet e ao Azure AD. Esse texto deve ser inserido na parte inferior do arquivo. Neste código, &lt;PROXYADDRESS&gt; representa o nome de host ou o endereço IP do proxy real.

```
    <system.net>
        <defaultProxy>
            <proxy
            usesystemdefault="true"
            proxyaddress="http://<PROXYADDRESS>:<PROXYPORT>"
            bypassonlocal="true"
            />
        </defaultProxy>
    </system.net>
```

* Se o servidor proxy precisar de autenticação, a [conta de serviço](reference-connect-accounts-permissions.md#adsync-service-account) deverá estar localizada no domínio e você deverá usar o caminho de instalação das configurações personalizadas para especificar uma [conta de serviço personalizada](how-to-connect-install-custom.md#install-required-components). Você também precisa de uma alteração diferente para machine.config. Com essa alteração no machine.config, o assistente de instalação e o mecanismo de sincronização respondem às solicitações de autenticação do servidor proxy. Em todas as páginas do assistente de instalação, com exceção da página **Configurar**, as credenciais do usuário conectado são usadas. Na página **Configurar** no final do assistente de instalação, o contexto é alternado para a [conta de serviço](reference-connect-accounts-permissions.md#adsync-service-account) que foi criada por você. A seção machine.config deve ter esta aparência.

```
    <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
            <proxy
            usesystemdefault="true"
            proxyaddress="http://<PROXYADDRESS>:<PROXYPORT>"
            bypassonlocal="true"
            />
        </defaultProxy>
    </system.net>
```

* Quando o Azure AD Connect envia uma solicitação da Web ao Azure AD como parte da sincronização de diretório, o Azure AD pode levar até 5 minutos para responder. É comum que servidores de proxy tenham uma configuração de tempo limite de ociosidade da conexão. A configuração deve ser definida em pelo menos 6 minutos.

Para obter mais informações, consulte o MSDN sobre o [Elemento proxy padrão](https://msdn.microsoft.com/library/kd3cf2ex.aspx).  
Para obter mais informações quando você tiver problemas de conectividade, consulte [Solucionar problemas de conectividade](tshoot-connect-connectivity.md).

### <a name="other"></a>Outros
* Opcional: uma conta de usuário de teste para verificar a sincronização.

## <a name="component-prerequisites"></a>Pré-requisitos do componente
### <a name="powershell-and-net-framework"></a>PowerShell e .NET Framework
O Azure AD Connect depende do Microsoft PowerShell e do .NET Framework 4.5.1. Você precisa desta versão ou de uma versão posterior instalada no seu servidor. Dependendo da versão do Windows Server, faça o seguinte:

* Windows Server 2012R2
  * O Microsoft PowerShell é instalado por padrão. Nenhuma ação é necessária.
  * .NET Framework 4.5.1 e versões posteriores são oferecidas pelo Windows Update. Verifique se você instalou as atualizações mais recentes para o Windows Server no painel de controle.
* Windows Server 2012
  * A versão mais recente do Microsoft PowerShell está disponível no **Windows Management Framework 4.0**, disponível no [Centro de Download da Microsoft](https://www.microsoft.com/downloads).
  * O .NET Framework 4.5.1 e versões posteriores estão disponíveis no [Centro de Download da Microsoft](https://www.microsoft.com/downloads).


### <a name="enable-tls-12-for-azure-ad-connect"></a>Habilitar TLS 1.2 no Azure Connect AD
Antes da versão 1.1.614.0, o Azure AD Connect usa TLS 1.0 por padrão para criptografar a comunicação entre o servidor do mecanismo de sincronização e o Azure AD. Você pode alterar isso Configurando aplicativos .NET para usar o TLS 1,2 por padrão no servidor. Saiba mais sobre o TLS 1.2 em [Microsoft Security Advisory 2960358 ](https://technet.microsoft.com/security/advisory/2960358).

1.  Verifique se você tem o hotfix do .NET 4.5.1 instalado para seu sistema operacional, consulte [o comunicado de segurança da Microsoft 2960358](https://technet.microsoft.com/security/advisory/2960358). É possível ter esse hotfix ou uma versão posterior já instalada no servidor.
    ```
2. For all operating systems, set this registry key and restart the server.
    ```
    HKEY_LOCAL_MACHINE \SOFTWARE\Microsoft \. NETFramework\v4.0.30319 "SchUseStrongCrypto" = DWORD: 00000001
    ```
4. If you also want to enable TLS 1.2 between the sync engine server and a remote SQL Server, then make sure you have the required versions installed for [TLS 1.2 support for Microsoft SQL Server](https://support.microsoft.com/kb/3135244).

## Prerequisites for federation installation and configuration
### Windows Remote Management
When using Azure AD Connect to deploy Active Directory Federation Services or the Web Application Proxy, check these requirements:

* If the target server is domain joined, then ensure that Windows Remote Managed is enabled
  * In an elevated PowerShell command window, use command `Enable-PSRemoting –force`
* If the target server is a non-domain joined WAP machine, then there are a couple of additional requirements
  * On the target machine (WAP machine):
    * Ensure the winrm (Windows Remote Management / WS-Management) service is running via the Services snap-in
    * In an elevated PowerShell command window, use command `Enable-PSRemoting –force`
  * On the machine on which the wizard is running (if the target machine is non-domain joined or untrusted domain):
    * In an elevated PowerShell command window, use the command `Set-Item WSMan:\localhost\Client\TrustedHosts –Value <DMZServerFQDN> -Force –Concatenate`
    * In Server Manager:
      * add DMZ WAP host to machine pool (server manager -> Manage -> Add Servers...use DNS tab)
      * Server Manager All Servers tab: right click WAP server and choose Manage As..., enter local (not domain) creds for the WAP machine
      * To validate remote PowerShell connectivity, in the Server Manager All Servers tab: right click WAP server and choose Windows PowerShell. A remote PowerShell session should open to ensure remote PowerShell sessions can be established.

### TLS/SSL Certificate Requirements
* It's strongly recommended to use the same TLS/SSL certificate across all nodes of your AD FS farm and all Web Application proxy servers.
* The certificate must be an X509 certificate.
* You can use a self-signed certificate on federation servers in a test lab environment. However, for a production environment, we recommend that you obtain the certificate from a public CA.
  * If using a certificate that is not publicly trusted, ensure that the certificate installed on each Web Application Proxy server is trusted on both the local server and on all federation servers
* The identity of the certificate must match the federation service name (for example, sts.contoso.com).
  * The identity is either a subject alternative name (SAN) extension of type dNSName or, if there are no SAN entries, the subject name specified as a common name.  
  * Multiple SAN entries can be present in the certificate, provided one of them matches the federation service name.
  * If you are planning to use Workplace Join, an additional SAN is required with the value **enterpriseregistration.** followed by the User Principal Name (UPN) suffix of your organization, for example, **enterpriseregistration.contoso.com**.
* Certificates based on CryptoAPI next generation (CNG) keys and key storage providers are not supported. This means you must use a certificate based on a CSP (cryptographic service provider) and not a KSP (key storage provider).
* Wild-card certificates are supported.

### Name resolution for federation servers
* Set up DNS records for the AD FS federation service name (for example sts.contoso.com) for both the intranet (your internal DNS server) and the extranet (public DNS through your domain registrar). For the intranet DNS record, ensure that you use A records and not CNAME records. This is required for windows authentication to work correctly from your domain joined machine.
* If you are deploying more than one AD FS server or Web Application Proxy server, then ensure that you have configured your load balancer and that the DNS records for the AD FS federation service name (for example sts.contoso.com) point to the load balancer.
* For windows integrated authentication to work for browser applications using Internet Explorer in your intranet, ensure that the AD FS federation service name (for example sts.contoso.com) is added to the intranet zone in IE. This can be controlled via group policy and deployed to all your domain joined computers.

## Azure AD Connect supporting components
The following is a list of components that Azure AD Connect installs on the server where Azure AD Connect is installed. This list is for a basic Express installation. If you choose to use a different SQL Server on the Install synchronization services page, then SQL Express LocalDB is not installed locally.

* Azure AD Connect Health
* Microsoft SQL Server 2012 Command Line Utilities
* Microsoft SQL Server 2012 Express LocalDB
* Microsoft SQL Server 2012 Native Client
* Microsoft Visual C++ 2013 Redistribution Package

## Hardware requirements for Azure AD Connect
The table below shows the minimum requirements for the Azure AD Connect sync computer.

| Number of objects in Active Directory | CPU | Memory | Hard drive size |
| --- | --- | --- | --- |
| Fewer than 10,000 |1.6 GHz |4 GB |70 GB |
| 10,000–50,000 |1.6 GHz |4 GB |70 GB |
| 50,000–100,000 |1.6 GHz |16 GB |100 GB |
| For 100,000 or more objects the full version of SQL Server is required | | | |
| 100,000–300,000 |1.6 GHz |32 GB |300 GB |
| 300,000–600,000 |1.6 GHz |32 GB |450 GB |
| More than 600,000 |1.6 GHz |32 GB |500 GB |

The minimum requirements for computers running AD FS or Web Application Proxy Servers is the following:

* CPU: Dual core 1.6 GHz or higher
* MEMORY: 2 GB or higher
* Azure VM: A2 configuration or higher

## Next steps
Learn more about [Integrating your on-premises identities with Azure Active Directory](whatis-hybrid-identity.md).
