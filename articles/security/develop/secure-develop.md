---
title: Desenvolva aplicativos seguros no Microsoft Azure
description: Este artigo discute as melhores práticas a serem consideradas durante as fases de implementação e verificação do seu projeto de aplicação web.
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/12/2019
ms.topic: article
ms.service: security
ms.subservice: security-develop
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 03f5b0124f95465c4a5da5043364a2f5816dae62
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81685741"
---
# <a name="develop-secure-applications-on-azure"></a>Desenvolver aplicativos seguros no Azure
Neste artigo apresentamos atividades de segurança e controles a serem considerados quando você desenvolve aplicações para a nuvem. Perguntas e conceitos de segurança a serem considerados durante as fases de implementação e verificação do [Microsoft Security Development Lifecycle (SDL)](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) são abordados. O objetivo é ajudá-lo a definir atividades e serviços do Azure que você pode usar para desenvolver um aplicativo mais seguro.

As seguintes fases de SDL são abordadas neste artigo:

- Implementação
- Verificação

## <a name="implementation"></a>Implementação
O foco da fase de implementação é estabelecer as melhores práticas de prevenção precoce e detectar e remover problemas de segurança do código.
Presuma que sua aplicação será usada de maneiras que você não pretendia que ela fosse usada. Isso ajuda você a se proteger contra o uso indevido acidental ou intencional de sua aplicação.

### <a name="perform-code-reviews"></a>Executar revisões de código

