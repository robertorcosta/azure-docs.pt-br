---
title: Quatro passos para uma forte fundação de identidade - Azure AD
description: Este tópico descreve quatro passos que os clientes de identidade híbrida podem tomar para construir uma forte base de identidade.
services: active-directory
author: martincoetzer
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/20/2019
ms.subservice: hybrid
ms.author: martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: d3eb98f543e17981be0d5b9ab08fa4e146659b47
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74206789"
---
# <a name="four-steps-to-a-strong-identity-foundation-with-azure-active-directory"></a>Quatro passos para uma forte fundação de identidade com o Azure Active Directory

O gerenciamento do acesso a aplicativos e dados não pode mais contar com as estratégias tradicionais de limite de segurança de rede, como redes de perímetro e firewalls, devido à rápida movimentação de aplicativos para a nuvem. Agora, as organizações devem confiar em sua solução de identidade para controlar quem e o que tem acesso aos aplicativos e dados da organização. Mais organizações estão permitindo que os funcionários tragam seus próprios dispositivos para trabalhar e usar seus dispositivos de qualquer lugar que possam se conectar à Internet. Garantir que esses dispositivos estejam em conformidade e seguros tornou-se uma consideração importante na solução de identidade que uma organização escolhe implementar. No local de trabalho digital de hoje, [a identidade é o principal plano](https://www.microsoft.com/security/technology/identity-access-management?rtc=1) de controle de qualquer organização que se move para a nuvem.

Ao adotar uma solução de identidade híbrida do Azure Active Directory (Azure AD), as organizações ganham acesso a recursos premium que desbloqueiam a produtividade por meio de recursos de automação, delegação, autoatendimento e logon único. Ele permite que seus trabalhadores acessem os recursos da empresa de onde eles precisam para fazer seu trabalho, permitindo que sua equipe de TI governe esse acesso, garantindo que as pessoas certas tenham o acesso certo aos recursos certos para estabelecer uma produtividade segura.

Com base em nossos aprendizados, esta lista de práticas recomendadas ajudará você a implantar rapidamente ações recomendadas para construir uma *forte* base de identidade em sua organização:

* Conecte-se facilmente aos aplicativos
* Estabeleça uma identidade para cada usuário automaticamente
* Capacite seus usuários com segurança
* Operacionalize seus insights

## <a name="step-1---connect-to-apps-easily"></a>Passo 1 - Conecte-se facilmente aos aplicativos

Ao conectar seus aplicativos com o Azure AD, você pode melhorar a produtividade e a segurança do usuário final, ativando o logon único (SSO) e fazer o provisionamento do usuário. Ao gerenciar seus aplicativos em um único lugar, o Azure AD, você pode minimizar a sobrecarga administrativa e alcançar um único ponto de controle para suas políticas de segurança e conformidade.

Esta seção abrange suas opções para gerenciar o acesso do usuário a aplicativos, permitindo acesso remoto seguro a aplicativos internos e os benefícios de migrar seus aplicativos para o Azure AD.

### <a name="make-apps-available-to-your-users-seamlessly"></a>Disponibilize aplicativos para seus usuários perfeitamente

O Azure AD permite que os administradores [adicionem aplicativos](https://docs.microsoft.com/azure/active-directory/manage-apps/add-application-portal) à galeria de aplicativos Da Enterprise no [portal Azure](https://portal.azure.com/). A adição de aplicativos à galeria de aplicativos Corporativos torna mais fácil para você configurar aplicativos para usar o Azure AD como seu provedor de identidade. Ele também permite que você gerencie o acesso do usuário ao aplicativo com políticas de Acesso Condicional e configure o SSO (Single Log-On) para aplicativos para que os usuários não precisem digitar suas senhas repetidamente e sejam automaticamente inseridos no local e aplicativos baseados em nuvem.

Uma vez que os aplicativos são adicionados à galeria Ad do Azure, os usuários podem ver aplicativos que são atribuídos a eles e pesquisar e solicitar outros aplicativos conforme necessário. O Azure AD fornece [vários métodos](https://docs.microsoft.com/azure/active-directory/manage-apps/end-user-experiences) para os usuários acessarem seus aplicativos:

* Painel de acesso/Meus aplicativos
* Iniciador do aplicativo do Office 365
* Logon direto a aplicativos federados
* Links diretos logon

Para saber mais sobre o acesso do usuário aos aplicativos, consulte **passo 3 -- Capacite seus usuários** neste artigo.

### <a name="migrate-apps-from-active-directory-federation-services-to-azure-ad"></a>Migrar aplicativos dos Serviços ativos da Federação de Diretórios para o Azure AD

A migração da configuração de login único do Active Directory Federation Services (ADFS) para o Azure AD permite recursos adicionais sobre segurança, uma capacidade de gerenciamento mais consistente e colaboração. Para obter resultados ideais, recomendamos que você migre seus aplicativos do AD FS para o Azure AD. Trazer sua autenticação de aplicativo e autorização para o Azure AD fornece os seguintes benefícios:

* Gerenciar custos
* Gerenciamento de riscos
* Aumento da produtividade
* Abordando compliance e governança

Para saber mais, consulte o [whitepaper migrando suas aplicações para o](https://aka.ms/migrateapps/whitepaper) whitepaper do Azure Active Directory.

### <a name="enable-secure-remote-access-to-apps"></a>Habilite o acesso remoto seguro aos aplicativos

[O Azure AD Application Proxy](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-application-proxy) fornece uma solução simples para as organizações publicarem aplicativos locais na nuvem para usuários remotos que precisam acessar aplicativos internos de forma segura. Após um único login no Azure AD, os usuários podem acessar aplicativos em nuvem e no local através de URLs externos ou de um portal interno de aplicativos.

O Azure AD Application Proxy oferece os seguintes benefícios:

* Extensão do AD do Azure para recursos locais
  * Proteção e segurança em escala de nuvem
  * Recursos como acesso condicional e autenticação multifatorial que são fáceis de habilitar
* Sem componentes na rede de perímetro, como VPN e soluções tradicionais de proxy reverso
* Nenhuma conexão de entrada necessária
* SSO (Single Sign-on) em dispositivos, recursos e aplicativos na nuvem e no local
* Capacita os usuários finais a serem produtivos a qualquer hora e em qualquer lugar

### <a name="discover-shadow-it-with-microsoft-cloud-app-security"></a>Descubra shadow it com a Microsoft Cloud App Security

Nas empresas modernas, os departamentos de TI geralmente não estão cientes de todos os aplicativos de nuvem que são usados pelos usuários enquanto realizam seus trabalhos. Quando os admins de TI são questionados sobre quantos aplicativos de nuvem eles acham que seus funcionários usam, em média eles dizem 30 ou 40. Na realidade, a média é de mais de 1.000 aplicativos separados sendo usados pelos funcionários em sua organização. 80% dos funcionários usam aplicativos não sancionados que ninguém revisou e pode não estar em conformidade com suas políticas de segurança e conformidade.

[O Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) (MCAS) pode ajudá-lo a identificar aplicativos úteis que são populares entre os usuários que a TI pode sancionar e adicionar à galeria de aplicativos Corporativos para que os usuários se beneficiem de recursos como SSO e Conditional Access.

<em>"**A Cloud App Security** nos ajuda a garantir que nosso pessoal esteja usando corretamente nossos aplicativos cloud e SaaS, de maneiras que suportam as políticas de segurança fundamental que ajudam a proteger a Accenture."</em> --- [John Blasi, Diretor Geral, Segurança da Informação, Accenture](https://customers.microsoft.com/story/accenture-professional-services-cloud-app-security)

Além de detectar a TI sombra, o MCAS também pode determinar o nível de risco dos aplicativos, impedir o acesso não autorizado a dados corporativos, possíveis vazamentos de dados e outros riscos de segurança inerentes aos aplicativos.

## <a name="step-2---establish-one-identity-for-every-user-automatically"></a>Passo 2 - Estabelecer uma identidade para cada usuário automaticamente

Reunir diretórios locais e baseados em nuvem em uma solução de identidade híbrida Azure AD permitirá que você reutilize seu investimento existente no Active Directory, provisionando suas identidades existentes na nuvem. A solução sincroniza as identidades locais com o Azure AD, enquanto a TI mantém o Active Directory no local funcionando com quaisquer soluções de governança existentes como a principal fonte de verdade para identidades. A solução de identidade híbrida Azure AD da Microsoft abrange recursos locais e baseados em nuvem, criando uma identidade de usuário comum para autenticação e autorização para todos os recursos, independentemente de sua localização.

A integração de seus diretórios locais com o Azure AD torna seus usuários mais produtivos e impede que os usuários usem várias contas entre aplicativos e serviços, fornecendo uma identidade comum para acessar recursos em nuvem e no local. O uso de várias contas é um ponto de dor para usuários finais e de TI. Do ponto de vista do usuário final, ter várias contas significa ter que lembrar várias senhas. Para evitar isso, muitos usuários reutilizam a mesma senha para cada conta, o que é ruim do ponto de vista da segurança. Do ponto de vista de TI, a reutilização muitas vezes leva a mais redefinições de senha e custos de helpdesk, juntamente com as reclamações do usuário final.

O Azure AD Connect é a ferramenta usada para sincronizar suas identidades no local com o Azure AD, que pode ser usado para acessar aplicativos em nuvem. Uma vez que as identidades estejam no Azure AD, eles podem provisionar para aplicativos SaaS como Salesforce ou Concur.

Nesta seção, listamos recomendações para fornecer alta disponibilidade, autenticação moderna para a nuvem e reduzir sua pegada no local.

> [!NOTE]
> Se você quiser saber mais sobre o Azure AD Connect, veja [o que é O Azure AD Connect Sync?](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-whatis)

### <a name="set-up-a-staging-server-for-azure-ad-connect-and-keep-it-up-to-date"></a>Configure um servidor de preparação para o Azure AD Connect e mantenha-o atualizado

O Azure AD Connect desempenha um papel fundamental no processo de provisionamento. Se o Sync Server ficar offline por qualquer motivo, as alterações no local não serão atualizadas na nuvem e causarão problemas de acesso aos usuários. É importante definir uma estratégia de failover que permita aos administradores retomar rapidamente a sincronização depois que o servidor de sincronização ficar off-line.

Para fornecer alta disponibilidade no caso de seu servidor principal do Azure AD Connect ficar offline, é recomendável que você implante um [servidor de preparação](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-staging-server) separado para o Azure AD Connect. A implantação de um servidor permite que o administrador "promova" o servidor de estágio para a produção por um simples switch de configuração. Ter um servidor de espera configurado no modo de preparação também permite testar e implantar novas alterações de configuração e introduzir um novo servidor se desativar o antigo.

> [!TIP]
> O Azure AD Connect é atualizado regularmente. Portanto, é fortemente recomendável que você mantenha o servidor de estágio atualizado para aproveitar as melhorias de desempenho, correções de bugs e novos recursos que cada nova versão fornece.

### <a name="enable-cloud-authentication"></a>Habilitar a autenticação na nuvem

As organizações com diretório ativo no local devem estender seu diretório ao Azure AD usando o Azure AD Connect e configurar o método de autenticação apropriado. [Escolher o método de autenticação correto](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn) para sua organização é o primeiro passo em sua jornada de mover aplicativos para a nuvem. É um componente crítico, pois controla o acesso a todos os dados e recursos da nuvem.

O método mais simples e recomendado para habilitar a autenticação em nuvem para objetos de diretório no local no Azure AD é habilitar a sincronização de hash (PHS) [de senha.](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-password-hash-synchronization) Alternativamente, algumas organizações podem considerar a habilitação da [Autenticação de Passagem](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta-quick-start) (PTA).

Se você escolher PHS ou PTA, não se esqueça de ativar o [Semsinal Único para](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso) permitir que os usuários acessem aplicativos na nuvem sem inserir constantemente seu nome de usuário e senha no aplicativo ao usar dispositivos Windows 7 e 8 em sua rede corporativa. Sem o único login, os usuários devem lembrar senhas específicas do aplicativo e entrar em cada aplicativo. Da mesma forma, a equipe de TI precisa criar e atualizar contas de usuário para cada aplicativo, como Office 365, Box e Salesforce. Os usuários precisam lembrar suas senhas, além de gastar o tempo para entrar em cada aplicativo. Fornecer um mecanismo de sinalização único padronizado para toda a empresa é crucial para a melhor experiência do usuário, redução de risco, capacidade de relatório e governança.

Para organizações que já usam AD FS ou outro provedor de autenticação local, a mudança para o Azure AD como seu provedor de identidade pode reduzir a complexidade e melhorar a disponibilidade. A menos que você tenha casos de uso específicos para usar a federação, recomendamos migrar da autenticação federada para phs e SSO sem emendas ou PTA e SSO sem emenda para desfrutar dos benefícios de uma pegada reduzida no local e da flexibilidade que a nuvem oferece com melhores experiências do usuário. Para obter mais informações, consulte [Migrar da federação para a sincronização de hash de senha para o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/hybrid/plan-migrate-adfs-password-hash-sync).

### <a name="enable-automatic-deprovisioning-of-accounts"></a>Habilitar o deprovisionamento automático das contas

Permitir o provisionamento automatizado e o desprovisionamento de seus aplicativos é a melhor estratégia para governar o ciclo de vida das identidades em vários sistemas. O Azure AD suporta [provisionamento automatizado baseado em políticas e desprovisionamento](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-automatic-user-provisioning-portal) de contas de usuário para uma variedade de aplicativos SaaS populares, como ServiceNow e Salesforce, e outros que implementam o [protocolo SCIM 2.0.](https://docs.microsoft.com/azure/active-directory/manage-apps/use-scim-to-provision-users-and-groups) Ao contrário das soluções tradicionais de provisionamento, que exigem código personalizado ou upload manual de arquivos CSV, o serviço de provisionamento está hospedado na nuvem e possui conectores pré-integrados que podem ser configurados e gerenciados usando o portal Azure. Um dos principais benefícios do desprovisionamento automático é que ele ajuda a proteger sua organização removendo instantaneamente as identidades dos usuários dos principais aplicativos SaaS quando eles saem da organização.

Para saber mais sobre o provisionamento de contas de usuário automático e como ele funciona, confira [Automatizar o provisionamento e o desprovisionamento de usuário para aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

## <a name="step-3---empower-your-users-securely"></a>Passo 3 - Capacitar seus usuários com segurança

No local de trabalho digital de hoje, é importante equilibrar segurança com produtividade. No entanto, os usuários finais muitas vezes empurram para trás em medidas de segurança que reduzem sua produtividade e acesso a aplicativos em nuvem. Para ajudar a resolver isso, o Azure AD fornece recursos de autoatendimento que permitem que os usuários permaneçam produtivos, minimizando as despesas administrativas.

Esta seção lista recomendações para remover o atrito de sua organização, capacitando seus usuários enquanto permanece vigilante.

### <a name="enable-self-service-password-reset-for-all-users"></a>Habilitar redefinição de senha de autoatendimento para todos os usuários

O [SSPR (Self-Service Password Reset, redefinição](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr) de senha de autoatendimento) do Azure oferece um meio simples para que os administradores de TI permitam que os usuários resete e desbloqueie suas senhas ou contas sem intervenção do administrador. O sistema inclui relatórios detalhados que acompanham quando os usuários acessam o sistema junto com as notificações para alertá-lo para uso indevido ou um abuso.

Por padrão, o Azure Active Directory desbloqueia contas quando ele executa uma redefinição de senha. No entanto, quando você habilita a integração do Azure AD Connect [no local,](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks#on-premises-integration)você também tem a opção de separar essas duas operações, que permitem que os usuários desbloqueiem sua conta sem ter que redefinir a senha.

### <a name="ensure-all-users-are-registered-for-mfa-and-sspr"></a>Certifique-se de que todos os usuários estejam registrados para MFA e SSPR

O Azure fornece relatórios que podem ser usados por você e sua organização para garantir que os usuários estejam registrados para MFA e SSPR. Os usuários que não se registraram podem precisar ser educados sobre o processo.

O [relatório de logins do](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-reporting) MFA inclui informações sobre o uso do MFA e fornece insights sobre como o MFA está trabalhando em sua organização. Ter acesso à atividade de login (e auditorias e detecções de risco) para o Azure AD é crucial para a solução de problemas, análises de uso e investigações forenses.

Da mesma forma, o [relatório de gerenciamento de senhas de autoatendimento](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-reporting) pode ser usado para determinar quem (ou não) se registrou no SSPR.

### <a name="self-service-app-management"></a>Gerenciamento de aplicativos de autoatendimento

Antes que seus usuários possam se autodescobrir aplicativos a partir de seu painel de acesso, você precisa habilitar o acesso de [aplicativos de autoatendimento](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-manage-self-service-access) a quaisquer aplicativos aos quais você deseja permitir que os usuários se autodescubram e solicitem acesso. O acesso a aplicativos de autoatendimento é uma ótima maneira de permitir que os usuários autodescubram aplicativos e, opcionalmente, permitem que o grupo de negócios aprove o acesso a esses aplicativos. Você pode permitir que o grupo de negócios gerencie as credenciais atribuídas a esses usuários para [aplicativos de logon único de senha](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-password-sso-gallery#configure-password-sso-for-an-azure-ad-gallery-app) direto de seus painéis de acesso.

### <a name="self-service-group-management"></a>Gerenciamento de grupo de autoatendimento

Atribuir usuários a aplicativos é melhor mapeado ao usar grupos, pois eles permitem grande flexibilidade e capacidade de gerenciar em escala:

* Baseado em atributos usando membros de grupo dinâmico
* Delegação para proprietários de aplicativos

O Azure AD fornece a capacidade de gerenciar o acesso aos recursos usando grupos de segurança e Grupos do Office 365. Esses grupos podem ser gerenciados por um proprietário de grupo que pode aprovar ou negar pedidos de associação e delegar o controle da associação do grupo. Conhecido como [gerenciamento de grupo de autoatendimento,](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-self-service-management)esse recurso economiza tempo permitindo que proprietários de grupos que não tenham uma função administrativa criem e gerenciem grupos sem ter que depender dos administradores para lidar com suas solicitações.

## <a name="step-4---operationalize-your-insights"></a>Passo 4 - Operacionalize seus insights

Auditoria e registro de eventos relacionados à segurança e alertas relacionados são componentes essenciais de uma estratégia eficiente para garantir que os usuários permaneçam produtivos e sua organização esteja segura. Registros de segurança e relatórios podem ajudar a responder perguntas como:

* Está usando o que está pagando?
* Há algo suspeito ou malicioso acontecendo no meu inquilino?
* Quem foi impactado durante um incidente de segurança?

Os logs e os relatórios de segurança fornecem um registro eletrônico das atividades suspeitas e ajudam a detectar padrões que podem indicar uma tentativa de penetração externa ou uma penetração externa com êxito da rede, bem como ataques internos. Você pode usar auditorias para monitorar a atividade do usuário, documentar a conformidade normativa, fazer análises forenses e muito mais. Os alertas fornecem notificações de eventos de segurança.

### <a name="assign-least-privileged-admin-roles-for-operations"></a>Atribuir funções de admin menos privilegiadas para operações

Ao pensar na sua abordagem às operações, há alguns níveis de administração a considerar. O primeiro nível coloca o ônus da administração sobre seus administradores globais. Sempre usando o papel de administrador global, pode ser apropriado para empresas menores. Mas para organizações maiores com pessoal de help desk e administradores responsáveis por tarefas específicas, atribuir o papel de administrador global pode ser um risco de segurança, uma vez que fornece a esses indivíduos a capacidade de gerenciar tarefas que estão acima e além o que eles devem ser capazes de fazer.

Neste caso, você deve considerar o próximo nível de administração. Usando o Azure AD, você pode designar os usuários finais como "administradores limitados" que podem gerenciar tarefas em funções menos privilegiadas. Por exemplo, você pode atribuir ao seu pessoal do help desk a função [de leitor](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#security-reader) de segurança para fornecer a eles a capacidade de gerenciar recursos relacionados à segurança com acesso somente leitura. Ou talvez faça sentido atribuir a função de administrador de [autenticação](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#authentication-administrator) aos indivíduos para lhes dar a capacidade de redefinir credenciais não-senha ou ler e configurar o Azure Service Health.

Para saber mais, consulte [as permissões de função administradorno Diretório Ativo do Azure](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles).

### <a name="monitor-hybrid-components-azure-ad-connect-sync-ad-fs-using-azure-ad-connect-health"></a>Monitore componentes híbridos (Sincronização Azure AD Connect, AD FS) usando o Azure AD Connect Health

O Azure AD Connect e o AD FS são componentes críticos que podem potencialmente quebrar o gerenciamento e a autenticação do ciclo de vida e, em última instância, levar a paralisações. Portanto, você deve implantar o Azure AD Connect Health para monitorar e relatar esses componentes.

Para saber mais, vá ler [Monitor AD FS usando a Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-adfs).

### <a name="use-azure-monitor-to-collect-data-logs-for-analytics"></a>Use o Azure Monitor para coletar registros de dados para análise

[O Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) é um portal de monitoramento unificado para todos os logs Azure AD, que fornece insights profundos, análises avançadas e aprendizado inteligente de máquina. Com o Azure Monitor, você pode consumir métricas e logs dentro do portal e via APIs para ganhar mais visibilidade sobre o estado e o desempenho de seus recursos. Ele permite um único painel de experiência de vidro dentro do portal, permitindo uma ampla gama de integrações de produtos através de APIs e opções de exportação de dados que suportam sistemas SIEM tradicionais de terceiros. O Azure Monitor também oferece a capacidade de configurar regras de alerta para ser notificado ou tomar ações automatizadas sobre problemas que afetam seus recursos.

![Azure Monitor](./media/four-steps/image1.png)

### <a name="create-custom-dashboards-for-your-leadership-and-your-day-to-day"></a>Crie dashboards personalizados para sua liderança e seu dia-a-dia

As organizações que não possuem uma solução SIEM podem baixar o [Power BI Content Pack](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-power-bi-content-pack) para a Azure AD. O pacote de conteúdo do Power BI contém relatórios pré-construídos para ajudá-lo a entender como seus usuários adotam e usam recursos ad do Azure, o que permite obter insights sobre todas as atividades dentro do seu diretório. Você também pode criar seu próprio [painel personalizado](https://docs.microsoft.com/power-bi/service-dashboards) e compartilhar com sua equipe de liderança para relatar atividades do dia-a-dia. Os dashboards são uma ótima maneira de monitorar o seu negócio e ver todas as suas métricas mais importantes rapidamente. As visualizações em um dashboard podem vir de um conjunto de dados subjacente, ou de muitos, e de um relatório subjacente, ou de muitos. Um dashboard combina dados locais e na nuvem, proporcionando uma exibição consolidada independentemente do local em que os dados residem.

![Painel personalizado power BI](./media/four-steps/image2.png)

### <a name="understand-your-support-call-drivers"></a>Entenda seus drivers de chamada de suporte

Quando você implementa uma solução de identidade híbrida como descrito neste artigo, você deve finalmente notar uma redução em suas chamadas de suporte. Problemas comuns, como senhas esquecidas e bloqueios de contas, são mitigados pela implementação da redefinição de senha de autoatendimento do Azure, ao mesmo tempo em que a ativação do acesso a aplicativos de autoatendimento permite que os usuários se autodescubram e solicitem acesso a aplicativos sem depender em sua equipe de TI.

Se você não observar uma redução nas chamadas de suporte, recomendamos que você analise seus drivers de chamada de suporte na tentativa de confirmar se o acesso a aplicativos sspr ou autoatendimento foi configurado corretamente ou se há outros novos problemas que podem ser sistematicamente Abordados.

*"Em nossa jornada de transformação digital, precisávamos de um provedor confiável de gerenciamento de identidade e acesso para facilitar a integração perfeita e segura entre nós, parceiros e provedores de serviços em nuvem, para um ecossistema eficaz; O Azure AD foi a melhor opção, oferecendo-nos os recursos e a visibilidade necessários que nos permitiram detectar e responder aos riscos."* --- [Yazan Almasri, Diretor Global de Segurança da Informação, Aramex](https://customers.microsoft.com/story/aramex-azure-active-directory-travel-transportation-united-arab-emirates-en)

### <a name="monitor-your-usage-of-apps-to-drive-insights"></a>Monitore seu uso de aplicativos para gerar insights

Além de descobrir o Shadow IT, monitorar o uso de aplicativos em toda a sua organização usando [o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) pode ajudar sua organização à medida que você se move para aproveitar ao máximo a promessa de aplicativos em nuvem. Ele pode ajudar a mantê-lo no controle de seus ativos através de uma melhor visibilidade na atividade e aumentar a proteção de dados críticos em aplicativos de nuvem. Monitorar o uso de aplicativos em sua organização usando MCAS pode ajudá-lo a responder as seguintes perguntas:

* Em que aplicativos não autorizados os funcionários estão usando para armazenar dados?
* Onde e quando os dados confidenciais estão sendo armazenados na nuvem?
* Quem está acessando dados confidenciais na nuvem?

*"Com a Cloud App Security, podemos detectar rapidamente anomalias e agir."* --- [Eric LePenske, Gerente Sênior, Segurança da Informação, Accenture](https://customers.microsoft.com/story/accenture-professional-services-cloud-app-security)

## <a name="summary"></a>Resumo

Existem muitos aspectos para implementar uma solução híbrida de identidade, mas essa lista de verificação de quatro etapas ajudará você a realizar rapidamente uma infra-estrutura de identidade que permitirá que os usuários sejam mais produtivos e seguros.

* Conecte-se facilmente aos aplicativos
* Estabeleça uma identidade para cada usuário automaticamente
* Capacite seus usuários com segurança
* Operacionalize seus insights

Esperamos que este documento seja um roteiro útil para estabelecer uma forte base de identidade para sua organização.

## <a name="identity-checklist"></a>Lista de verificação de identidade

Recomendamos que você imprima a seguinte lista de verificação para referência à medida que você começa sua jornada para uma base de identidade mais sólida em sua organização.

### <a name="today"></a>Hoje

|Pronto?|Item|
|:-|:-|
||Redefinição de senha de auto-serviço piloto (SSPR) para um grupo|
||Monitore componentes híbridos usando o Azure AD Connect Health|
||Atribuir funções de admin menos privilegiadas para operação|
||Descubra shadow it com a Microsoft Cloud App Security|
||Use o Azure Monitor para coletar registros de dados para análise|

### <a name="next-two-weeks"></a>Próximas duas semanas

|Pronto?|Item|
|:-|:-|
||Disponibilize um aplicativo para seus usuários|
||Provisionamento ad piloto do Azure para um aplicativo saas de escolha|
||Configure um servidor de preparação para o Azure AD Connect e mantenha-o atualizado|
||Comece a migrar aplicativos do ADFS para o Azure AD|
||Crie dashboards personalizados para sua liderança e seu dia-a-dia|

### <a name="next-month"></a>No próximo mês.

|Pronto?|Item|
|:-|:-|
||Monitore seu uso de aplicativos para gerar insights|
||Piloto de acesso remoto seguro a aplicativos|
||Certifique-se de que todos os usuários estejam registrados para MFA e SSPR|
||Habilitar a autenticação na nuvem|

### <a name="next-three-months"></a>Próximos três meses

|Pronto?|Item|
|:-|:-|
||Habilite o gerenciamento de aplicativos de autoatendimento|
||Habilite o gerenciamento de grupos de autoatendimento|
||Monitore seu uso de aplicativos para gerar insights|
||Entenda seus drivers de chamada de suporte|

## <a name="next-steps"></a>Próximas etapas

Saiba como aumentar sua postura segura usando os recursos do Azure Active Directory e desta lista de verificação de cinco etapas - [Cinco passos para proteger sua infra-estrutura](https://aka.ms/securitysteps)de identidade .

Saiba como os recursos de identidade no Azure AD podem ajudá-lo a acelerar sua transição para o gerenciamento regido em nuvem, fornecendo soluções e recursos que permitem que as organizações adotem e movam mais de seu gerenciamento de identidade a partir de sistemas tradicionais locais para o Azure AD - [Como o Azure AD oferece gerenciamento regido em nuvem para cargas de trabalho on-premises.](https://aka.ms/cloudgoverned)
