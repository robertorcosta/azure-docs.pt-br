---
title: Designa aplicativos seguros no Microsoft Azure
description: Este artigo discute as melhores práticas a serem consideradas durante as fases de exigência e design do seu projeto de aplicação web.
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/11/2019
ms.topic: article
ms.service: security
ms.subservice: security-develop
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 889897cfd4dc8714ae3aea556f0924c9dbcd7825
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78299407"
---
# <a name="design-secure-applications-on-azure"></a>Designa aplicativos seguros no Azure
Neste artigo apresentamos atividades de segurança e controles a considerar quando você projeta aplicativos para a nuvem. Os recursos de treinamento, juntamente com questões de segurança e conceitos a serem considerados durante os requisitos e fases de design do [Microsoft Security Development Lifecycle (SDL)](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) são cobertos. O objetivo é ajudá-lo a definir atividades e serviços do Azure que você pode usar para projetar um aplicativo mais seguro.

As seguintes fases de SDL são abordadas neste artigo:

- Treinamento
- Requisitos
- Design

## <a name="training"></a>Treinamento
Antes de começar a desenvolver seu aplicativo na nuvem, tire um tempo para entender a segurança e a privacidade no Azure. Ao dar esse passo, você pode reduzir o número e a gravidade das vulnerabilidades exploráveis em seu aplicativo. Você estará mais preparado para reagir adequadamente ao cenário de ameaças em constante mudança.