Antes de verificar o código, realize [revisões de código](https://docs.microsoft.com/azure/devops/learn/devops-at-microsoft/code-reviews-not-primarily-finding-bugs) para aumentar a qualidade geral do código e reduzir o risco de criação de bugs. Você pode usar [o Visual Studio](https://docs.microsoft.com/azure/devops/repos/tfvc/get-code-reviewed-vs?view=vsts) para gerenciar o processo de revisão de código.

### <a name="perform-static-code-analysis"></a>Realizar análise de código estático

[A análise de código estático](https://www.owasp.org/index.php/Static_Code_Analysis) (também conhecida como *análise de código fonte)* é geralmente realizada como parte de uma revisão de código. A análise de código estático geralmente se refere à execução de ferramentas de análise de código estático para encontrar potenciais vulnerabilidades em código sem execução, usando técnicas como [verificação de manchas](https://en.wikipedia.org/wiki/Taint_checking) e [análise de fluxo de dados](https://en.wikipedia.org/wiki/Data-flow_analysis).

O Azure Marketplace oferece [ferramentas de desenvolvedor](https://azuremarketplace.microsoft.com/marketplace/apps/category/developer-tools?page=1&search=code%20review) que realizam análises estáticas de código e auxiliam em revisões de código.

### <a name="validate-and-sanitize-every-input-for-your-application"></a>Valide e higienize cada entrada para sua aplicação

Trate todas as entradas como não confiáveis para proteger seu aplicativo das vulnerabilidades mais comuns do aplicativo web. Dados não confiáveis são um veículo para ataques de injeção. A entrada para o seu aplicativo inclui parâmetros na URL, entrada do usuário, dados do banco de dados ou de uma API, e qualquer coisa que seja passada em que um usuário poderia potencialmente manipular. Um aplicativo deve [validar](https://owasp.org/www-project-proactive-controls/v3/en/c5-validate-inputs) que os dados são sintáticamente e semanticamente válidos antes que o aplicativo use os dados de qualquer maneira (incluindo exibi-los de volta para o usuário).

Valide a entrada no início do fluxo de dados para garantir que apenas dados devidamente formados entrem no fluxo de trabalho. Você não quer dados malformados persistindo em seu banco de dados ou desencadeando um mau funcionamento em um componente a jusante.

Lista negra e whitelisting são duas abordagens gerais para realizar a validação da sintaxe de entrada:

  - A lista negra tenta verificar se uma determinada entrada de usuário não contém conteúdo "conhecido por ser malicioso".

  - Whitelisting tenta verificar se uma determinada entrada de usuário corresponde a um conjunto de entradas "boas conhecidas". A whitelisting baseada em caracteres é uma forma de whitelisting onde um aplicativo verifica que a entrada do usuário contém apenas caracteres "bons conhecidos" ou que a entrada corresponde a um formato conhecido.
    Por exemplo, isso pode envolver verificar se um nome de usuário contém apenas caracteres alfanuméricos ou que ele contém exatamente dois números.

Whitelisting é a abordagem preferida para a construção de software seguro.
A lista negra é propensa a erros porque é impossível pensar em uma lista completa de entradas potencialmente ruins.

Faça esse trabalho no servidor, não no lado do cliente (ou no servidor e no lado do cliente).

### <a name="verify-your-applications-outputs"></a>Verifique as saídas do seu aplicativo

Qualquer saída que você apresentar visualmente ou dentro de um documento deve ser sempre codificada e escapada. [A fuga](https://www.owasp.org/index.php/Injection_Theory#Escaping_.28aka_Output_Encoding.29), também conhecida como *codificação de saída,* é usada para ajudar a garantir que dados não confiáveis não sejam um veículo para um ataque de injeção. A fuga, combinada com a validação de dados, fornece defesas em camadas para aumentar a segurança do sistema como um todo.

A fuga garante que tudo seja exibido como *saída.* A fuga também permite que o intérprete saiba que os dados não são destinados a serem executados, e isso impede que os ataques funcionassem. Esta é outra técnica de ataque comum chamada *scripting entre sites* (XSS).

Se você estiver usando uma estrutura web de terceiros, você pode verificar suas opções de codificação de saída em sites usando a [folha de trapaça de prevenção OWASP XSS](https://github.com/OWASP/CheatSheetSeries/blob/master/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.md).

### <a name="use-parameterized-queries-when-you-contact-the-database"></a>Use consultas parametrizadas quando entrar em contato com o banco de dados

Nunca crie uma consulta de banco de dados inline "em tempo real" em seu código e envie-a diretamente para o banco de dados. O código malicioso inserido em seu aplicativo pode potencialmente fazer com que seu banco de dados seja roubado, apagado ou modificado. Seu aplicativo também pode ser usado para executar comandos maliciosos do sistema operacional no sistema operacional que hospeda seu banco de dados.

Em vez disso, use consultas parametrizadas ou procedimentos armazenados. Quando você usa consultas parametrizadas, você pode invocar o procedimento a partir do seu código com segurança e passá-lo uma seqüência sem se preocupar que ele será tratado como parte da declaração de consulta.

### <a name="remove-standard-server-headers"></a>Remover cabeçalhos padrão do servidor

Cabeçalhos como Server, X-Powered-By e X-AspNet-Version revelam informações sobre o servidor e as tecnologias subjacentes. Recomendamos que você suprima esses cabeçalhos para evitar a impressão digital do aplicativo.
Consulte [a remoção de cabeçalhos padrão do servidor em sites do Azure](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/).

### <a name="segregate-your-production-data"></a>Segregar seus dados de produção

Seus dados de produção, ou dados "reais", não devem ser usados para desenvolvimento, teste ou qualquer outra finalidade que não seja o que o negócio pretendia. Um conjunto de dados mascarado[(anonimizado)](https://en.wikipedia.org/wiki/Data_anonymization)deve ser usado para todo o desenvolvimento e teste.

Isso significa que menos pessoas têm acesso aos seus dados reais, o que reduz sua superfície de ataque. Também significa que menos funcionários veem dados pessoais, o que elimina uma possível quebra de sigilo.

### <a name="implement-a-strong-password-policy"></a>Implementar uma política de senha forte

Para se defender contra a força bruta e a adivinhação baseada em dicionário, você deve implementar uma política de senha forte para garantir que os usuários criem uma senha complexa (por exemplo, 12 caracteres de comprimento mínimo e que exijacaracteres alfanuméricos e especiais).

Você pode usar uma estrutura de identidade para criar e impor políticas de senha. O Azure AD B2C ajuda você com o gerenciamento de senhas fornecendo [políticas incorporadas,](../../active-directory-b2c/tutorial-create-user-flows.md#create-a-password-reset-user-flow) [redefinição de senha de autoatendimento](../../active-directory-b2c/user-flow-self-service-password-reset.md)e muito mais.

Para se defender de ataques em contas padrão, verifique se todas as chaves e senhas são substituíveis e que elas são geradas ou substituídas após a instalação de recursos.

Se o aplicativo tiver que gerar senhas automaticamente, certifique-se de que as senhas geradas sejam aleatórias e que tenham alta entropia.

### <a name="validate-file-uploads"></a>Validar uploads de arquivos

Se o seu aplicativo permitir [uploads de arquivos,](https://www.owasp.org/index.php/Unrestricted_File_Upload)considere as precauções que você pode tomar para esta atividade de risco. O primeiro passo em muitos ataques é colocar algum código malicioso em um sistema que está sob ataque. O uso de um upload de arquivo ajuda o invasor a conseguir isso. A OWASP oferece soluções para validar um arquivo para garantir que o arquivo que você está carregando esteja seguro.

A proteção antimalware ajuda a identificar e remover vírus, spyware e outros softwares maliciosos. Você pode instalar o [Microsoft Antimalware](../fundamentals/antimalware.md) ou a solução de proteção de ponto final de um parceiro Microsoft[(Trend Micro,](https://www.trendmicro.com/azure/) [Broadcom,](https://www.broadcom.com/products) [McAfee,](https://www.mcafee.com/us/products.aspx) [Windows Defender](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-in-windows-10)e [Endpoint Protection).](https://docs.microsoft.com/configmgr/protect/deploy-use/endpoint-protection)

[O Microsoft Antimalware](../fundamentals/antimalware.md) inclui recursos como proteção em tempo real, varredura programada, remediação de malware, atualizações de assinaturas, atualizações do mecanismo, relatórios de amostras e coleta de eventos de exclusão. Você pode integrar o Antimalware da Microsoft e as soluções de parceiros com a [Central de Segurança do Azure](../../security-center/security-center-partner-integration.md) para facilidade de implantação e detecções internas (alertas e incidentes).

### <a name="dont-cache-sensitive-content"></a>Não escoe conteúdo sensível

Não faça cache de conteúdo sensível no navegador. Os navegadores podem armazenar informações para cache e histórico. Os arquivos armazenados em cache são armazenados em uma pasta como a pasta Arquivos temporários da Internet, no caso do Internet Explorer. Quando essas páginas são referidas novamente, o navegador exibe as páginas de seu cache. Se informações confidenciais (endereço, detalhes do cartão de crédito, número da Segurança Social, nome de usuário) forem exibidas ao usuário, as informações poderão ser armazenadas no cache do navegador e serem recuperadas examinando o cache do navegador ou simplesmente pressionando o botão **De volta** do navegador.

## <a name="verification"></a>Verificação
A fase de verificação envolve um esforço abrangente para garantir que o código atenda aos princípios de segurança e privacidade estabelecidos nas fases anteriores.

### <a name="find-and-fix-vulnerabilities-in-your-application-dependencies"></a>Encontre e corrija vulnerabilidades em suas dependências de aplicativos

Você escaneia seu aplicativo e suas bibliotecas dependentes para identificar quaisquer componentes vulneráveis conhecidos. Os produtos disponíveis para realizar esta varredura incluem [Verificação de Dependência OWASP,](https://www.owasp.org/index.php/OWASP_Dependency_Check)[Snyk](https://snyk.io/)e [Pato Negro](https://www.blackducksoftware.com/).

A varredura de vulnerabilidades alimentada pela [Tinfoil Security](https://www.tinfoilsecurity.com/) está disponível para aplicativos web do Azure App Service. [A digitalização de segurança por meio do App Service](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/) oferece aos desenvolvedores e administradores um meio rápido, integrado e econômico de descobrir e abordar vulnerabilidades antes que um ator mal-intencionado possa tirar vantagem delas.

> [!NOTE]
> Você também pode [integrar a Tinfoil Security com o Azure AD](../../active-directory/saas-apps/tinfoil-security-tutorial.md). Integrar a Segurança de Papel alumínio com o Azure AD fornece os seguintes benefícios:
>  - No Azure AD, você pode controlar quem tem acesso à Segurança de Papel alumínio.
>  - Seus usuários podem ser automaticamente logados no Tinfoil Security (single sign-on) usando suas contas Azure AD.
>  - Você pode gerenciar suas contas em um único local central, o portal Azure.

### <a name="test-your-application-in-an-operating-state"></a>Teste sua aplicação em um estado operacional

O DAST (Dynamic Application Security Testing, teste dinâmico de segurança de aplicativos) é um processo de teste de um aplicativo em estado operacional para encontrar vulnerabilidades de segurança. As ferramentas DAST analisam programas enquanto estão executando para encontrar vulnerabilidades de segurança, como corrupção de memória, configuração de servidor inseguro, scripting entre sites, problemas de privilégio do usuário, injeção de SQL e outras preocupações críticas de segurança.

O DAST é diferente do teste de segurança de aplicativos estáticos (SAST). As ferramentas SAST analisam o código-fonte ou as versões compiladas do código quando o código não está sendo executado para encontrar falhas de segurança.

Realizar DAST, preferencialmente com o auxílio de um profissional de segurança (um [testador de penetração](../fundamentals/pen-testing.md) ou assessor de vulnerabilidade). Se um profissional de segurança não estiver disponível, você pode realizar o DAST você mesmo com um scanner proxy web e algum treinamento. Conecte um scanner DAST no início para garantir que você não introduza problemas de segurança óbvios em seu código. Consulte o site [OWASP](https://www.owasp.org/index.php/Category:Vulnerability_Scanning_Tools) para obter uma lista de scanners de vulnerabilidade de aplicativos web.

### <a name="perform-fuzz-testing"></a>Realizar testes de fuzz

Em [testes de fuzz,](https://cloudblogs.microsoft.com/microsoftsecure/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/)você induz a falha do programa introduzindo deliberadamente dados mal formados ou aleatórios a um aplicativo. Induzir a falha do programa ajuda a revelar possíveis problemas de segurança antes do aplicativo ser lançado.

[A Detecção de Risco de Segurança](https://docs.microsoft.com/security-risk-detection/) é o serviço exclusivo de teste de difusão da Microsoft para encontrar bugs críticos à segurança no software.

### <a name="conduct-attack-surface-review"></a>Realizar revisão da superfície de ataque

A revisão da superfície de ataque após a conclusão do código ajuda a garantir que qualquer alteração de projeto ou implementação em um aplicativo ou sistema tenha sido considerada. Ele ajuda a garantir que quaisquer novos vetores de ataque que foram criados como resultado das mudanças, incluindo modelos de ameaças, tenham sido revisados e mitigados.

Você pode construir uma imagem da superfície de ataque escaneando o aplicativo. A Microsoft oferece uma ferramenta de análise de superfície de ataque chamada [Attack Surface Analyzer](https://www.microsoft.com/download/details.aspx?id=24487). Você pode escolher entre muitas ferramentas ou serviços de varredura de teste dinâmico comercial e de varredura de vulnerabilidades, incluindo [OWASP Zed Attack Proxy Project,](https://www.owasp.org/index.php/OWASP_Zed_Attack_Proxy_Project) [Arachni,](http://arachni-scanner.com/) [Skipfish](https://code.google.com/p/skipfish/)e [w3af](http://w3af.sourceforge.net/). Essas ferramentas de digitalização rastreiam seu aplicativo e mapeiam as partes do aplicativo que estão acessíveis pela Web. Você também pode pesquisar no Azure Marketplace por ferramentas de [desenvolvedor semelhantes.](https://azuremarketplace.microsoft.com/marketplace/apps/category/developer-tools?page=1)

### <a name="perform-security-penetration-testing"></a>Realizar testes de penetração de segurança

Garantir que seu aplicativo esteja seguro é tão importante quanto testar qualquer outra funcionalidade. Faça [do teste de penetração](../fundamentals/pen-testing.md) uma parte padrão do processo de construção e implantação. Agende testes regulares de segurança e a varredura de vulnerabilidades em aplicativos implantados e monitore portas abertas, pontos finais e ataques.

### <a name="run-security-verification-tests"></a>Executar testes de verificação de segurança

[Secure DevOps Kit for Azure](https://azsk.azurewebsites.net/index.html) (AzSK) contém SVTs para vários serviços da plataforma Azure. Você executa esses SVTs periodicamente para garantir que sua assinatura do Azure e os diferentes recursos que compõem sua aplicação estejam em um estado seguro. Você também pode automatizar esses testes usando o recurso de extensões de integração contínua/implantação contínua (CI/CD) do AzSK, que disponibiliza SVTs como uma extensão do Visual Studio.

## <a name="next-steps"></a>Próximas etapas
Nos artigos a seguir, recomendamos controles de segurança e atividades que possam ajudá-lo a projetar e implantar aplicativos seguros.

- [Projetar aplicativos seguros](secure-design.md)
- [Implantar aplicativos seguros](secure-deploy.md)
