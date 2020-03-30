---
title: Use restrições de inquilinos para gerenciar o acesso aos aplicativos SaaS - Azure AD
description: Como usar as restrições de inquilinos para gerenciar quais usuários podem acessar aplicativos com base em seu inquilino Azure AD.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: mimart
ms.reviewer: richagi
ms.collection: M365-identity-device-management
ms.openlocfilehash: ecd49b340810f92727f0fc98f84031c8cbf68179
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481170"
---
# <a name="use-tenant-restrictions-to-manage-access-to-saas-cloud-applications"></a>Use restrições de inquilinos para gerenciar o acesso a aplicativos em nuvem SaaS

Organizações de grande porte que enfatizam a segurança desejam mudar para serviços de nuvem como o Office 365, mas precisam saber que seus usuários poderão acessar somente os recursos aprovados. Tradicionalmente, as empresas restringem endereços IP ou nomes de domínio quando desejam gerenciar o acesso. Essa abordagem falha em um mundo onde os aplicativos de software como serviço (ou SaaS) estão hospedados em uma nuvem pública, rodando em nomes de domínio compartilhados como [outlook.office.com](https://outlook.office.com/) e [login.microsoftonline.com](https://login.microsoftonline.com/). Bloquear esses endereços impediria totalmente que os usuários acessassem o Outlook na web, em vez de simplesmente restringi-los a identidades e recursos aprovados.

A solução Azure Active Directory (Azure AD) para este desafio é um recurso chamado restrições de inquilinos. Com as restrições de inquilinos, as organizações podem controlar o acesso aos aplicativos em nuvem SaaS, com base no inquilino Azure AD que os aplicativos usam para logon único. Por exemplo, você pode desejar permitir o acesso aos aplicativos do Office 365 da sua organização enquanto impede o acesso a instâncias de outras organizações desses mesmos aplicativos.  

Com as restrições de inquilinos, as organizações podem especificar a lista de inquilinos que seus usuários podem acessar. O Azure AD então apenas concede acesso para esses locatários permitidos.

Este artigo se concentra nas restrições de inquilinos para o Office 365, mas o recurso deve funcionar com qualquer aplicativo em nuvem SaaS que use protocolos de autenticação modernos com o Azure AD para cadastro único. Se você usar aplicativos de SaaS com um locatário do Azure AD diferente do usado pelo Office 365, certifique-se de que todos os locatários necessários sejam permitidos. Para obter mais informações sobre os aplicativos de nuvem de SaaS, consulte o [Marketplace do Active Directory](https://azure.microsoft.com/marketplace/active-directory/).

## <a name="how-it-works"></a>Como ele funciona

A solução geral inclui os seguintes componentes:

1. **Azure AD**: `Restrict-Access-To-Tenants: <permitted tenant list>` Se o presente estiver, o Azure AD apenas emite tokens de segurança para os inquilinos permitidos.

2. **Infra-estrutura de servidor proxy no local**: Essa infra-estrutura é um dispositivo proxy capaz de inspeção TLS (Transport Layer Security, segurança de camada de transporte). Você deve configurar o proxy para inserir o cabeçalho contendo a lista de inquilinos permitidos no tráfego destinado ao Azure AD.

3. **Software do cliente**: Para suportar as restrições de inquilinos, o software do cliente deve solicitar tokens diretamente do Azure AD, para que a infra-estrutura proxy possa interceptar o tráfego. Os aplicativos office 365 baseados em navegador atualmente suportam restrições de inquilinos, assim como clientes do Office que usam autenticação moderna (como o OAuth 2.0).

4. **Autenticação moderna**: Os serviços em nuvem devem usar autenticação moderna para usar restrições de inquilinos e bloquear o acesso a todos os inquilinos não permitidos. Você deve configurar os serviços de nuvem do Office 365 para usar protocolos de autenticação modernos por padrão. Para obter as informações mais recentes sobre o suporte do Office 365 para autenticação moderna, leia [Updated Office 365 modern authentication](https://www.microsoft.com/en-us/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/) (Autenticação moderna do Office 365 atualizada).

O diagrama a seguir ilustra o fluxo do tráfego de alto nível. As restrições de inquilinos exigem a inspeção do TLS apenas no tráfego para o Azure AD, não para os serviços de nuvem do Office 365. Essa distinção é importante, porque o volume de tráfego para autenticação do Azure AD é tipicamente muito menor do que o volume de tráfego para aplicativos SaaS como Exchange Online e SharePoint Online.

![Restrições de inquilino saem do fluxo de tráfego - diagrama](./media/tenant-restrictions/traffic-flow.png)

## <a name="set-up-tenant-restrictions"></a>Configurar restrições de inquilinos

Há dois passos para começar com as restrições dos inquilinos. Primeiro, certifique-se de que seus clientes podem se conectar aos endereços certos. Em segundo lugar, configure sua infra-estrutura proxy.

### <a name="urls-and-ip-addresses"></a>URLs e endereços IP

Para usar as restrições de inquilinos, seus clientes devem ser capazes de se conectar aos seguintes URLs AD do Azure para autenticar: [login.microsoftonline.com,](https://login.microsoftonline.com/) [login.microsoft.com](https://login.microsoft.com/)e [login.windows.net](https://login.windows.net/). Além disso, para acessar o Office 365, seus clientes também devem ser capazes de se conectar aos nomes de domínio (FQDNs), URLs e endereços IP definidos nas [faixas de URLs e endereços IP do Office 365.](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) 

### <a name="proxy-configuration-and-requirements"></a>Requisitos e configuração de proxy

A configuração a seguir é necessária para habilitar restrições de inquilinos através de sua infra-estrutura proxy. Essa diretriz é genérica, você deverá consultar a documentação do seu fornecedor de proxy para encontrar as etapas de implementação específicas.

#### <a name="prerequisites"></a>Pré-requisitos

- O proxy deve ser capaz de executar a interceptação TLS, a inserção de cabeçalho HTTP e destinos de filtro usando FQDNs/URLs.

- Os clientes devem confiar na cadeia de certificados apresentada pelo proxy para comunicações TLS. Por exemplo, se os certificados de uma [infra-estrutura de chave pública interna (PKI)](/windows/desktop/seccertenroll/public-key-infrastructure) forem usados, o certificado de autoridade de certificado raiz de emissão interna deve ser confiável.

- Esse recurso está incluído nas assinaturas do Office 365, mas se você quiser usar restrições de inquilinos para controlar o acesso a outros aplicativos SaaS, então as licenças Do Azure AD Premium 1 são necessárias.

#### <a name="configuration"></a>Configuração

Para cada solicitação de entrada para login.microsoftonline.com, login.microsoft.com e login.windows.net, insira dois cabeçalhos HTTP: *Restrict-Access-To-Tenants* e *Restrict-Access-Context*.

Os cabeçalhos devem incluir os seguintes elementos:

- Para *restringir o acesso aos inquilinos,* \<use um\>valor da lista de inquilinos permitidos, que é uma lista separada por comuma de inquilinos que você deseja permitir que os usuários acessem. Qualquer domínio que é registrado com um locatário pode ser usado para identificar o locatário nessa lista. Por exemplo, para permitir o acesso aos locatários Contoso e Fabrikam, o par nome/valor é semelhante a: `Restrict-Access-To-Tenants: contoso.onmicrosoft.com,fabrikam.onmicrosoft.com`

- Para *restringir-acesso-contexto,* use um valor de um ID de diretório único, declarando qual inquilino está definindo as restrições do inquilino. Por exemplo, para declarar Contoso como o inquilino que definiu a política de restrições do inquilino, o par de nome/valor parece: `Restrict-Access-Context: 456ff232-35l2-5h23-b3b3-3236w0826f3d`  

> [!TIP]
> Você pode encontrar seu ID de diretório no portal Do Diretório Ativo do [Azure](https://aad.portal.azure.com/). Entre como administrador, selecione **Azure Active Directory** e selecione **Propriedades**.

Para evitar que os usuários insiram seu próprio cabeçalho HTTP com inquilinos não aprovados, o proxy precisa substituir o cabeçalho *Restringir-Acesso aos Inquilinos* se ele já estiver presente na solicitação recebida.

Os clientes devem ser forçados a usar o proxy para todas as solicitações para login.microsoftonline.com, login.microsoft.com e login.windows.net. Por exemplo, se os arquivos PAC forem usados para direcionar os clientes a usar o proxy, os usuários finais não poderão editar ou desativar os arquivos PAC.

## <a name="the-user-experience"></a>A experiência do usuário

Esta seção descreve a experiência para usuários finais e admins.

### <a name="end-user-experience"></a>Experiência do usuário final

Um usuário de exemplo está na rede da Contoso, mas está tentando acessar a instância da Fabrikam de um aplicativo de SaaS compartilhado como o Outlook online. Se fabrikam é um inquilino não permitido para a instância Contoso, o usuário vê uma mensagem de negação de acesso, que diz que você está tentando acessar um recurso que pertence a uma organização não aprovada pelo seu departamento de TI.

### <a name="admin-experience"></a>Experiência de admin

Embora a configuração das restrições de inquilinos seja feita na infra-estrutura de proxy corporativo, os administradores podem acessar diretamente os relatórios de restrições de inquilinos no portal do Azure. Para visualizar os relatórios:

1. Faça login no portal do Diretório Ativo do [Azure](https://aad.portal.azure.com/). O **painel central do diretório ativo do Azure** é exibido.

2. No painel esquerdo, selecione **Azure Active Directory**. A página de visão geral do Diretório Ativo do Azure é exibida.

3. No título **Outros recursos,** selecione **restrições de inquilino**.

O admin para o inquilino especificado como o inquilino de Contexto de Acesso Restrito pode usar este relatório para ver os logins bloqueados devido à política de restrições do inquilino, incluindo a identidade usada e o ID do diretório de destino. As entradas serão incluídas se o locatário que define a restrição for o locatário do usuário ou o locatário do recurso para a entrada.

> [!NOTE]
> O relatório pode conter informações limitadas, como iD do diretório de destino, quando um usuário que está em um inquilino diferente do inquilino de acesso restrito-contexto entra em ação. Neste caso, as informações identificáveis do usuário, como nome e nome principal do usuário, são mascaradas para proteger os dados do usuário em outros inquilinos.

Como outros relatórios no Portal do Azure, você pode usar filtros para especificar o escopo do relatório. Você pode filtrar em um intervalo de tempo específico, usuário, aplicativo, cliente ou status. Se você selecionar o botão **Colunas,** você pode optar por exibir dados com qualquer combinação dos seguintes campos:

- **Usuário**
- **Aplicativo**
- **Status**
- **Data**
- **Data (UTC)** (onde utc é tempo universal coordenado)
- **Método MFA Auth** (método de autenticação multifatorial)
- **MFA Auth Detail** (detalhe de autenticação multifatorial)
- **Resultado do MFA**
- **Endereço IP**
- **Cliente**
- **Username**
- **Local**
- **ID do inquilino alvo**

## <a name="office-365-support"></a>Suporte ao Office 365

Os aplicativos do Office 365 devem atender a dois critérios para apoiar totalmente as restrições dos inquilinos:

1. O cliente usado suporta autenticação moderna.
2. A autenticação moderna está habilitada como o protocolo de autenticação padrão para o serviço de nuvem.

Consulte [Updated Office 365 modern authentication](https://www.microsoft.com/en-us/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/) (Autenticação moderna do Office 365 atualizada) para obter as informações mais recentes sobre quais clientes do Office atualmente dão suporte à autenticação moderna. Essa página também inclui links para instruções para habilitar a autenticação moderna nos locatários do Exchange Online e Skype for Business Online. O SharePoint Online já permite a autenticação moderna por padrão.

Os aplicativos baseados no navegador Office 365 (o Portal do Escritório, yammer, sites sharepoint, outlook na Web e muito mais) atualmente suportam restrições de inquilinos. Clientes espessos (Outlook, Skype for Business, Word, Excel, PowerPoint e muito mais) podem impor restrições de inquilinos somente quando usam autenticação moderna.  

Os clientes do Outlook e do Skype for Business que suportam a autenticação moderna ainda podem usar protocolos legados contra inquilinos onde a autenticação moderna não é habilitada, ignorando efetivamente as restrições dos inquilinos. As restrições de inquilinos podem bloquear aplicativos que usam protocolos legados se entrarem em contato com login.microsoftonline.com, login.microsoft.com ou login.windows.net durante a autenticação.

Para o Outlook no Windows, os clientes podem optar por implementar restrições que impedem que os usuários finais adicionem contas de email não aprovadas aos seus perfis. Por exemplo, consulte a configuração da política de grupo [Prevent adding non-default Exchange accounts](https://gpsearch.azurewebsites.net/default.aspx?ref=1) (Impedir a adição de contas do Exchange não padrão).

## <a name="testing"></a>Testes

Se você quiser testar as restrições de inquilinoantes de implementá-la para toda a sua organização, você tem duas opções: uma abordagem baseada em host usando uma ferramenta como o Fiddler ou um lançamento em etapas de configurações proxy.

### <a name="fiddler-for-a-host-based-approach"></a>Fiddler para uma abordagem baseada em host

O Fiddler é um proxy de depuração da Web gratuito que pode ser usado para capturar e modificar o tráfego HTTP/HTTPS, incluindo a inserção de cabeçalhos HTTP. Para configurar o Fiddler para testar as restrições do inquilino, execute as seguintes etapas:

1. [Baixe e instale o Fiddler](https://www.telerik.com/fiddler).

2. Configure o Fiddler para descriptografar o tráfego HTTPS, de acordo com a [documentação de ajuda do Fiddler](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS).

3. Configure o Fiddler para inserir os cabeçalhos *Restrict-Access-To-Tenants* e *Restrict-Access-Context* usando regras personalizadas:

   1. Na ferramenta Depurador da Web Fiddler Web, selecione o menu **Regras** e selecione **Personalizar Regras...** para abrir o arquivo CustomRules.

   2. Adicione as seguintes linhas `OnBeforeRequest` no início da função. Substitua \<\> o domínio do inquilino por um `contoso.onmicrosoft.com`domínio registrado com seu inquilino (por exemplo, ). Substitua \<directory ID\> pelo identificador de GUID do Azure AD do locatário.

      ```JScript.NET
      if (
          oSession.HostnameIs("login.microsoftonline.com") ||
          oSession.HostnameIs("login.microsoft.com") ||
          oSession.HostnameIs("login.windows.net")
      )
      {
          oSession.oRequest["Restrict-Access-To-Tenants"] = "<tenant domain>";
          oSession.oRequest["Restrict-Access-Context"] = "<directory ID>";
      }
      ```

      Se você precisar permitir vários locatários, use uma vírgula para separar os nomes de locatário. Por exemplo: 

      `oSession.oRequest["Restrict-Access-To-Tenants"] = "contoso.onmicrosoft.com,fabrikam.onmicrosoft.com";`

4. Salve e feche o arquivo CustomRules.

Depois de configurar o Fiddler, você pode capturar o tráfego indo para o menu **Arquivo** e selecionando **Capturar Tráfego**.

### <a name="staged-rollout-of-proxy-settings"></a>Distribuição em etapas as configurações de proxy

Dependendo dos recursos da sua infraestrutura de proxy, você poderá liberar em etapas a distribuição de configurações para seus usuários. Aqui estão algumas opções de alto nível para consideração:

1. Use arquivos PAC para apontar os usuários de teste para uma infraestrutura de proxy de teste, enquanto os usuários normais continuam a usar a infraestrutura do proxy de produção.
2. Alguns servidores proxy podem dar suporte a configurações diferentes usando grupos.

Para obter detalhes específicos, consulte a documentação do servidor proxy.

## <a name="next-steps"></a>Próximas etapas

- Leia sobre a [Updated Office 365 modern authentication](https://www.microsoft.com/en-us/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/) (Autenticação moderna do Office 365 atualizada)
- Examine as [URLs e intervalos de endereços IP do Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)
