---
title: Acesso remoto a aplicativos locais – Azure Proxy de Aplicativo do AD
description: O Proxy de Aplicativo do Azure Active Directory fornece acesso remoto seguro aos aplicativos Web locais. Após um logon único para o Azure AD, os usuários podem acessar aplicativos locais e de nuvem por meio de uma URL externa ou um portal interno do aplicativo. Por exemplo, o Proxy de Aplicativo pode fornecer acesso remoto e o logon único para a Área de Trabalho Remota, o SharePoint, o Teams, o Tableau, o Qlik e aplicativos LOB (linha de negócios).
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 05/09/2019
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4780786f0caea2c211b6b93fb0736feaade8de80
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/21/2019
ms.locfileid: "74274844"
---
# <a name="remote-access-to-on-premises-applications-through-azure-active-directorys-application-proxy"></a>Acesso remoto a aplicativos locais por meio do Proxy de Aplicativo do Azure Active Directory 

O Proxy de Aplicativo do Azure Active Directory fornece acesso remoto seguro aos aplicativos Web locais. Após um logon único para o Azure AD, os usuários podem acessar aplicativos locais e de nuvem por meio de uma URL externa ou um portal interno do aplicativo. Por exemplo, o Proxy de Aplicativo pode fornecer acesso remoto e o logon único para a Área de Trabalho Remota, o SharePoint, o Teams, o Tableau, o Qlik e aplicativos LOB (linha de negócios).

O Proxy de Aplicativo do Azure AD é:

- **Simples de usar**. Os usuários podem acessar seus aplicativos locais da mesma forma que acessam o O365 e outros aplicativos SaaS integrados ao Azure AD. Você não precisa alterar ou atualizar seus aplicativos para que funcionem com o Proxy de Aplicativo. 

- **Seguro**. Os aplicativos locais podem usar os controles de autorização e análise de segurança do Azure. Por exemplo, aplicativos locais podem usar o acesso condicional e a verificação em duas etapas. O Proxy de Aplicativo não exige que você abra conexões de entrada através do firewall.
 
- **Econômico**. As soluções locais geralmente exigem que você configure e mantenha redes de perímetro, servidores de borda ou outras infraestruturas complexas. O Proxy de Aplicativo é executado na nuvem, o que o torna fácil de usar. Para usar o Proxy de Aplicativo, você não precisa alterar a infraestrutura de rede ou instalar dispositivos adicionais no seu ambiente local.

## <a name="what-is-application-proxy"></a>O que é o Proxy de Aplicativo?
O Proxy de Aplicativo é um recurso do Azure AD que permite que os usuários acessem aplicativos Web locais de um cliente remoto. O Proxy de Aplicativo inclui o serviço de Proxy de Aplicativo que é executado na nuvem e o conector de Proxy de Aplicativo que é executado em um servidor local. O Azure AD, o serviço de Proxy de Aplicativo e o conector de Proxy de Aplicativo trabalham em conjunto para transmitir o token de logon do usuário do Azure AD para o aplicativo Web de forma segura.

O Proxy de Aplicativo funciona com:

* Aplicativos Web que usam a [Autenticação Integrada do Windows](application-proxy-configure-single-sign-on-with-kcd.md) para autenticação  
* Aplicativos Web que usam o acesso baseado em formulário ou [baseado em cabeçalho](application-proxy-configure-single-sign-on-with-ping-access.md)  
* APIs Web que você deseja expor a aplicativos avançados em diferentes dispositivos  
* Aplicativos hospedados por trás de um [Gateway de Área de Trabalho Remota](application-proxy-integrate-with-remote-desktop-services.md)  
* Aplicativos cliente avançados que são integrados com a ADAL (Biblioteca de autenticação do Active Directory)

