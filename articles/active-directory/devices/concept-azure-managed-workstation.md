---
title: Entenda estações de trabalho seguras e gerenciadas pelo Azure - Azure Active Directory
description: Aprenda sobre estações de trabalho seguras e gerenciadas pelo Azure e entenda por que elas são importantes.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: frasim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 05a3a8cf14a591dd3037175e4eed5b5bd8d3096c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672652"
---
# <a name="understand-secure-azure-managed-workstations"></a>Entenda estações de trabalho seguras e gerenciadas pelo Azure

Estações de trabalho seguras e isoladas são extremamente importantes para a segurança de funções sensíveis, como administradores, desenvolvedores e operadores de serviços críticos. Se a segurança da estação de trabalho do cliente estiver comprometida, muitos controles e garantias de segurança podem falhar ou ser ineficazes.

Este documento explica o que você precisa para construir uma estação de trabalho segura, muitas vezes conhecida como uma estação de trabalho de acesso privilegiado (PAW). O artigo também contém instruções detalhadas para configurar controles iniciais de segurança. Essa orientação descreve como a tecnologia baseada em nuvem pode gerenciar o serviço. Ele conta com recursos de segurança que foram introduzidos no Windows 10RS5, Microsoft Defender Advanced Threat Protection (ATP), Azure Active Directory e Microsoft Intune.

> [!NOTE]
> Este artigo explica o conceito de uma estação de trabalho segura e sua importância. Se você já está familiarizado com o conceito e gostaria de pular para a implantação, visite [Deploy a Secure Workstation](howto-azure-managed-workstation.md).

## <a name="why-secure-workstation-access-is-important"></a>Por que o acesso seguro à estação de trabalho é importante

A rápida adoção de serviços em nuvem e a capacidade de trabalhar de qualquer lugar criaram um novo método de exploração. Ao explorar controles de segurança fracos em dispositivos onde os administradores trabalham, os invasores podem obter acesso a recursos privilegiados.