Use os seguintes recursos durante a fase de treinamento para se familiarizar com os serviços do Azure disponíveis para desenvolvedores e com as práticas recomendadas de segurança no Azure:

  - [O guia do desenvolvedor para o Azure](https://azure.microsoft.com/campaigns/developer-guide/) mostra como começar com o Azure. O guia mostra quais serviços você pode usar para executar seus aplicativos, armazenar seus dados, incorporar inteligência, criar aplicativos de IoT e implantar suas soluções de forma mais eficiente e segura.

  - [Obtenha o guia para desenvolvedores do Azure](../../guides/developer/azure-developer-guide.md) fornece informações essenciais para desenvolvedores que estão procurando começar a usar a plataforma Azure para suas necessidades de desenvolvimento.

  - [SDKs e ferramentas](https://docs.microsoft.com/azure/index?pivot=sdkstools) descrevem as ferramentas disponíveis no Azure.

  - [O Azure DevOps Services](https://docs.microsoft.com/azure/devops/) fornece ferramentas de colaboração de desenvolvimento. As ferramentas incluem pipelines de alto desempenho, repositórios git gratuitos, placas Kanban configuráveis e extensos testes automatizados e baseados em nuvem.
    O [DevOps Resource Center](https://docs.microsoft.com/azure/devops/learn/) combina nossos recursos para aprender práticas de DevOps, controle de versão do Git, métodos ágeis, como trabalhamos com DevOps na Microsoft e como você pode avaliar sua própria progressão de DevOps.

  - [Os 5 principais itens de segurança a considerar antes de empurrar para a produção](https://docs.microsoft.com/learn/modules/top-5-security-items-to-consider/index?WT.mc_id=Learn-Blog-tajanca) mostram como ajudar a proteger seus aplicativos web no Azure e proteger seus aplicativos contra os ataques mais comuns e perigosos de aplicativos da Web.

  - [Secure DevOps Kit for Azure](https://azsk.azurewebsites.net/index.html) é uma coleção de scripts, ferramentas, extensões e automações que atende às necessidades abrangentes de assinatura e segurança de recursos do Azure de equipes de DevOps que usam automação extensiva. O Kit DevOps Seguro para Azure pode mostrar como integrar a segurança suavemente aos seus fluxos de trabalho DevOps nativos. O kit aborda ferramentas como testes de verificação de segurança (SVTs), que podem ajudar os desenvolvedores a escrever código seguro e testar a configuração segura de seus aplicativos em nuvem nos estágios de codificação e desenvolvimento inicial.

  - [As práticas recomendadas de segurança para soluções Azure](https://azure.microsoft.com/resources/security-best-practices-for-azure-solutions) fornecem uma coleção de práticas recomendadas de segurança para usar à medida que você projeta, implanta e gerencia suas soluções em nuvem usando o Azure.

## <a name="requirements"></a>Requisitos
A fase de definição de requisitos é um passo crucial na definição do que seu aplicativo é e o que ele fará quando for lançado. A fase de requisitos também é um momento para pensar sobre os controles de segurança que você irá construir em sua aplicação. Durante esta fase, você também inicia as etapas que você tomará em todo o SDL para garantir que você libere e implante um aplicativo seguro.

### <a name="consider-security-and-privacy-issues"></a>Considere questões de segurança e privacidade
Esta fase é o melhor momento para considerar questões fundamentais de segurança e privacidade. Definir níveis aceitáveis de segurança e privacidade no início de um projeto ajuda uma equipe:

- Entenda os riscos associados a problemas de segurança.
- Identifique e corrija bugs de segurança durante o desenvolvimento.
- Aplique níveis estabelecidos de segurança e privacidade durante todo o projeto.

Ao escrever os requisitos para o seu aplicativo, considere os controles de segurança que podem ajudar a manter seu aplicativo e dados seguros.

### <a name="ask-security-questions"></a>Faça perguntas de segurança
Faça perguntas de segurança como:

  - Meu aplicativo contém dados confidenciais?

  - Meu aplicativo coleta ou armazena dados que exigem que eu siga os padrões do setor e programas de conformidade, como o [Federal Financial Institution Examination Council (FFIEC)](../blueprints/ffiec-analytics-overview.md) ou o Payment Card Industry Data Security Standards [(PCI DSS)](../blueprints/pcidss-analytics-overview.md)?

  - Meu aplicativo coleta ou contém dados pessoais ou de clientes sensíveis que podem ser usados, por conta própria ou com outras informações, para identificar, contatar ou localizar uma única pessoa?

  - Meu aplicativo coleta ou contém dados que podem ser usados para acessar as informações médicas, educacionais, financeiras ou de emprego de um indivíduo? Identificar a sensibilidade de seus dados durante a fase de requisitos ajuda você a classificar seus dados e identificar o método de proteção de dados que você usará para sua aplicação.

  - Onde e como meus dados são armazenados? Considere como você monitorará os serviços de armazenamento que seu aplicativo usa para quaisquer alterações inesperadas (como tempos de resposta mais lentos). Você será capaz de influenciar o registro para coletar dados mais detalhados e analisar um problema em profundidade?

  - Será que minha inscrição estará disponível para o público (na internet) ou apenas internamente? Se sua aplicação estiver disponível ao público, como você protege os dados que podem ser coletados de serem usados de forma errada? Se sua aplicação estiver disponível apenas internamente, considere quem em sua organização deve ter acesso ao aplicativo e quanto tempo eles devem ter acesso.

  - Você entende seu modelo de identidade antes de começar a projetar sua aplicação? Como você determinará que os usuários são quem dizem ser e o que um usuário está autorizado a fazer?

  - Minha aplicação executa tarefas sensíveis ou importantes (como transferir dinheiro, abrir portas ou entregar medicamentos)?
    Considere como você validará que o usuário que executa uma tarefa sensível está autorizado a executar a tarefa e como você autenticará que a pessoa é quem ela diz ser. Autorização (AuthZ) é o ato de conceder uma permissão de segurança autenticada para fazer algo. Autenticação (AuthN) é o ato de desafiar uma parte por credenciais legítimas.

  - Meu aplicativo executa atividades de software arriscadas, como permitir que os usuários carreguem ou baixem arquivos ou outros dados? Se o seu aplicativo realizar atividades arriscadas, considere como seu aplicativo protegerá os usuários de lidar com arquivos ou dados maliciosos.

### <a name="review-owasp-top-10"></a>Review OWASP top 10
Considere revisar os [<span class="underline">10 principais riscos de segurança de aplicativos da OWASP.</span>](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project)
O OWASP Top 10 aborda riscos críticos de segurança para aplicativos web.
A conscientização sobre esses riscos de segurança pode ajudá-lo a tomar decisões de exigência e design que minimizem esses riscos em sua aplicação.

Pensar em controles de segurança para evitar violações é importante.
No entanto, você também quer [assumir que uma brecha](https://docs.microsoft.com/azure/devops/learn/devops-at-microsoft/security-in-devops) ocorrerá. Assumindo que uma violação ajuda a responder algumas perguntas importantes sobre segurança com antecedência, para que eles não tenham que ser respondidos em uma emergência:

  - Como vou detectar um ataque?

  - O que farei se houver um ataque ou violação?

  - Como vou me recuperar do ataque como vazamento de dados ou adulteração?

## <a name="design"></a>Design

A fase de design é fundamental para estabelecer as melhores práticas de design e especificações funcionais. Também é fundamental para a realização de análises de risco que ajudam a mitigar problemas de segurança e privacidade ao longo de um projeto.

Quando você tem requisitos de segurança no local e usa conceitos de design seguro, você pode evitar ou minimizar as oportunidades para uma falha de segurança. Uma falha de segurança é um descuido no design do aplicativo que pode permitir que um usuário realize ações maliciosas ou inesperadas após o lançamento do seu aplicativo.

Durante a fase de design, pense também em como você pode aplicar segurança em camadas; um nível de defesa não é necessariamente suficiente. O que acontece se um invasor passar pelo firewall de aplicativos web (WAF)? Você quer outro controle de segurança para se defender desse ataque.

Com isso em mente, discutimos os seguintes conceitos de design seguro e os controles de segurança que você deve abordar ao projetar aplicativos seguros:

- Use uma biblioteca de codificação segura e uma estrutura de software.
- Procure componentes vulneráveis.
- Use modelagem de ameaças durante o projeto do aplicativo.
- Reduza sua superfície de ataque.
- Adote uma política de identidade como o perímetro de segurança primário.
- Exija reautenticação para transações importantes.
- Use uma solução de gerenciamento de chaves para proteger chaves, credenciais e outros segredos.
- Proteger dados confidenciais.
- Implementar medidas de segurança contra falhas.
- Aproveite o erro e o manuseio de exceções.
- Use registro e alerta.

### <a name="use-a-secure-coding-library-and-a-software-framework"></a>Use uma biblioteca de codificação segura e uma estrutura de software

Para o desenvolvimento, use uma biblioteca de codificação segura e uma estrutura de software que tenha segurança incorporada. Os desenvolvedores podem usar recursos existentes e comprovados (criptografia, saneamento de entrada, codificação de saída, chaves ou strings de conexão, e qualquer outra coisa que seja considerada um controle de segurança) em vez de desenvolver controles de segurança do zero. Isso ajuda a proteger contra falhas de projeto e implementação relacionadas à segurança.

Certifique-se de que você está usando a versão mais recente do seu framework e todos os recursos de segurança disponíveis no framework. A Microsoft oferece um conjunto abrangente [de ferramentas de desenvolvimento](https://azure.microsoft.com/product-categories/developer-tools/) para todos os desenvolvedores, trabalhando em qualquer plataforma ou idioma, para fornecer aplicativos em nuvem. Você pode codificar com o idioma de sua escolha escolhendo entre vários [SDKs](https://azure.microsoft.com/downloads/).
Você pode aproveitar ambientes de desenvolvimento integrado completos (IDEs) e editores que têm recursos avançados de depuração e suporte integrado ao Azure.

A Microsoft oferece uma variedade de [idiomas, frameworks e ferramentas](https://docs.microsoft.com/azure/index?pivot=sdkstools&panel=sdkstools-all) que você pode usar para desenvolver aplicativos no Azure. Um exemplo é [o Azure para desenvolvedores .NET e .NET Core](https://docs.microsoft.com/dotnet/azure/). Para cada idioma e estrutura que oferecemos, você encontrará partidas rápidas, tutoriais e referências de API para ajudá-lo a começar rápido.

O Azure oferece uma variedade de serviços que você pode usar para hospedar sites e aplicativos web. Esses serviços permitem que você se desenvolva em seu idioma favorito, seja .NET, .NET Core, Java, Ruby, Node.js, PHP ou Python.
[O Azure App Service Web Apps](../../app-service/overview.md) (Web Apps) é um desses serviços.

O Web Apps adiciona o poder do Microsoft Azure ao seu aplicativo. Inclui segurança, balanceamento de carga, autodimensionamento e gerenciamento automatizado. Você também pode aproveitar os recursos do DevOps em Aplicativos Web, como gerenciamento de pacotes, ambientes de preparação, domínios personalizados, certificados SSL/TLS e implantação contínua do Azure DevOps, GitHub, Docker Hub e outras fontes.

O Azure oferece outros serviços que você pode usar para hospedar sites e aplicativos web. Os Aplicativos Web são a melhor opção para a maioria dos cenários. Para uma arquitetura de micro serviços, considere [o Azure Service Fabric](https://azure.microsoft.com/documentation/services/service-fabric).
Se você precisar de mais controle sobre as VMs nas quais seu código é executado, considere as [Máquinas Virtuais do Azure](https://azure.microsoft.com/documentation/services/virtual-machines/).
Para obter mais informações sobre como escolher entre esses serviços do Azure, consulte uma [comparação do Azure App Service, Virtual Machines, Service Fabric e Cloud Services](/azure/architecture/guide/technology-choices/compute-decision-tree).

### <a name="apply-updates-to-components"></a>Aplicar atualizações aos componentes

Para evitar vulnerabilidades, você deve fazer um inventário contínuo tanto dos componentes do lado do cliente quanto do lado do servidor (por exemplo, frameworks e bibliotecas) e suas dependências para atualizações. Novas vulnerabilidades e versões atualizadas de software são lançadas continuamente. Certifique-se de que você tem um plano contínuo para monitorar, triagem e aplicar atualizações ou alterações de configuração nas bibliotecas e componentes que você usa.

Consulte a página [OWASP (Open Web Application Security Project)](https://www.owasp.org/index.php/Main_Page) sobre [o uso de componentes com vulnerabilidades conhecidas](https://www.owasp.org/index.php/Top_10-2017_A9-Using_Components_with_Known_Vulnerabilities) para sugestões de ferramentas. Você também pode assinar alertas de e-mail para vulnerabilidades de segurança relacionadas aos componentes que você usa.

### <a name="use-threat-modeling-during-application-design"></a>Usar a modelagem de ameaças durante o design do aplicativo

Modelagem de ameaças é o processo de identificar potenciais ameaças à segurança de sua empresa e aplicativo e, em seguida, garantir que as mitigações adequadas estejam em vigor. O SDL especifica que as equipes devem se envolver na modelagem de ameaças durante a fase de projeto, quando a resolução de problemas potenciais é relativamente fácil e econômica. O uso de modelagem de ameaças na fase de design pode reduzir consideravelmente o seu custo total de desenvolvimento.

Para ajudar a facilitar o processo de modelagem de ameaças, projetamos a [Ferramenta de Modelagem de Ameaças SDL](threat-modeling-tool.md) com especialistas não-segurança em mente. Esta ferramenta facilita a modelagem de ameaças para todos os desenvolvedores, fornecendo orientação clara sobre como criar e analisar modelos de ameaças.

Modelando o design do aplicativo e enumerando ameaças [do STRIDE](https://docs.google.com/viewer?a=v&pid=sites&srcid=ZGVmYXVsdGRvbWFpbnxzZWN1cmVwcm9ncmFtbWluZ3xneDo0MTY1MmM0ZDI0ZjQ4ZDMy) — Spoofing, Tampering, Repúdio, Divulgação de Informações, Negação de Serviço e Elevação de Privilégios — em todos os limites de confiança provou ser uma maneira eficaz de capturar erros de design no início. A tabela a seguir lista as ameaças do STRIDE e dá alguns exemplos de mitigações que usam recursos fornecidos pelo Azure. Essas mitigações não funcionarão em todas as situações.

| Ameaça | Propriedade de segurança | Potencial mitigação da plataforma Azure |
| ---------------------- | --------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Falsificação               | Autenticação        | [Requerem conexões HTTPS](https://docs.microsoft.com/aspnet/core/security/enforcing-ssl?view=aspnetcore-2.1&tabs=visual-studio). |
| Violação              | Integridade             | Valide os certificados SSL/TLS. Os aplicativos que usam SSL/TLS devem verificar totalmente os certificados X.509 das entidades a que se conectam. Use os certificados do Azure Key Vault para [gerenciar seus certificados x509](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-certificates). |
| Repúdio            | Não repúdio       | Habilitar o [monitoramento e diagnóstico](https://docs.microsoft.com/azure/architecture/best-practices/monitoring) do Azure.|
| Revelação de informações | Confidencialidade       | Criptografe dados confidenciais [em repouso](../fundamentals/encryption-atrest.md) e [em trânsito](../fundamentals/data-encryption-best-practices.md#protect-data-in-transit). |
| Negação de Serviço      | Disponibilidade          | Monitore as métricas de desempenho para possíveis condições de negação de serviço. Implementar filtros de conexão. [A proteção Azure DDoS](../../virtual-network/ddos-protection-overview.md#next-steps), combinada com as práticas recomendadas de design de aplicativos, fornece defesa contra ataques DDoS.|
| Elevação de privilégio | Autorização         | Use o Azure <span class="underline"> </span> Active Directory [Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md).|

### <a name="reduce-your-attack-surface"></a>Reduza sua superfície de ataque

Uma superfície de ataque é a soma total de onde possíveis vulnerabilidades podem ocorrer. Neste artigo, focamos na superfície de ataque de uma aplicação.
O foco é proteger uma aplicação contra ataques. Uma maneira simples e rápida de minimizar sua superfície de ataque é remover recursos e códigos não utilizados do seu aplicativo. Quanto menor sua aplicação, menor sua superfície de ataque. Por exemplo, remova:

- Código para recursos que você ainda não liberou.
- Depuração do código de suporte.
- Interfaces de rede e protocolos que não foram usados ou que foram preteridos.
- Máquinas virtuais e outros recursos que você não está usando.

Fazer a limpeza regular de seus recursos e garantir que você remova códigos não utilizados são ótimas maneiras de garantir que haja menos oportunidades para atores mal-intencionados atacarem.

Uma maneira mais detalhada e profunda de reduzir sua superfície de ataque é completar uma análise da superfície de ataque. Uma análise da superfície de ataque ajuda a mapear as partes de um sistema que precisam ser revisadas e testadas para vulnerabilidades de segurança.

O objetivo de uma análise de superfície de ataque é entender as áreas de risco em um aplicativo para que desenvolvedores e especialistas em segurança estejam cientes de quais partes do aplicativo estão abertas a ataques. Então, você pode encontrar maneiras de minimizar esse potencial, rastrear quando e como a superfície de ataque muda, e o que isso significa de uma perspectiva de risco.

Uma análise da superfície de ataque ajuda a identificar:

- Funções e partes do sistema você precisa rever e testar vulnerabilidades de segurança.
- Áreas de código de alto risco que requerem proteção de defesa em profundidade (partes do sistema que você precisa defender).
- Quando você altera a superfície de ataque e precisa atualizar uma avaliação de ameaça.

Reduzir as oportunidades para os invasores explorarem um ponto fraco ou vulnerabilidade em potencial exige que você analise minuciosamente a superfície de ataque geral do seu aplicativo. Também inclui desativar ou restringir o acesso aos serviços do sistema, aplicar o princípio do menor privilégio e empregar defesas em camadas sempre que possível.

Discutimos [a realização de uma revisão da superfície](secure-develop.md#conduct-attack-surface-review) de ataque durante a fase de verificação do SDL.

> [!NOTE]
> **Qual é a diferença entre modelagem de ameaças e análise de superfície de ataque?**
Modelagem de ameaças é o processo de identificar potenciais ameaças à segurança ao seu aplicativo e garantir que as mitigações adequadas contra as ameaças estejam em vigor. A análise da superfície de ataque identifica áreas de alto risco de código que estão abertas a ataques. Envolve encontrar maneiras de defender áreas de alto risco do seu aplicativo e revisar e testar essas áreas de código antes de implantar o aplicativo.

### <a name="adopt-a-policy-of-identity-as-the-primary-security-perimeter"></a>Adotar uma política de identidade como o perímetro de segurança primário

Quando você projeta aplicativos em nuvem, é importante expandir o foco do perímetro de segurança a partir de uma abordagem centrada na rede para uma abordagem centrada na identidade. Historicamente, o principal perímetro de segurança no local era a rede de uma organização. A maioria dos projetos de segurança no local usa a rede como pivô de segurança primária. Para aplicações em nuvem, você é melhor servido considerando a identidade como o perímetro de segurança principal.

Coisas que você pode fazer para desenvolver uma abordagem centrada na identidade para o desenvolvimento de aplicativos web:

- Imponha a autenticação multifatorial para os usuários.
- Use plataformas de autorização e autenticação fortes.
- Aplique o princípio do menor privilégio.
- Implementar acesso just-in-time.

#### <a name="enforce-multi-factor-authentication-for-users"></a>Impor autenticação multifatorial para usuários

Use a autenticação de dois fatores. A autenticação de dois fatores é o padrão atual de autenticação e autorização, pois evita as fraquezas de segurança inerentes aos tipos de autenticação de nome de usuário e senha. O acesso às interfaces de gerenciamento do Azure (portal Azure/PowerShell remoto) e aos serviços voltados para o cliente deve ser projetado e configurado para usar a [Autenticação Multifatorial do Azure.](../../active-directory/authentication/concept-mfa-howitworks.md)

#### <a name="use-strong-authentication-and-authorization-platforms"></a>Use plataformas de autenticação e autorização fortes

Use os mecanismos de autenticação e autorização fornecidos pela plataforma em vez de código personalizado. Isso porque o desenvolvimento de código de autenticação personalizado pode ser propenso a erros. O código comercial (por exemplo, da Microsoft) muitas vezes é amplamente revisado para segurança. [Azure Active Directory (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md) é a solução do Azure para gerenciamento de identidades e acesso. Essas ferramentas e serviços azure AD ajudam no desenvolvimento seguro:

- [A plataforma de identidade Azure AD (Azure AD para desenvolvedores)](../../active-directory/develop/about-microsoft-identity-platform.md) é um serviço de identidade na nuvem que os desenvolvedores usam para criar aplicativos que assinam com segurança os usuários. O Azure AD auxilia desenvolvedores que estão construindo aplicativos de linha de negócios (LOB) e desenvolvedores que estão procurando desenvolver aplicativos multi-inquilinos. Além do login básico, os aplicativos que são construídos usando o Azure AD podem chamar APIs da Microsoft e APIs personalizadas que são construídas na plataforma Azure AD. A plataforma de identidade Azure AD suporta protocolos padrão do setor, como o OAuth 2.0 e o OpenID Connect.

- [O Azure Active Directory B2C (Azure AD B2C)](../../active-directory-b2c/index.yml) é um serviço de gerenciamento de identidade que você pode usar para personalizar e controlar como os clientes se inscrevem, fazem login e gerenciam seus perfis quando usam seus aplicativos. Isso inclui aplicativos desenvolvidos para iOS, Android e .NET, entre outros. O Azure AD B2C permite essas ações enquanto protege as identidades dos clientes.

#### <a name="apply-the-principle-of-least-privilege"></a>Aplique o princípio do menor privilégio

O conceito de [menos privilégio](https://en.wikipedia.org/wiki/Principle_of_least_privilege) significa dar aos usuários o nível preciso de acesso e controle de que precisam para fazer seus trabalhos e nada mais.

Um desenvolvedor de software precisaria de direitos de admin de domínio? Um assistente administrativo precisaria de acesso a controles administrativos em seu computador pessoal? Avaliar o acesso ao software não é diferente. Se você usar [o RBAC (Role-based Access Control, controle de acesso baseado em função)](../../role-based-access-control/overview.md) para dar aos usuários diferentes habilidades e autoridade em seu aplicativo, você não daria a todos acesso a tudo. Ao limitar o acesso ao que é necessário para cada função, você limita o risco de um problema de segurança ocorrer.

Certifique-se de que seu aplicativo impõe [menos privilégios](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models#in-applications) em todos os seus padrões de acesso.

> [!NOTE]
>  As regras de menor privilégio precisam ser aplicadas ao software e às pessoas que criam o software. Os desenvolvedores de software podem ser um grande risco para a segurança de TI se tiverem muito acesso. As consequências podem ser severas se um desenvolvedor tiver intenção maliciosa ou tiver muito acesso. Recomendamos que as regras de menor privilégio sejam aplicadas aos desenvolvedores durante todo o ciclo de vida do desenvolvimento.

#### <a name="implement-just-in-time-access"></a>Implementar acesso just-in-time

Implementar o acesso *just-in-time* (JIT) para reduzir ainda mais o tempo de exposição dos privilégios. Use [o Azure AD Privileged Identity Management](../../active-directory/users-groups-roles/directory-admin-roles-secure.md#stage-3-build-visibility-and-take-full-control-of-admin-activity) para:

- Dê aos usuários as permissões de que eles precisam apenas de JIT.
- Atribua funções por uma duração reduzida com confiança de que os privilégios são revogados automaticamente.

### <a name="require-re-authentication-for-important-transactions"></a>Exigir reautenticação para transações importantes

[A falsificação de solicitações entre sites](https://docs.microsoft.com/aspnet/core/security/anti-request-forgery?view=aspnetcore-2.1) (também conhecida como *XSRF* ou *CSRF)* é um ataque contra aplicativos hospedados na Web, no qual um aplicativo web mal-intencionado influencia a interação entre um navegador cliente e um aplicativo web que confia nesse navegador. Ataques de falsificação de solicitação entre sites são possíveis porque os navegadores da Web enviam alguns tipos de tokens de autenticação automaticamente a cada solicitação a um site.
Essa forma de exploração também é conhecida como *um ataque de um clique* ou uma sessão de *cavalgada* porque o ataque se aproveita da sessão autenticada anteriormente do usuário.

A melhor maneira de se defender contra esse tipo de ataque é pedir ao usuário algo que só o usuário pode fornecer antes de cada transação importante, como uma compra, desativação de conta ou uma mudança de senha. Você pode pedir ao usuário para reinserir sua senha, completar um captcha ou enviar um token secreto que só o usuário teria. A abordagem mais comum é o símbolo secreto.

### <a name="use-a-key-management-solution-to-secure-keys-credentials-and-other-secrets"></a>Use uma solução de gerenciamento de chaves para proteger chaves, credenciais e outros segredos

Perder chaves e credenciais é um problema comum. A única coisa pior do que perder as chaves e as credenciais é quando um terceiro não autorizado tem acesso a elas. Os atacantes podem aproveitar técnicas automatizadas e manuais para encontrar chaves e segredos armazenados em repositórios de código como o GitHub. Não coloque chaves e segredos nesses repositórios de código público ou em qualquer outro servidor.

Coloque sempre suas chaves, certificados, segredos e strings de conexão em uma solução de gerenciamento de chaves. Você pode usar uma solução centralizada na qual chaves e segredos são armazenados em módulos de segurança de hardware (HSMs). O Azure fornece um HSM na nuvem com [o Azure Key Vault](../../key-vault/key-vault-overview.md).

Key Vault é uma *loja secreta:* é um serviço de nuvem centralizado para armazenar segredos de aplicativos. O Key Vault mantém seus dados confidenciais seguros mantendo segredos de aplicativos em um único local central e fornecendo acesso seguro, controle de permissões e registro de acesso.

Segredos são *armazenados*em cofres individuais. Cada cofre tem sua própria configuração e políticas de segurança para controlar o acesso. Você chega aos seus dados através de uma API REST ou através de um SDK cliente que está disponível para a maioria das linguagens de programação.

> [!IMPORTANT]
> O Azure Key Vault foi projetado para armazenar segredos de configuração para aplicativos de servidor. Não se destina a armazenar dados que pertencem aos usuários do aplicativo. Isso é refletido em suas características de desempenho, na API e no modelo de custo.
>
> Os dados do usuário devem ser armazenados em outros lugares, como em uma instância do Banco de Dados SQL do Azure que tenha Criptografia de Dados Transparente (TDE) ou em uma conta de armazenamento que use a Criptografia do Serviço de Armazenamento Do Azure. Segredos que são usados pelo seu aplicativo para acessar esses armazenamentos de dados podem ser mantidos no Azure Key Vault.

### <a name="protect-sensitive-data"></a>Proteger dados confidenciais

Proteger dados é uma parte essencial da sua estratégia de segurança.
Classificar seus dados e identificar suas necessidades de proteção de dados ajuda você a projetar seu aplicativo com a segurança dos dados em mente. Classificar (categorizar) dados armazenados por sensibilidade e impacto nos negócios ajuda os desenvolvedores a determinar os riscos associados aos dados.

Marque todos os dados aplicáveis como sensíveis ao projetar seus formatos de dados. Certifique-se de que o aplicativo trata os dados aplicáveis como sensíveis. Essas práticas podem ajudá-lo a proteger seus dados confidenciais:

- Use criptografia.
- Evite segredos de codificação difícil, como chaves e senhas.
- Certifique-se de que os controles de acesso e auditoria estão no lugar.

#### <a name="use-encryption"></a>Use criptografia

Proteger dados deve ser uma parte essencial da sua estratégia de segurança.
Se seus dados forem armazenados em um banco de dados ou se ele se mover para frente e para trás entre locais, use criptografia de [dados em repouso](../fundamentals/encryption-atrest.md) (enquanto estiver no banco de dados) e criptografia de dados em [trânsito](../fundamentals/data-encryption-best-practices.md#protect-data-in-transit) (a caminho de e para o usuário, o banco de dados, uma API ou ponto final de serviço). Recomendamos que você sempre use protocolos SSL/TLS para trocar dados. Certifique-se de usar a versão mais recente do TLS para criptografia (atualmente, esta é a versão 1.2).

#### <a name="avoid-hard-coding"></a>Evite codificação dura

Algumas coisas nunca devem ser codificadas em seu software. Alguns exemplos são nomes de host ou endereços IP, URLs, endereços de e-mail, nomes de usuário, senhas, chaves de conta de armazenamento e outras chaves criptográficas. Considere implementar requisitos em torno do que pode ou não ser codificado em seu código, inclusive nas seções de comentários do seu código.

Quando você colocar comentários em seu código, certifique-se de que você não salve nenhuma informação sensível. Isso inclui seu endereço de e-mail, senhas, strings de conexão, informações sobre seu aplicativo que só seriam conhecidas por alguém em sua organização e qualquer outra coisa que possa dar a um invasor uma vantagem em atacar seu aplicativo ou organização .

Basicamente, suponha que tudo em seu projeto de desenvolvimento será de conhecimento público quando for implantado. Evite incluir dados confidenciais de qualquer tipo no projeto.

Mais cedo, discutimos [o Azure Key Vault](../../key-vault/key-vault-overview.md). Você pode usar o Key Vault para armazenar segredos como chaves e senhas em vez de codifica-los. Quando você usa o Key Vault em combinação com identidades gerenciadas para recursos do Azure, seu aplicativo web Azure pode acessar valores de configuração secretos de forma fácil e segura sem armazenar nenhum segredo em seu controle de origem ou configuração. Para saber mais, consulte [Gerenciar segredos em seus aplicativos de servidor com o Azure Key Vault](https://docs.microsoft.com/learn/modules/manage-secrets-with-azure-key-vault/).

### <a name="implement-fail-safe-measures"></a>Implementar medidas de segurança contra falhas

Sua aplicação deve ser capaz de lidar com [erros](https://docs.microsoft.com/dotnet/standard/exceptions/) que ocorrem durante a execução de forma consistente. O aplicativo deve pegar todos os erros e falhar seguro ou fechado.

Você também deve garantir que os erros sejam registrados com contexto de usuário suficiente para identificar atividades suspeitas ou maliciosas. Os registros devem ser retidos por tempo suficiente para permitir a análise forense atrasada. Os logs devem estar em um formato que pode ser facilmente consumido por uma solução de gerenciamento de log. Certifique-se de que os alertas de erros relacionados à segurança sejam acionados. O registro e o monitoramento insuficientes permitem que os atacantes ataquem mais sistemas e mantenham a persistência.

### <a name="take-advantage-of-error-and-exception-handling"></a>Aproveite o manuseio de erros e exceções

Implementar o erro correto e [o manuseio de exceções](https://docs.microsoft.com/dotnet/standard/exceptions/best-practices-for-exceptions) é uma parte importante da codificação defensiva. O manuseio de erros e exceções é fundamental para tornar um sistema confiável e seguro. Erros no manuseio de erros podem levar a diferentes tipos de vulnerabilidades de segurança, como vazar informações para os atacantes e ajudar os atacantes a entender mais sobre sua plataforma e design.

Verifique se:

- Você lida com exceções de forma centralizada para evitar [blocos de tentativa/captura](https://docs.microsoft.com/dotnet/standard/exceptions/how-to-use-the-try-catch-block-to-catch-exceptions) duplicados no código.

- Todos os comportamentos inesperados são tratados dentro do aplicativo.

- As mensagens exibidas aos usuários não vazam dados críticos, mas fornecem informações suficientes para explicar o problema.

- Exceções são registradas e que fornecem informações suficientes para as equipes forenses ou de resposta a incidentes investigarem.

[O Azure Logic Apps](../../logic-apps/logic-apps-overview.md) oferece uma experiência de primeira classe para [lidar com erros e exceções](../../logic-apps/logic-apps-exception-handling.md) que são causados por sistemas dependentes. Você pode usar o Logic Apps para criar fluxos de trabalho para automatizar tarefas e processos que integram aplicativos, dados, sistemas e serviços entre empresas e organizações.

### <a name="use-logging-and-alerting"></a>Use registro e alerta

[Registre](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1) seus problemas de segurança para investigações de segurança e acione alertas sobre problemas para garantir que as pessoas saibam sobre os problemas em tempo hábil. Habilite a auditoria e log em todos os componentes. Os registros de auditoria devem capturar o contexto do usuário e identificar todos os eventos importantes.

Verifique se você não registra nenhum dado sensível que um usuário envia ao seu site. Exemplos de dados confidenciais:

- Credenciais de usuário
- Números da Previdência Social ou outras informações de identificação
- Números de cartão de crédito ou outras informações financeiras
- Informações de integridade
- Chaves privadas ou outros dados que podem ser usados para descriptografar informações criptografadas
- Informações do sistema ou aplicativo que podem ser usadas para atacar com mais eficiência o aplicativo

Certifique-se de que o aplicativo monitore eventos de gerenciamento de usuários, como logins de usuário bem-sucedidos e com falha, redefinições de senha, alterações de senha, bloqueio de conta e registro de usuário. O registro desses eventos ajuda a detectar e reagir a comportamentos potencialmente suspeitos. Ele também permite coletar dados de operações, como quem está acessando o aplicativo.

## <a name="next-steps"></a>Próximas etapas
Nos artigos a seguir, recomendamos controles de segurança e atividades que possam ajudá-lo a desenvolver e implantar aplicativos seguros.

- [Desenvolver aplicativos e segurança](secure-develop.md)
- [Implantar aplicativos seguros](secure-deploy.md)
