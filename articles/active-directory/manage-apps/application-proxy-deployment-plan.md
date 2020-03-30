---
title: Planeje uma implantação de proxy de aplicativo de diretório ativo do Azure
description: Um guia completo para planejar a implantação do proxy do aplicativo dentro da sua organização
services: active-directory
documentationcenter: azure
author: barbaraselden
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: baselden
ms.reviewer: ''
ms.openlocfilehash: edd607c4d708df9fcfd3cbd5fdb71f0a7652d6c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80330900"
---
# <a name="plan-an-azure-ad-application-proxy-deployment"></a>Planeje uma implantação de proxy de aplicativo AD do Azure

O Azure Active Directory (Azure AD) Application Proxy é uma solução de acesso remoto segura e econômica para aplicativos locais. Ele fornece um caminho de transição imediato para que as organizações "Cloud First" gerenciem o acesso a aplicativos legados no local que ainda não são capazes de usar protocolos modernos. Para obter informações introdutórias adicionais, consulte [O que é proxy de aplicativo](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy).

O Proxy do aplicativo é recomendado para dar aos usuários remotos acesso a recursos internos. O Proxy de aplicativos substitui a necessidade de uma VPN ou proxy reverso para esses casos de uso remoto. Não se destina a usuários que estão na rede corporativa. Esses usuários que usam o Proxy do aplicativo para acesso à intranet podem ter problemas de desempenho indesejáveis.

Este artigo inclui os recursos necessários para planejar, operar e gerenciar o Proxy de aplicativo AD do Azure. 

## <a name="plan-your-implementation"></a>Planejar sua implementação

A seção a seguir fornece uma visão ampla dos principais elementos de planejamento que o configurarão para uma experiência de implantação eficiente. 

### <a name="prerequisites"></a>Pré-requisitos

Você precisa atender aos seguintes pré-requisitos antes de iniciar sua implementação. Você pode ver mais informações sobre como configurar seu ambiente, incluindo esses pré-requisitos, neste [tutorial](application-proxy-add-on-premises-application.md).

* **Conectores**: Conectores são agentes leves que você pode implantar em:
   * Hardware físico no local
   * Uma VM hospedada em qualquer solução de hipervisor
   * Uma VM hospedada no Azure para habilitar a conexão de saída ao serviço Proxy do aplicativo.