O uso indevido privilegiado e os ataques à cadeia de suprimentos estão entre os cinco principais métodos que os atacantes usam para violar organizações. Eles também são a segunda tática mais comumente detectada em incidentes relatados em 2018, de acordo com o [relatório Verizon Threat](https://enterprise.verizon.com/resources/reports/dbir/), e o Relatório de Inteligência de [Segurança](https://aka.ms/sir).

A maioria dos atacantes segue estas etapas:

1. Reconhecimento para encontrar uma maneira de entrar, muitas vezes específico para uma indústria.
1. Análise para coletar informações e identificar a melhor maneira de se infiltrar em uma estação de trabalho que é percebida como de baixo valor.
1. Persistência para procurar um meio de se mover [lateralmente](https://en.wikipedia.org/wiki/Network_Lateral_Movement).
1. Exfiltração de dados confidenciais e confidenciais.

Durante o reconhecimento, os atacantes frequentemente se infiltram em dispositivos que parecem de baixo risco ou desvalorizados. Eles usam esses dispositivos vulneráveis para localizar uma oportunidade de movimentação lateral e encontrar usuários e dispositivos administrativos. Depois de obter acesso a funções de usuário privilegiadas, os invasores identificam dados de alto valor e exfiltram esses dados com sucesso.

![Padrão típico de compromisso](./media/concept-azure-managed-workstation/typical-timeline.png)

Este documento descreve uma solução que pode ajudar a proteger seus dispositivos de computação contra tais ataques laterais. A solução isola o gerenciamento e os serviços de dispositivos de produtividade menos valiosos, quebrando a cadeia antes que o dispositivo que tenha acesso a recursos sensíveis na nuvem possa ser infiltrado. A solução usa serviços nativos do Azure que fazem parte da pilha Enterprise do Microsoft 365:

* Intune para gerenciamento de dispositivos e uma lista segura de aplicativos e URLs
* Piloto automático para configuração, implantação e atualização do dispositivo
* Azure AD para gerenciamento de usuários, acesso condicional e autenticação multifatorial
* Windows 10 (versão atual) para atestado de saúde do dispositivo e experiência do usuário
* Defender ATP para proteção, detecção e resposta gerenciada em nuvem
* Azure AD PIM para gerenciamento de autorização e acesso privilegiado just-in-time (JIT) aos recursos
* Log Analytics e Sentinel para monitoramento e alerta

## <a name="who-benefits-from-a-secure-workstation"></a>Quem se beneficia de uma estação de trabalho segura?

Todos os usuários e operadores se beneficiam ao usar uma estação de trabalho segura. Um invasor que compromete um PC ou dispositivo pode se passar por todas as contas armazenadas em cache. Quando conectados ao dispositivo, eles também podem usar credenciais e tokens. Esse risco torna importante a segurança de dispositivos que são usados para funções privilegiadas, incluindo direitos administrativos. Dispositivos com contas privilegiadas são alvos de ataques de movimento lateral e de escalonamento de privilégios. Essas contas podem ser usadas para uma variedade de ativos, tais como:

* Administrador de sistemas on-premises ou baseados em nuvem
* Estação de trabalho de desenvolvedor esporu para sistemas críticos
* Administrador de conta de mídia social com alta exposição
* Estação de trabalho altamente sensível, como um terminal de pagamento SWIFT
* Estação de trabalho que lida com segredos comerciais

Para reduzir o risco, você deve implementar controles de segurança elevados para estações de trabalho privilegiadas que fazem uso dessas contas. Para obter mais informações, consulte o guia de implantação de [recursos do Azure Active Directory,](../fundamentals/active-directory-deployment-checklist-p2.md) [o roteiro do Office 365](https://aka.ms/o365secroadmap)e [o roteiro de acesso privilegiado ).](https://aka.ms/sparoadmap)

## <a name="why-use-dedicated-workstations"></a>Por que usar estações de trabalho dedicadas?

Embora seja possível adicionar segurança a um dispositivo existente, é melhor começar com uma fundação segura. Para colocar sua organização na melhor posição para manter um alto nível de segurança, comece com um dispositivo que você sabe que é seguro e implemente um conjunto de controles de segurança conhecidos.

Um número crescente de vetores de ataque através de e-mail e navegação na Web torna cada vez mais difícil ter certeza de que um dispositivo pode ser confiável. Este guia pressupõe que uma estação de trabalho dedicada esteja isolada da produtividade padrão, navegação e e-mail. A remoção da produtividade, navegação na Web e e-mail de um dispositivo pode ter um impacto negativo na produtividade. No entanto, essa salvaguarda é normalmente aceitável para cenários em que as tarefas de trabalho não exigem explicitamente e o risco de um incidente de segurança é alto.

> [!NOTE]
> Navegação na Web aqui refere-se ao acesso geral a sites arbitrários que podem ser uma atividade de alto risco. Essa navegação é distintamente diferente de usar um navegador da Web para acessar um pequeno número de sites administrativos conhecidos para serviços como Azure, Office 365, outros provedores de nuvem e aplicativos SaaS.

As estratégias de contenção reforçam a segurança aumentando o número e o tipo de controles que impedem um invasor de obter acesso a ativos sensíveis. O modelo descrito neste artigo usa um design de privilégio hierárquico e restringe privilégios administrativos a dispositivos específicos.

## <a name="supply-chain-management"></a>Gestão da cadeia de suprimentos

Essencial para uma estação de trabalho segura é uma solução de cadeia de suprimentos onde você usa uma estação de trabalho confiável chamada "raiz da confiança". A tecnologia que deve ser considerada na seleção da raiz do hardware de confiança deve incluir as seguintes tecnologias incluídas em laptops modernos: 

* [Módulo de Plataforma Confiável (TPM) 2.0](/windows-hardware/design/device-experiences/oem-tpm)
* [Criptografia de Unidade BitLocker](/windows-hardware/design/device-experiences/oem-bitlocker)
* [Inicialização segura da UEFI](/windows-hardware/design/device-experiences/oem-secure-boot)
* [Drivers e firmware distribuídos através da atualização do Windows](/windows-hardware/drivers/dashboard/understanding-windows-update-automatic-and-optional-rules-for-driver-distribution)
* [Virtualização e HVCI Ativado](/windows-hardware/design/device-experiences/oem-vbs)
* [Drivers e aplicativos HVCI-Ready](/windows-hardware/test/hlk/testref/driver-compatibility-with-device-guard)
* [Windows Hello](/windows-hardware/design/device-experiences/windows-hello-biometric-requirements)
* [Proteção de I/O dma](/windows/security/information-protection/kernel-dma-protection-for-thunderbolt)
* [Guarda do Sistema](/windows/security/threat-protection/windows-defender-system-guard/system-guard-how-hardware-based-root-of-trust-helps-protect-windows)
* [Standby moderno](/windows-hardware/design/device-experiences/modern-standby)

Para esta solução, a raiz de confiança será implantada usando a tecnologia [Microsoft Autopilot](/windows/deployment/windows-autopilot/windows-autopilot) com hardware que atende aos requisitos técnicos modernos. Para garantir uma estação de trabalho, o Piloto Automático permite que você aproveite dispositivos Windows 10 otimizados pelo Microsoft. Estes dispositivos vêm em um estado conhecido do fabricante. Em vez de refazer a imagem de um dispositivo potencialmente inseguro, o Piloto Automático pode transformar um dispositivo Windows em um estado "pronto para negócios". Ele aplica configurações e políticas, instala aplicativos e até altera a edição do Windows 10. Por exemplo, o Piloto Automático pode alterar a instalação do Windows de um dispositivo do Windows 10 Pro para o Windows 10 Enterprise para que ele possa usar recursos avançados.

![Níveis seguros da estação de trabalho](./media/concept-azure-managed-workstation/supplychain.png)

## <a name="device-roles-and-profiles"></a>Funções e perfis de dispositivos

Essa orientação faz referência a vários perfis de segurança e funções que podem ajudá-lo a criar soluções mais seguras para usuários, desenvolvedores e pessoal de TI. Esses perfis equilibram a usabilidade e os riscos para usuários comuns que podem se beneficiar de uma estação de trabalho aprimorada ou segura. As configurações de configuração fornecidas aqui são baseadas em padrões aceitos pela indústria. Essa orientação mostra como endurecer o Windows 10 e reduzir os riscos associados ao comprometimento do dispositivo ou do usuário. Para aproveitar a moderna tecnologia de hardware e o dispositivo raiz de confiança, usaremos [o Device Health Attestation](https://techcommunity.microsoft.com/t5/Intune-Customer-Success/Support-Tip-Using-Device-Health-Attestation-Settings-as-Part-of/ba-p/282643), que é habilitado a partir do perfil **de Alta Segurança.** Esse recurso está presente para garantir que os atacantes não possam ser persistentes durante a inicialização inicial de um dispositivo. Ele faz isso usando políticas e tecnologia para ajudar a gerenciar recursos e riscos de segurança.
![Níveis seguros da estação de trabalho](./media/concept-azure-managed-workstation/seccon-levels.png)

* **Segurança Básica** – Uma estação de trabalho gerenciada e padrão fornece um bom ponto de partida para a maioria dos usos domésticos e de pequenas empresas. Esses dispositivos são registrados no Azure AD e gerenciados com o Intune. Este perfil permite que os usuários executem quaisquer aplicativos e naveguem em qualquer site. Uma solução anti-malware como [o Microsoft Defender](https://www.microsoft.com/windows/comprehensive-security) deve ser ativada.

* **Segurança aprimorada** – Esta solução de nível básico e protegida é boa para usuários domésticos, usuários de pequenas empresas e desenvolvedores em geral.

   A estação de trabalho aprimorada é uma maneira baseada em políticas para aumentar a segurança do perfil de baixa segurança. Ele fornece um meio seguro para trabalhar com dados do cliente e, ao mesmo tempo, usar ferramentas de produtividade, como e-mail e navegação na Web. Você pode usar políticas de auditoria e Intune para monitorar uma estação de trabalho aprimorada para o comportamento do usuário e o uso do perfil. Você implanta o perfil de estação de trabalho aprimorada com o script windows10 (1809) e ele tira vantagem da proteção avançada contra malware usando [o ADVANCED Threat Protection (ATP)](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection).

* **Alta Segurança** – O meio mais eficaz para reduzir a superfície de ataque de uma estação de trabalho é remover a capacidade de auto-administrar a estação de trabalho. A remoção dos direitos administrativos locais é um passo que melhora a segurança, mas pode afetar a produtividade se implementada incorretamente. O alto perfil de segurança se baseia no perfil de segurança aprimorado com uma mudança considerável: a remoção do administrador local. Este perfil é projetado para usuários de alto perfil: executivos, usuários de folha de pagamento e dados sensíveis, aprovadores de serviços e processos.

   O usuário de alta segurança exige um ambiente mais controlado e ainda pode fazer atividades como navegação por e-mail e web em uma experiência simples de usar. Os usuários esperam que recursos como cookies, favoritos e outros atalhos funcionem. No entanto, esses usuários podem não exigir a capacidade de modificar ou depurar seu dispositivo. Eles também não precisam instalar drivers. O perfil de alta segurança é implantado usando o script High Security - Windows10 (1809).

* **Specialized** – Os atacantes têm como alvo desenvolvedores e administradores de TI porque podem alterar sistemas de interesse para os invasores. A estação de trabalho especializada expande as políticas da estação de trabalho de alta segurança, gerenciando aplicativos locais e limitando sites. Ele também restringe recursos de produtividade de alto risco, como ActiveX, Java, plugins de navegador e outros controles do Windows. Você implanta este perfil com o script DeviceConfiguration_NCSC - Windows10 (1803) SecurityBaseline.

* **Protegido** – Um invasor que compromete uma conta administrativa pode causar danos significativos aos negócios por roubo de dados, alteração de dados ou interrupção de serviço. Neste estado endurecido, a estação de trabalho permite todos os controles de segurança e políticas que restringem o controle direto da gestão local de aplicativos. Uma estação de trabalho segura não tem ferramentas de produtividade, por isso o dispositivo é mais difícil de comprometer. Bloqueia o vetor mais comum para ataques de phishing: e-mail e mídias sociais. A estação de trabalho protegida pode ser implantada com o script Secure Workstation - Windows10 (1809) SecurityBaseline.

   ![Estação de trabalho protegida](./media/concept-azure-managed-workstation/secure-workstation.png)

   Uma estação de trabalho segura fornece a um administrador uma estação de trabalho endurecida que tem controle de aplicativo claro e proteção de aplicativos. A estação de trabalho usa proteção de credencial, guarda de dispositivo e proteção de exploração para proteger o host de comportamentos maliciosos. Todos os discos locais também são criptografados com o BitLocker.

* **Isolado** – Esse cenário personalizado e offline representa a extremidade extrema do espectro. Não são fornecidos scripts de instalação para este caso. Você pode precisar gerenciar uma função crítica para os negócios que requer um sistema operacional legado não suportado ou sem correção. Por exemplo, uma linha de produção de alto valor ou um sistema de suporte de vida. Como a segurança é crítica e os serviços em nuvem não estão disponíveis, você pode gerenciar e atualizar esses computadores manualmente ou com uma arquitetura isolada de floresta do Active Directory, como o Ambiente de Administração de Segurança Aprimorada (ESAE). Nestas circunstâncias, considere remover todos os acessos, exceto os cheques básicos de saúde intune e ATP.

   * [Exigência de comunicação de rede intune](/intune/network-bandwidth-use)
   * [Exigência de comunicação de rede ATP](/azure-advanced-threat-protection/configure-proxy)

## <a name="next-steps"></a>Próximas etapas

[Implante uma estação de trabalho segura gerenciada pelo Azure](howto-azure-managed-workstation.md).