O Proxy de Aplicativo é compatível com o logon único. Para obter mais informações sobre métodos com suporte, consulte [Escolhendo um método de logon único](what-is-single-sign-on.md#choosing-a-single-sign-on-method).

O proxy de aplicativo é recomendado para conceder aos usuários remotos acesso a recursos internos. O proxy de aplicativo substitui a necessidade de um proxy reverso ou VPN. Ele não se destina a usuários internos na rede corporativa.  Esses usuários que desnecessariamente usam o proxy de aplicativo podem introduzir problemas de desempenho inesperados e indesejáveis.

## <a name="how-application-proxy-works"></a>Como o Proxy de Aplicativo funciona

O diagrama a seguir mostra como o Azure AD e o Proxy de Aplicativo trabalham juntos para fornecer o logon único para aplicativos locais.

![Diagrama do Proxy de Aplicativo do Azure](./media/application-proxy/azureappproxxy.png)

1. Após o usuário ter acessado o aplicativo por meio de um ponto de extremidade, ele será direcionado para a página de entrada do Azure AD. 
2. Após uma entrada com êxito, o Azure AD envia um token para o dispositivo cliente do usuário.
3. O cliente agora envia o token para o serviço Proxy de Aplicativo que recuperará o nome UPN e o SPN (nome da entidade de segurança) do token. O Proxy de Aplicativo envia então a solicitação para o conector de Proxy de Aplicativo.
4. Se você tiver configurado o logon único, o conector não realizará nenhuma autenticação adicional necessária em nome do usuário.
5. O conector envia a solicitação para o aplicativo no local.  
6. A resposta é enviada por meio do conector e do serviço Proxy de Aplicativo para o usuário.

| Componente | DESCRIÇÃO |
| --------- | ----------- |
| Ponto de extremidade  | O ponto de extremidade é uma URL ou um [portal do usuário final](end-user-experiences.md). Os usuários podem acessar aplicativos enquanto estão fora de sua rede ao acessar uma URL externa. Usuários dentro de sua rede podem acessar o aplicativo por meio de uma URL ou de um portal do usuário final. Quando os usuários acessam um desses pontos de extremidade, eles são autenticados no Azure AD e, em seguida, são direcionados por meio do conector até o aplicativo local.|
| AD do Azure | O Azure AD executa a autenticação usando o diretório do locatário armazenado na nuvem. |
| Serviço do Proxy de Aplicativo | Esse serviço de Proxy de Aplicativo é executado na nuvem como parte do Azure AD. Ele passa o token de logon do usuário para o Conector de Proxy de Aplicativo. O Proxy de Aplicativo encaminha qualquer cabeçalho acessível na solicitação e define os cabeçalhos de acordo com seu protocolo para o endereço IP do cliente. Se a solicitação de entrada para o proxy já tiver esse cabeçalho, o endereço IP do cliente será adicionado ao final da lista separada por vírgulas que é o valor do cabeçalho.|
| Conector de Proxy de Aplicativo | O conector é um agente leve executado em um Windows Server na sua rede. Ele gerencia a comunicação entre o serviço de Proxy de Aplicativo na nuvem e o aplicativo local. O conector usa apenas conexões de saída, portanto você não precisa abrir portas de entrada nem colocar nada na DMZ. Os conectores são sem monitoração de estado e efetuam pull de informações da nuvem conforme necessário. Para obter mais informações sobre conectores, como eles fazem o balanceamento de carga e a autenticação, consulte [Noções básicas sobre conectores de Proxy de Aplicativo do Azure AD](application-proxy-connectors.md).|
| AD (Active Directory) | O Active Directory é executado localmente para realizar a autenticação para contas de domínio. Quando o logon único está configurado, o conector se comunica com o AD para realizar qualquer autenticação adicional necessária.
| Aplicativo local | Por fim, o usuário é capaz de acessar um aplicativo local. 

## <a name="next-steps"></a>Próximas etapas
Para começar a usar o proxy de aplicativo, consulte [tutorial: adicionar um aplicativo local para acesso remoto por meio do proxy de aplicativo](application-proxy-add-on-premises-application.md). 

Para encontrar as últimas notícias e atualizações, confira o [blog do Proxy de Aplicativo](https://blogs.technet.com/b/applicationproxyblog/)