* Consulte [Entenda os conectores proxy do aplicativo AD do Azure](application-proxy-connectors.md) para obter uma visão geral mais detalhada.

     * As máquinas de conectores devem [ser habilitadas para o TLS 1.2](application-proxy-add-on-premises-application.md) antes de instalar os conectores.

     * Se possível, implante conectores na [mesma rede](application-proxy-network-topology.md) e segmento que os servidores de aplicativos web back-end. É melhor implantar conectores depois de concluir uma descoberta de aplicativos.
     * Recomendamos que cada grupo de conectores tenha pelo menos dois conectores para fornecer alta disponibilidade e escala. Ter três conectores é ótimo no caso de você precisar atender uma máquina a qualquer momento. Revise a tabela de capacidade do [conector](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-connectors#capacity-planning) para ajudar a decidir em que tipo de máquina instalar conectores. Quanto maior a máquina, mais tampão e executor o conector será.

* **Configurações de acesso à rede**: Os conectores proxy do aplicativo Azure AD [conectam-se ao Azure via HTTPS (Porta TCP 443) e HTTP (Porta TCP 80).](application-proxy-add-on-premises-application.md) 

   * O tráfego tLS do conector de terminação não é suportado e impedirá que os conectores estabeleçam um canal seguro com seus respectivos pontos finais do Proxy do Aplicativo Azure.

   * Evite todas as formas de inspeção inline nas comunicações TLS de saída entre conectores e Azure. A inspeção interna entre um conector e aplicações backend é possível, mas pode degradar a experiência do usuário e, como tal, não é recomendada.

   * O balanceamento de carga dos próprios conectores também não é suportado, ou mesmo necessário.

### <a name="important-considerations-before-configuring-azure-ad-application-proxy"></a>Considerações importantes antes de configurar o Proxy de aplicativo AD do Azure

Os seguintes requisitos principais devem ser cumpridos para configurar e implementar o Proxy de aplicativo AD do Azure.

*  **Onboarding do Azure**: Antes de implantar o proxy do aplicativo, as identidades do usuário devem ser sincronizadas a partir de um diretório local ou criadas diretamente dentro de seus inquilinos Azure AD. A Sincronização de Identidades permite que o Azure AD pré-autentique os usuários antes de permitir acesso a eles aos aplicativos publicados pelo Proxy de Aplicativo e tenha as informações de identificador de usuário necessárias para realizar o SSO (logon único).

* **Requisitos de acesso condicional**: Não recomendamos o uso do Proxy de aplicativo para acesso à intranet porque isso adiciona latência que afetará os usuários. Recomendamos o uso do Proxy de aplicativo com políticas de pré-autenticação e acesso condicional para acesso remoto da internet.  Uma abordagem para fornecer acesso condicional para uso de intranet é modernizar aplicativos para que eles possam autenticar diretamente com AAD. Consulte [Recursos para migrar aplicativos para AAD](https://docs.microsoft.com/azure/active-directory/manage-apps/migration-resources) para obter mais informações. 

* **Limites de**serviço : Para proteger contra o consumo excessivo de recursos por inquilinos individuais, existem limites de estrangulamento definidos por aplicativo e inquilino. Para ver esses limites, consulte [os limites e restrições de serviço do Azure AD](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-service-limits-restrictions). Esses limites de estrangulamento são baseados em um benchmark muito acima do volume de uso típico e fornecem um buffer amplo para a maioria das implantações.

* **Certificado público**: Se você estiver usando nomes de domínio personalizados, você deve obter um certificado TLS/SSL. Dependendo dos seus requisitos organizacionais, obter um certificado pode levar algum tempo e recomendamos iniciar o processo o mais cedo possível. O Azure Application Proxy suporta certificados padrão, [curinga](application-proxy-wildcard.md)ou baseados em SAN. Para obter mais detalhes, [consulte Configurar domínios personalizados com o Proxy de aplicativo Azure AD](application-proxy-configure-custom-domain.md).

* **Requisitos de domínio**: O login único em seus aplicativos publicados usando o Kerberos Constrained Delegation (KCD) exige que o servidor que executa o Conector e o servidor que executa o aplicativo sejam unidos pelo domínio e fazem parte do mesmo domínio ou domínios confiáveis.
Para obter informações detalhadas sobre o tópico, consulte [KCD para fazer o login único](application-proxy-configure-single-sign-on-with-kcd.md) com o Proxy do aplicativo. O serviço de conector é executado no contexto do sistema local e não deve ser configurado para usar uma identidade personalizada.

* **Registros de DNS para URLs**

   * Antes de usar domínios personalizados no Proxy de aplicativo, você deve criar um registro CNAME no DNS público, permitindo que os clientes resolvam a URL externa definida personalizada para o endereço proxy de aplicativo pré-definido. A não criação de um registro CNAME para um aplicativo que usa um domínio personalizado impedirá que usuários remotos se conectem ao aplicativo. As etapas necessárias para adicionar registros CNAME podem variar de provedor de DNS para provedor, então aprenda a [gerenciar registros e conjuntos de registros DNS usando o portal Azure](https://docs.microsoft.com/azure/dns/dns-operations-recordsets-portal).

   * Da mesma forma, os hosts de conectores devem ser capazes de resolver a URL interna dos aplicativos que estão sendo publicados.

* **Direitos e funções administrativas**

   * **A instalação do conector** requer os direitos de admin locais para o servidor Windows em que ele está sendo instalado. Ele também requer um mínimo de uma função *de administrador* de aplicativos para autenticar e registrar a instância do conector para o seu inquilino Azure AD. 

   * **A publicação e a administração** do aplicativo exigem a função *administrador de aplicativos.* Os administradores de aplicativos podem gerenciar todos os aplicativos no diretório, incluindo registros, configurações de SSO, atribuições e licenciamento de usuários e grupos, configurações de proxy de aplicativos e consentimento. Ela não concede a capacidade de gerenciar o Acesso Condicional. A função *Administrador de aplicativos* em nuvem tem todas as habilidades do administrador de aplicativos, exceto que ele não permite o gerenciamento das configurações do Proxy do aplicativo.

* **Licenciamento**: O Proxy de aplicativo está disponível através de uma assinatura Azure AD Premium. Consulte a página de preços do diretório ativo do [Azure](https://azure.microsoft.com/pricing/details/active-directory/) para obter uma lista completa de opções e recursos de licenciamento.  

### <a name="application-discovery"></a>Descoberta de aplicativos

Compile um inventário de todos os aplicativos no escopo que estão sendo publicados via Proxy de aplicativo coletando as seguintes informações:

| Information Type| Informações a serem coletadas |
|---|---|
| Tipo de Serviço| Por exemplo: SharePoint, SAP, CRM, Aplicativo Web Personalizado, API |
| Plataforma de aplicativos | Por exemplo: Windows IIS, Apache no Linux, Tomcat, NGINX |
| Associação do domínio| Nome de domínio totalmente qualificado do servidor Web (FQDN) |
| Localização do aplicativo | Onde o servidor web ou fazenda está localizado em sua infra-estrutura |
| Acesso interno | A URL exata usada ao acessar o aplicativo internamente. <br> Se uma fazenda, que tipo de balanceamento de carga está em uso? <br> Se o aplicativo extrai conteúdo de fontes diferentes de si mesmo.<br> Determine se o aplicativo opera por WebSockets. |
| Acesso externo | A solução do fornecedor a que o aplicativo já está exposto externamente. <br> A URL que você deseja usar para acesso externo. Se do SharePoint, certifique-se de que mapeamentos de acesso alternativos sejam configurados por [essa orientação](https://docs.microsoft.com/SharePoint/administration/configure-alternate-access-mappings). Caso assim, você precisará definir URLs externos. |
| Certificado público | Se usar um domínio personalizado, obtenha um certificado com um nome de assunto correspondente. se um certificado existir, anote o número de série e o local de onde ele pode ser obtido. |
| Tipo de autenticação| O tipo de autenticação suportado pelo suporte ao aplicativo, como Basic, Windows Integration Authentication, forms-based, header-based e claims. <br>Se o aplicativo estiver configurado para ser executado em uma conta de domínio específica, observe o Nome de Domínio Totalmente Qualificado (FQDN) da conta de serviço.<br> Se baseado em SAML, o identificador e as URLs de resposta. <br> Se baseado em cabeçalho, a solução do fornecedor e o requisito específico para lidar com o tipo de autenticação. |
| Nome do grupo do conector | O nome lógico para o grupo de conectores que serão designados para fornecer o conduíte e o SSO para este aplicativo back-end. |
| Acesso a usuários/grupos | Os usuários ou grupos de usuários que terão acesso externo ao aplicativo. |
| Requisitos adicionais | Observe quaisquer requisitos adicionais de acesso remoto ou segurança que devem ser levados em conta para publicar o aplicativo. |

Você pode baixar esta [planilha de inventário de aplicativos](https://aka.ms/appdiscovery) para fazer o inventário de seus aplicativos.

### <a name="define-organizational-requirements"></a>Definir requisitos organizacionais

A seguir estão as áreas para as quais você deve definir os requisitos de negócios da sua organização. Cada área contém exemplos de requisitos

 **Acesso**

* Usuários remotos com domínio saqueado ou dispositivos aderidos ao Azure AD os usuários podem acessar aplicativos publicados com segurança com logon único (SSO).

* Usuários remotos com dispositivos pessoais aprovados podem acessar com segurança aplicativos publicados desde que estejam inscritos no MFA e tenham registrado o aplicativo Microsoft Authenticator em seu celular como um método de autenticação.

**Governança** 

* Os administradores podem definir e monitorar o ciclo de vida das atribuições do usuário para aplicativos publicados através do Proxy do aplicativo.

**Segurança**

* Somente os usuários atribuídos aos aplicativos via membro do grupo ou individualmente podem acessar esses aplicativos.

**Desempenho**

* Não há degradação do desempenho do aplicativo em comparação com o acesso ao aplicativo da rede interna.

**Experiência do Usuário**

* Os usuários estão cientes de como acessar seus aplicativos usando URLs da empresa familiar em qualquer plataforma de dispositivo.

**Auditoria**
* Os administradores são capazes de auditar a atividade de acesso ao usuário.


### <a name="best-practices-for-a-pilot"></a>Melhores práticas para um piloto

Determine a quantidade de tempo e esforço necessários para encomendar integralmente um único aplicativo para acesso remoto com sso de logon único. Faça isso executando um piloto que considera sua descoberta inicial, publicação e testes gerais. O uso de um simples aplicativo web baseado em IIS que já está pré-configurado para iWA (Integrated Windows Authentication, autenticação integrada do Windows) ajudaria a estabelecer uma linha de base, já que essa configuração requer um esforço mínimo para pilotar com sucesso o acesso remoto e o SSO.

Os seguintes elementos de design devem aumentar o sucesso de sua implementação piloto diretamente em um inquilino de produção.  

**Gerenciamento de conectores:**  

* Os conectores desempenham um papel fundamental no fornecimento do conduíte local para seus aplicativos. O uso do grupo conector **Padrão** é adequado para testes piloto iniciais de aplicações publicadas antes de comissioná-los para a produção. Os aplicativos testados com sucesso podem então ser movidos para grupos de conectores de produção.

**Gerenciamento de aplicações**:

* É mais provável que sua força de trabalho se lembre que uma URL externa é familiar e relevante. Evite publicar seu aplicativo usando nossos sufixos pré-definidos msappproxy.net ou onmicrosoft.com. Em vez disso, forneça um domínio verificado de alto nível familiar, prefixado com um nome de host lógico como *intranet.<customers_domain>.com*.

* Restringir a visibilidade do ícone do aplicativo piloto a um grupo piloto escondendo seu ícone de lançamento no portal Azure MyApps. Quando estiver pronto para a produção, você pode escopo do aplicativo para seu respectivo público-alvo, seja no mesmo inquilino de pré-produção, ou também publicando o aplicativo em seu inquilino de produção.

**Configurações únicas de login**: Algumas configurações de SSO têm dependências específicas que podem levar tempo para configurar, portanto evite atrasos no controle de alterações, garantindo que as dependências sejam tratadas com antecedência. Isso inclui a junção de hosts de conectores de domínio para executar o SSO usando a Delegação Restrita Kerberos (KCD) e cuidando de outras atividades demoradas. Por exemplo, configurar uma instância de acesso ping, se precisar de SSO baseado em cabeçalho.

**TLS Entre host conector e aplicativo de destino**: A segurança é primordial, de modo que o TLS entre o host conector e os aplicativos-alvo devem ser sempre usados. Especialmente se o aplicativo web estiver configurado para autenticação baseada em formulários (FBA), pois as credenciais do usuário são então efetivamente transmitidas em texto claro.

**Implementar incrementalmente e testar cada etapa**. Realize testes funcionais básicos após a publicação de um aplicativo para garantir que todos os requisitos do usuário e da empresa sejam atendidos seguindo as instruções abaixo:

1. Teste e valide o acesso geral ao aplicativo web com pré-autenticação desativada.
2. Se for bem-sucedido, habilite a pré-autenticação e atribua usuários e grupos. Teste e valide o acesso.
3. Em seguida, adicione o método SSO para sua aplicação e teste novamente para validar o acesso.
4. Aplique políticas de Acesso Condicional e MFA conforme necessário. Teste e valide o acesso.

**Ferramentas de solução de**problemas : Ao solucionar problemas, sempre comece validando o acesso ao aplicativo publicado a partir do navegador no host do conector e confirme se o aplicativo funciona conforme o esperado. Quanto mais simples for a configuração, mais fácil de determinar a causa raiz, então considere tentar reproduzir problemas com uma configuração mínima, como usar apenas um conector único e nenhum SSO. Em alguns casos, ferramentas de depuração da Web, como o Fiddler da Telerik, podem se mostrar indispensáveis para solucionar problemas de acesso ou conteúdo em aplicativos acessados por meio de um proxy. Fiddler também pode atuar como um proxy para ajudar a rastrear e depurar tráfego para plataformas móveis, como iOS e Android, e praticamente qualquer coisa que possa ser configurada para rota através de um proxy. Consulte o [guia de solução de problemas](application-proxy-troubleshoot.md) para obter mais informações.

## <a name="implement-your-solution"></a>Implementar sua solução

### <a name="deploy-application-proxy"></a>Implantar proxy de aplicativos

As etapas para implantar o proxy do aplicativo são [abordadas](application-proxy-add-on-premises-application.md)neste tutorial para adicionar um aplicativo local para acesso remoto . Se a instalação não for bem sucedida, selecione **''Solucionar problemas' Proxy** no portal ou use o guia de solução de [problemas para problemas com a instalação do Conector do Agente proxy do aplicativo](application-proxy-connector-installation-problem.md).

### <a name="publish-applications-via-application-proxy"></a>Publicar aplicativos via Proxy de Aplicativos

Os aplicativos de publicação assumem que você satisfez todos os pré-requisitos e que você tem vários conectores mostrando-se registrados e ativos na página Proxy do aplicativo.

Você também pode publicar aplicativos usando [o PowerShell](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview).

Abaixo estão algumas práticas recomendadas a seguir ao publicar um aplicativo:

* **Use grupos conectores**: Atribua um grupo de conectores designado para publicar cada aplicativo respectivo. Recomendamos que cada grupo de conectores tenha pelo menos dois conectores para fornecer alta disponibilidade e escala. Ter três conectores é ótimo no caso de você precisar atender uma máquina a qualquer momento. Além disso, consulte [Publicar aplicativos em redes e locais separados usando grupos de conectores](application-proxy-connector-groups.md) para ver como você também pode usar grupos de conectores para segmentar seus conectores por rede ou localização.

* **Definir tempo out do aplicativo Backend**: Essa configuração é útil em cenários onde o aplicativo pode exigir mais de 75 segundos para processar uma transação cliente. Por exemplo, quando um cliente envia uma consulta para um aplicativo web que age como um front-end para um banco de dados. O front-end envia essa consulta para o servidor de banco de dados back-end e aguarda uma resposta, mas quando recebe uma resposta, o lado cliente da conversa é eliminado. Definir o tempo máximo para Long fornece 180 segundos para transações mais longas para serem concluídas.

* **Use tipos de cookies apropriados**

   * **Cookies HTTP-Only**: Fornece segurança adicional ao ter o Proxy do aplicativo, incluindo o sinalizador HTTPOnly em cabeçalhos de resposta HTTP de set-cookie. Essa configuração ajuda a mitigar explorações como scripting entre sites (XSS). Deixe este conjunto para Não para clientes/agentes de usuário que requerem acesso ao cookie da sessão. Por exemplo, o cliente RDP/MTSC que se conecta a um Gateway de Desktop Remoto publicado via App Proxy.

   * **Cookie seguro**: Quando um cookie é definido com o atributo Secure, o agente do usuário (aplicativo do lado do cliente) só incluirá o cookie em solicitações HTTP se a solicitação for transmitida através de um canal protegido TLS. Isso ajuda a mitigar o risco de um cookie ser comprometido por canais de texto claros, por isso deve ser ativado.

   * **Cookies persistentes**: Permite que o cookie de sessão proxy do aplicativo persista entre os fechamentos do navegador, permanecendo válido até que ele expire ou seja excluído. Usado para cenários em que um aplicativo rico, como o escritório, acessa um documento dentro de um aplicativo web publicado, sem que o usuário seja solicitado para autenticação. No entanto, permita com cautela, pois os cookies persistentes podem, em última análise, deixar um serviço em risco de acesso não autorizado, se não for usado em conjunto com outros controles compensadores. Essa configuração só deve ser usada para aplicativos mais antigos que não podem compartilhar cookies entre processos. É melhor atualizar seu aplicativo para lidar com o compartilhamento de cookies entre processos em vez de usar essa configuração.

* **Traduza URLs em Cabeçalhos**: Você habilita isso para cenários onde o DNS interno não pode ser configurado para corresponder ao namespace público da organização (também conhecido como DNS split). A menos que seu aplicativo exija o cabeçalho de host original na solicitação do cliente, deixe este valor definido como Sim. A alternativa é fazer com que o conector use o FQDN na URL interna para roteamento do tráfego real, e o FQDN na URL externa, como o cabeçalho do host. Na maioria dos casos, essa alternativa deve permitir que o aplicativo funcione normalmente, quando acessado remotamente, mas seus usuários perdem os benefícios de ter uma correspondência dentro & URL externo.

* **Translate URLs in Application Body**: Ative a tradução do link do corpo do aplicativo para um aplicativo quando você quiser que os links desse aplicativo sejam traduzidos em respostas de volta ao cliente. Se ativada, essa função oferece uma tentativa de melhor esforço para traduzir todos os links internos que o App Proxy encontra em respostas HTML e CSS sendo devolvidas aos clientes. É útil ao publicar aplicativos que contenham links de nome curto de código rígido ou netbios no conteúdo ou aplicativos com conteúdo que se vincule a outros aplicativos locais.

Para cenários em que um aplicativo publicado se vincule a outros aplicativos publicados, habilite a tradução de link para cada aplicativo para que você tenha controle sobre a experiência do usuário no nível por aplicativo.

Por exemplo, suponha que você tenha três aplicativos publicados através do Proxy do aplicativo que todos vinculam um ao outro: Benefícios, Despesas e Viagens, além de um quarto aplicativo, Feedback, que não é publicado através do Proxy do aplicativo.

![Imagem 1](media/App-proxy-deployment-plan/link-translation.png)

Quando você habilita a tradução de link para o aplicativo Benefícios, os links para Despesas e Viagens são redirecionados para urls externos para esses aplicativos, para que os usuários que acessam os aplicativos de fora da rede corporativa possam acessá-los. Links de Despesas e Viagens de Volta aos Benefícios não funcionam porque a tradução de link não foi ativada para esses dois aplicativos. O link para feedback não é redirecionado porque não há URL externo, então os usuários que usam o aplicativo Benefícios não poderão acessar o aplicativo de feedback de fora da rede corporativa. Consulte informações detalhadas sobre [tradução de link e outras opções de redirecionamento](application-proxy-configure-hard-coded-link-translation.md).

### <a name="access-your-application"></a>Acesse seu aplicativo

Existem várias opções para gerenciar o acesso aos recursos publicados pelo App Proxy, por isso escolha as mais adequadas para o seu cenário e necessidades de escalabilidade. As abordagens comuns incluem: usar grupos locais que estão sendo sincronizados via Azure AD Connect, criar Grupos Dinâmicos no Azure AD com base em atributos do usuário, usar grupos de autoatendimento gerenciados por um proprietário de recursos ou uma combinação de todos eles. Veja os recursos vinculados para os benefícios de cada um.

A maneira mais direta de atribuir aos usuários o acesso a um aplicativo é ir para as opções **usuários e grupos** a partir do painel esquerdo do seu aplicativo publicado e atribuir diretamente grupos ou indivíduos.

![Figura 24](media/App-proxy-deployment-plan/add-user.png)

Você também pode permitir que os usuários tenham acesso a autoatendimento ao seu aplicativo, atribuindo a um grupo do que eles não são atualmente um membro e configurando as opções de autoatendimento.

![Imagem 25](media/App-proxy-deployment-plan/allow-access.png)

Se ativado, os usuários poderão entrar no portal do MyApps e solicitar acesso, e serão aprovados automaticamente e adicionados ao grupo de autoatendimento já permitido, ou precisarão de aprovação de um aprovador designado.

Os usuários convidados também podem ser [convidados a acessar aplicativos internos publicados via Proxy de aplicativo através do Azure AD B2B](https://docs.microsoft.com/azure/active-directory/b2b/add-users-information-worker).

Para aplicativos no local que normalmente são acessíveis anonimamente, não exigindo autenticação, você pode preferir desativar a opção localizada nas **Propriedades**do aplicativo .

![Imagem 26](media/App-proxy-deployment-plan/assignment-required.png)


Deixar esta opção definida como No permite que os usuários acessem o aplicativo local via Azure AD App Proxy sem permissões, então use com cautela.

Uma vez que seu aplicativo é publicado, ele deve ser acessível [https://myapps.microsoft.com](https://myapps.microsoft.com/)digitando sua URL externa em um navegador ou por seu ícone em .

### <a name="enable-pre-authentication"></a>Habilite a pré-autenticação

Verifique se seu aplicativo está acessível através do Proxy do aplicativo acessando-o através da URL externa. 

1. Navegue pelos**aplicativos** >  **Do Azure Active Directory** > Enterprise**Todos os aplicativos** e escolha o aplicativo que deseja gerenciar.

2. Selecione **Proxy de Aplicativo**.

3. No **campo Pré-Autenticação,** use a lista de paradas para selecionar **o Diretório Ativo do Azure**e selecione **Salvar**.

Com a pré-autenticação ativada, o Azure AD desafiará os usuários primeiro para autenticação e se o login único for configurado, o aplicativo back-end também verificará o usuário antes que o acesso ao aplicativo seja concedido. A alteração do modo de pré-autenticação de Passthrough para Azure AD também configura a URL externa com HTTPS, de modo que qualquer aplicativo inicialmente configurado para HTTP será agora protegido com HTTPS.

### <a name="enable-single-sign-on"></a>Habilitar Logon Único

O SSO oferece a melhor experiência e segurança possível do usuário, porque os usuários só precisam fazer login uma vez ao acessar o Azure AD. Uma vez que um usuário tenha pré-autenticado, o SSO é realizado pelo conector Proxy do aplicativo autenticando-se no aplicativo local, em nome do usuário. O aplicativo backend processa o login como se fosse o próprio usuário. 

A escolha da opção **Passagem** permite que os usuários acessem o aplicativo publicado sem nunca ter que autenticar no Azure AD.

A realização do SSO só é possível se o Azure AD puder identificar o usuário solicitando acesso a um recurso, de modo que seu aplicativo deve ser configurado para pré-autenticar usuários com o Azure AD no acesso para o SSO funcionar, caso contrário as opções de SSO serão desativadas.

Leia [o login único em aplicativos no Azure AD para ajudá-lo](what-is-single-sign-on.md) a escolher o método SSO mais apropriado ao configurar seus aplicativos.

###  <a name="working-with-other-types-of-applications"></a>Trabalhando com outros tipos de aplicativos

O Azure AD Application Proxy também pode suportar aplicativos que foram desenvolvidos para usar nossa Biblioteca de Autenticação AD[(ADAL)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries)ou Microsoft Authentication Library[(MSAL).](https://azure.microsoft.com/blog/start-writing-applications-today-with-the-new-microsoft-authentication-sdks/) Ele suporta aplicativos de clientes nativos consumindo tokens emitidos pelo Azure AD recebidos nas informações de cabeçalho da solicitação do cliente para realizar a pré-autenticação em nome dos usuários.

Leia [a publicação de aplicativos de clientes nativos e móveis](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-native-client) e aplicativos [baseados em sinistros](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-claims-aware-apps) para saber sobre as configurações disponíveis do Proxy de aplicativos.

### <a name="use-conditional-access-to-strengthen-security"></a>Use o Acesso Condicional para reforçar a segurança

A segurança do aplicativo requer um conjunto avançado de recursos de segurança que podem proteger e responder a ameaças complexas no local e na nuvem. Os atacantes geralmente ganham acesso à rede corporativa através de credenciais de usuários fracas, padrão ou roubadas.  A segurança baseada em identidade da Microsoft reduz o uso de credenciais roubadas, gerenciando e protegendo identidades privilegiadas e não privilegiadas.

Os seguintes recursos podem ser usados para suportar o Proxy de aplicativo AD do Azure:

* Acesso condicional baseado em usuário e localização: mantenha os dados confidenciais protegidos limitando o acesso do usuário com base em geolocalização ou em um endereço IP com [políticas de acesso condicional baseadas em localização](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-locations).

* Acesso Condicional baseado em dispositivos: Certifique-se de que apenas dispositivos inscritos, aprovados e compatíveis podem acessar dados corporativos com [acesso condicional baseado em dispositivos](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-policy-connected-applications).

* Acesso Condicional baseado em aplicativos: O trabalho não precisa parar quando um usuário não está na rede corporativa. [Garanta o acesso a aplicativos corporativos em nuvem e on-premises](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam) e mantenha o controle com o Conditional Access.

* Acesso Condicional baseado em risco: Proteja seus dados contra hackers mal-intencionados com uma [política de acesso condicional baseada em riscos](https://www.microsoft.com/cloud-platform/conditional-access) que pode ser aplicada a todos os aplicativos e a todos os usuários, seja no local ou na nuvem.

* Painel de acesso a ad do Azure: Com o serviço proxy de aplicativos implantado e os aplicativos publicados com segurança, ofereça aos seus usuários um hub simples para descobrir e acessar todos os seus aplicativos. Aumentar a produtividade com recursos de autoatendimento, como a capacidade de solicitar acesso a novos aplicativos e grupos ou gerenciar o acesso a esses recursos em nome de outros, através do Painel de [Acesso](https://aka.ms/AccessPanelDPDownload).

## <a name="manage-your-implementation"></a>Gerencie sua implementação

### <a name="required-roles"></a>Funções necessárias

A Microsoft defende o princípio de conceder o menor privilégio possível para executar tarefas necessárias com o Azure AD. [Revise as diferentes funções do Azure disponíveis](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal) e escolha a certa para atender às necessidades de cada persona. Algumas funções podem precisar ser aplicadas temporariamente e removidas após a implantação ser concluída.

| Papel do negócio| Tarefas de negócios| Funções do Azure AD |
|---|---|---|
| Admin help desk | Normalmente limitado a problemas de atendimento ao usuário final qualificados e na execução de tarefas limitadas, como alterar senhas dos usuários, invalidar tokens de atualização e monitorar a saúde do serviço. | Administrador de assistência técnica |
| Admin de identidade| Leia o sinal do Azure AD em relatórios e registros de auditoria para depurar problemas relacionados ao Proxy do Aplicativo.| Leitor de segurança |
| Proprietário do aplicativo| Crie e gerencie todos os aspectos de aplicativos corporativos, registros de aplicativos e configurações de proxy de aplicativos.| Admin de aplicação |
| Admin de infra-estrutura | Proprietário de rolagem de certificados | Admin de aplicação |

Minimizar o número de pessoas que têm acesso a informações ou recursos seguros ajudará a reduzir a chance de um ator mal-intencionado obter acesso não autorizado ou um usuário autorizado inadvertidamente impactando um recurso sensível. 
 
No entanto, os usuários ainda precisam realizar operações privilegiadas no dia-a-dia, por isso, aplicar políticas de Gerenciamento de [Identidade Privilegiada](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) baseadas em Just-in-Time (JIT) para fornecer acesso privilegiado demanda aos recursos do Azure e o Azure AD é nossa abordagem recomendada para gerenciar efetivamente o acesso e a auditoria administrativa.

### <a name="reporting-and-monitoring"></a>Relatórios e monitoramento

O Azure AD fornece insights adicionais sobre o uso de aplicativos e a saúde operacional da sua organização através de [registros de auditoria e relatórios](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context). O Proxy de aplicativos também facilita muito o monitoramento de conectores do portal Azure AD e do Windows Event Logs.

#### <a name="application-audit-logs"></a>Logs de auditoria de aplicativo

Esses logs fornecem informações detalhadas sobre logins para aplicativos configurados com o Proxy do aplicativo e o dispositivo e o usuário acessando o aplicativo. [Os registros de auditoria](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) estão localizados no portal azure e na [API de auditoria](https://docs.microsoft.com/graph/api/resources/directoryaudit?view=graph-rest-beta) para exportação. Além disso, [relatórios de uso e insights](../reports-monitoring/concept-usage-insights-report.md?context=azure/active-directory/manage-apps/context/manage-apps-context) também estão disponíveis para sua aplicação.

#### <a name="application-proxy-connector-monitoring"></a>Monitoramento do conector proxy do aplicativo

Os conectores e o serviço cuidam de todas as tarefas de alta disponibilidade. Você pode monitorar o status de seus conectores a partir da página Proxy do aplicativo no Portal Azure AD. Para obter mais informações sobre a manutenção do conector, consulte [Understand Azure AD Application Proxy Connectors](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-connectors#maintenance).

![Exemplo: Conectores proxy do aplicativo Azure AD](./media/application-proxy-connectors/app-proxy-connectors.png)

#### <a name="windows-event-logs-and-performance-counters"></a>Logs de eventos do Windows e contadores de desempenho

Os conectores têm registros de admin e de sessão. Os logs de administrador incluem eventos de chave e seus erros. Os logs de sessão incluem todas as transações e seus detalhes de processamento. Logs e contadores estão localizados no Windows Event Logs para obter mais informações, consulte [Understand Azure AD Application Proxy Connectors](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-connectors#under-the-hood). Siga este [tutorial para configurar fontes de dados de registro de eventos no Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-windows-events).

### <a name="troubleshooting-guide-and-steps"></a>Guia e etapas de solução de problemas

Saiba mais sobre problemas comuns e como resolvê-los com nosso guia para [solucionar](application-proxy-troubleshoot.md) mensagens de erro. 

Os artigos a seguir abrangem cenários comuns que também podem ser usados para criar guias de solução de problemas para sua organização de suporte. 

* [Problema ao exibir página do aplicativo](application-proxy-page-appearance-broken-problem.md)
* [O carregamento de aplicativos é muito longo](application-proxy-page-load-speed-problem.md)
* [Os links na página do aplicativo não funcionam](application-proxy-page-links-broken-problem.md)
* [Quais portas abrir para meu aplicativo](application-proxy-connectivity-ports-how-to.md)
* [Nenhum conector útil em um grupo de conectores para meu aplicativo](application-proxy-connectivity-no-working-connector.md)
* [Configurar no portal do administrador](application-proxy-config-how-to.md)
* [Configurar logon único para meu aplicativo](application-proxy-config-sso-how-to.md)
* [Problema ao criar um aplicativo no portal de administração](application-proxy-config-problem.md)
* [Configurar a Delegação Restrita de Kerberos](application-proxy-back-end-kerberos-constrained-delegation-how-to.md)
* [Configurar com PingAccess](application-proxy-back-end-ping-access-how-to.md)
* [Erro Não é possível acessar este aplicativo corporativo](application-proxy-sign-in-bad-gateway-timeout-error.md)
* [Problema ao instalar o Conector de Agente de Proxy do Aplicativo](application-proxy-connector-installation-problem.md)
* [Problema de entrada](application-sign-in-problem-on-premises-application-proxy.md)
