---
title: Perguntas frequentes - HSM Dedicado do Azure | Microsoft Docs
description: Perguntas frequentes cobrindo diferentes tópicos sobre o HSM Dedicado do Azure
services: dedicated-hsm
author: johncdawson
manager: rkarlin
tags: azure-resource-manager
ms.custom: mvc
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 5/8/2019
ms.author: mbaldwin
ms.openlocfilehash: 338977c236ebb8f1b800eb6c28747a010e708300
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72244232"
---
# <a name="frequently-asked-questions-faq"></a>Perguntas frequentes (FAQ)

Encontre respostas para perguntas comuns sobre o HSM Dedicado do Azure da Microsoft.

## <a name="the-basics"></a>Noções básicas

### <a name="q-what-is-a-hardware-security-module-hsm"></a>P: O que é um módulo de segurança de hardware (HSM)?

Um Módulo de Segurança de Hardware (HSM) é um dispositivo de computação física usado para proteger e gerenciar chaves criptográficas. Chaves armazenadas em HSMs podem ser usadas para operações criptográficas. O material da chave permanece com segurança em módulos de hardware invioláveis e invioláveis. O HSM permite apenas que aplicativos autenticados e autorizados usem as chaves. O material chave nunca sai do limite de proteção do HSM.

### <a name="q-what-is-the-azure-dedicated-hsm-offering"></a>P: O que é a oferta do HSM Dedicado do Azure?

O HSM Dedicado do Azure é um serviço baseado em nuvem que fornece HSMs hospedados em datacenters do Azure que são conectados diretamente à rede virtual de um cliente. Esses HSMs são dispositivos de rede dedicados (Rede SafeNet da Gemalto HSM 7 Modelo A790). Eles são implementados diretamente no espaço de endereço IP privado de um cliente e a Microsoft não tem acesso à funcionalidade criptográfica dos HSMs. Somente o cliente tem total controle administrativo e criptográfico sobre esses dispositivos. Os clientes são responsáveis pelo gerenciamento do dispositivo e podem obter logs de atividade completos diretamente de seus dispositivos. Os HSMs Dedicados ajudam os clientes a atender aos requisitos normativos/de conformidade, como FIPS 140-2 Nível 3, HIPAA, PCI-DSS e eIDAS e muitos outros.

### <a name="q-what-hardware-is-used-for-dedicated-hsm"></a>P: Qual hardware é usado para HSM Dedicado?

A Microsoft fez uma parceria com a Gemalto para fornecer o serviço HSM Dedicado do Azure. O dispositivo específico usado é o [SafeNet Luna Network HSM 7 Modelo A790](https://safenet.gemalto.com/data-encryption/hardware-security-modules-hsms/safenet-network-hsm/). Este dispositivo não apenas fornece firmware validado FIPS 140-2 Nível 3, mas também oferece baixa latência, alto desempenho e alta capacidade por meio de 10 partições. 

### <a name="q-what-is-an-hsm-used-for"></a>P: Para que serve o HSM?

Os HSMs são usados para armazenar chaves criptográficas que são usadas para funcionalidade criptográfica, como SSL (secure socket layer), criptografia de dados, PKI (infraestrutura de chave pública), DRM (gerenciamento de direitos digitais) e assinatura de documentos.

### <a name="q-how-does-dedicated-hsm-work"></a>P: Como o HSM dedicado funciona?

Os clientes podem provisionar HSMs em regiões específicas usando o PowerShell ou a interface de linha de comando. O cliente especifica a qual rede virtual os HSMs serão conectados e, uma vez provisionados, os HSMs estarão disponíveis na sub-rede designada nos endereços IP designados no espaço de endereço IP privado do cliente. Em seguida, os clientes podem se conectar aos HSMs usando SSH para gerenciamento e administração de dispositivos, configurar conexões de cliente HSM, inicializar HSMs, criar partições, definir e designar funções como o oficial de partição, o agente de criptografia e o usuário de criptografia. Em seguida, o cliente usará a Gemalto para fornecer ferramentas/SDK/software de cliente HSM para executar operações criptográficas a partir de seus aplicativos.

### <a name="q-what-software-is-provided-with-the-dedicated-hsm-service"></a>P: P: Qual software é fornecido com o serviço HSM Dedicado?

Gemalto fornece todo o software para o dispositivo HSM após provisionado pela Microsoft. O software está disponível no [Portal de suporte ao cliente da Gemalto](https://supportportal.gemalto.com/csm/). Os clientes que usam o serviço de HSM Dedicado precisam estar registrados no suporte da Gemalto e ter um ID de cliente que permita acesso e download do software relevante. O software cliente suportado é a versão 7.2, que é compatível com a versão 7.0.3 do firmware validado FIPS 140-2 Nível 3. 

### <a name="q-does-azure-dedicated-hsm-offer-password-based-and-ped-based-authentication"></a>P: O HSM Dedicado do Azure oferece autenticação baseada em senha e com base em PED?

Neste momento, o HSM Dedicado do Azure fornece apenas HSMs com autenticação baseada em senha.

### <a name="q-will-azure-dedicated-hsm-host-my-hsms-for-me"></a>P: O HSM Dedicado do Azure hospedará meus HSMs para mim?

A Microsoft oferece o HSM de Rede Gemalto SafeNet Luna apenas por meio do serviço HSM Dedicado e não é possível hospedar qualquer dispositivo fornecido por clientes.

### <a name="q-does-azure-dedicated-hsm-support-payment-pineft-features"></a>P: O HSM dedicado do Azure dá suporte a recursos de pagamento (PIN/TEF)?

O serviço HSM Dedicado do Azure usa dispositivos de HSM de Rede SafeNet Luna 7 (modelo A790). Esses dispositivos não dão suporte à funcionalidade específica do HSM de pagamento (como PIN ou TEF) ou certificações. Se você quiser que o serviço HSM dedicado do Azure ofereça suporte a HSMs de pagamento no futuro, passe os comentários para seu representante de conta da Microsoft.

### <a name="q-which-azure-regions-is-dedicated-hsm-available-in"></a>P: Em quais regiões do Azure o HSM dedicado está disponível?

Desde o final de março de 2019, o HSM dedicado está disponível nas 14 regiões listadas abaixo. Outras regiões são planejadas e podem ser discutidas por meio de seu representante de conta da Microsoft.

* East US
* Leste dos EUA 2
* Oeste dos EUA
* Centro-Sul dos Estados Unidos
* Sudeste da Ásia
* Ásia Oriental
* Norte da Europa
* Europa Ocidental
* Sul do Reino Unido
* Oeste do Reino Unido
* Canadá Central
* Leste do Canadá
* Leste da Austrália
* Sudeste da Austrália

## <a name="interoperability"></a>Interoperabilidade

### <a name="q-how-does-my-application-connect-to-a-dedicated-hsm"></a>P: Como meu aplicativo se conecta a um HSM Dedicado?

Você usa a Gemalto para fornecer ferramentas/SDK/software de cliente HSM para realizar operações criptográficas de seus aplicativos. O software está disponível no [Portal de suporte ao cliente da Gemalto](https://supportportal.gemalto.com/csm/). Os clientes que usam o serviço de HSM Dedicado precisam estar registrados no suporte da Gemalto e ter um ID de cliente que permita acesso e download do software relevante.

### <a name="q-can-an-application-connect-to-dedicated-hsm-from-a-different-vnet-in-or-across-regions"></a>P: Um aplicativo pode se conectar ao HSM Dedicado de uma VNET diferente em regiões ou entre regiões?

Sim, você precisará usar [emparelhamento VNET](../virtual-network/virtual-network-peering-overview.md) dentro de uma região para estabelecer conectividade entre redes virtuais. Para conectividade entre regiões, você deve usar [Gateway de VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).

### <a name="q-can-i-synchronize-dedicated-hsm-with-on-premises-hsms"></a>P: Posso sincronizar o HSM Dedicado com HSMs locais?

Sim, você pode sincronizar HSMs locais com o HSM Dedicado. [VPN ponto-a-ponto ou conectividade ponto-a-ponto](../vpn-gateway/vpn-gateway-about-vpngateways.md) pode ser usada para estabelecer conectividade com sua rede local.

### <a name="q-can-i-encrypt-data-used-by-other-azure-services-using-keys-stored-in-dedicated-hsm"></a>P: Posso criptografar dados usados por outros serviços do Azure usando chaves armazenadas no HSM Dedicado?

Nº Os HSMs Dedicados do Azure só podem ser acessados de dentro da sua rede virtual.

### <a name="q-can-i-import-keys-from-an-existing-on-premises-hsm-to-dedicated-hsm"></a>P: Posso importar chaves de um HSM local para um HSM Dedicado?

Sim, se você tiver HSMs da Gemalto SafeNet no local. Existem vários métodos. Consulte a documentação do HSM da Gemalto.

### <a name="q-what-operating-systems-are-supported-by-dedicated-hsm-client-software"></a>P: Quais sistemas operacionais são suportados pelo software cliente de HSM Dedicado?

* Windows, Linux, Solaris, AIX, HP-UX, FreeBSD
* Virtual: VMware, hyperv, Xen, KVM

### <a name="q-how-do-i-configure-my-client-application-to-create-a-high-availability-configuration-with-multiple-partitions-from-multiple-hsms"></a>P: Como configuro meu aplicativo cliente para criar uma configuração de alta disponibilidade com várias partições de vários HSMs?

Para ter alta disponibilidade, você precisa configurar sua configuração de aplicativo cliente HSM para usar partições de cada HSM. Consulte a documentação do software cliente do HSM da Gemalto.

### <a name="q-what-authentication-mechanisms-are-supported-by-dedicated-hsm"></a>P: Quais mecanismos de autenticação são suportados pelo HSM Dedicado?

O HSM Dedicado do Azure usa dispositivos SafeNet Network HSM 7 (modelo A790) e eles suportam autenticação baseada em senha.

### <a name="q-what-sdks-apis-client-software-is-available-to-use-with-dedicated-hsm"></a>P: Quais SDKs, APIs, software cliente estão disponíveis para uso com o HSM Dedicado?

PKCS#11, Java (JCA / JCE), CAPI da Microsoft e CNG, OpenSSL

### <a name="q-can-i-importmigrate-keys-from-luna-56-hsms-to-azure-dedicated-hsms"></a>P: Posso importar/migrar chaves de HSMs do Luna 5/6 HSMs para HSMs Dedicados do Azure?

Sim. Por favor, consulte o guia de migração da Gemalto. 

## <a name="using-your-hsm"></a>Usando seu HSM

### <a name="q-how-do-i-decide-whether-to-use-azure-key-vault-or-azure-dedicated-hsm"></a>P: Como decidir se devo usar o Azure Key Vault ou o HSM Dedicado do Azure?

O HSM Dedicado do Azure é a escolha apropriada para empresas migrando para aplicativos locais do Azure que usam HSMs. HSMs Dedicados apresentam uma opção para migrar um aplicativo com alterações mínimas. Se operações criptográficas forem executadas no código do aplicativo em execução em uma VM do Azure ou no Web App, elas poderão usar o HSM Dedicado. Em geral, o software encapsulado em execução nos modelos IaaS (infra-estrutura como serviço), que suportam HSMs como armazenamento de chaves, pode usar o HSM Dedicado, como gateway de aplicativo ou gerenciador de tráfego para SSL sem chave, ADCS (Active Directory Certificate Services) ou ferramentas, ferramentas/aplicativos PKI semelhantes usados para assinatura de documentos, assinatura de código ou um SQL Server (IaaS) configurado com TDE (criptografia de banco de dados transparente) com chave mestre em um HSM usando um provedor EKM (gerenciamento extensível de chaves). O Azure Key Vault é adequado para aplicativos "nascidos na nuvem" ou para cenários de criptografia em repouso nos quais os dados do cliente são processados por cenários PaaS (plataforma como um serviço) ou SaaS (Software como um serviço), como Chave do cliente do Office 365, Azure Proteção de informações, criptografia de disco do Azure, criptografia do Azure Data Lake Store com chave gerenciada pelo cliente, criptografia de armazenamento do Azure com chave gerenciada pelo cliente e SQL Azure com chave gerenciada pelo cliente.

### <a name="q-what-usage-scenarios-best-suit-azure-dedicated-hsm"></a>P: Quais cenários de uso melhor se adequam ao HSM Dedicado do Azure?

O HSM Dedicado do Azure é mais adequado para cenários de migração. Isso significa que, se você estiver migrando aplicativos locais para o Azure que já estão usando HSMs. Isso fornece uma opção de baixa fricção para migrar para o Azure com alterações mínimas no aplicativo. Se operações criptográficas forem executadas no código do aplicativo em execução na VM do Azure ou no Web App, o HSM Dedicado pode ser usado. Em geral, o software encapsulado em execução nos modelos IaaS (Infraestrutura como Serviço), que suportam HSMs como armazenamento de chaves, pode usar o Dedicate HSM, como:

* Gateway de aplicativo ou gerenciador de tráfego para SSL keyless
* ADCS (Serviços de Certificados do Active Directory)
* Ferramentas semelhantes de PKI
* Ferramentas/aplicativos usados para assinatura de documentos
* Assinatura de código
* SQL Server (IaaS) configurado com TDE (criptografia de banco de dados transparente) com chave mestra em um HSM usando um provedor EKM (gerenciamento extensível de chaves)

### <a name="q-can-dedicated-hsm-be-used-with-office-365-customer-key-azure-information-protection-azure-data-lake-store-disk-encryption-azure-storage-encryption-azure-sql-tde"></a>P: O HSM dedicado pode ser usado com a chave do cliente do Office 365, a Proteção de Informações do Azure, o Azure Data Lake Store, o Disk Encryption, a criptografia de Armazenamento do Azure e o Azure SQL TDE?

Nº O HSM Dedicado é provisionado diretamente no espaço de Endereço IP privado de um cliente, de modo que não pode ser acessado por outros serviços do Azure ou da Microsoft.

## <a name="administration-access-and-control"></a>Administração, acesso e controle

### <a name="q-does-the-customer-get-full-exclusive-control-over-the-hsms-with-dedicated-hsms"></a>P: O cliente obtém controle total exclusivo sobre os HSMs com HSMs Dedicados?

Sim. Cada dispositivo HSM é totalmente dedicado a um único cliente e ninguém mais tem controle administrativo depois de provisionado e a senha do administrador é alterada.

### <a name="q-what-level-of-access-does-microsoft-have-to-my-hsm"></a>P: Qual o nível de acesso da Microsoft ao meu HSM?

A Microsoft não possui nenhum controle administrativo ou criptográfico sobre o HSM. A Microsoft possui acesso em nível de monitor via conexão de porta serial para recuperar telemetria básica, como temperatura e integridade de componentes. Isso permite que a Microsoft forneça notificação proativa de problemas de integridade. Se necessário, o cliente pode desativar essa conta.

### <a name="q-what-is-the-tenantadmin-account-microsoft-uses-i-am-used-to-the-admin-user-being-admin-on-safenet-hsms"></a>P: O que é a conta "tenantadmin" que a Microsoft usa, estou acostumado com o usuário administrador "admin" em SafeNet HSMs?

O dispositivo HSM é fornecido com um usuário padrão administrador com sua senha padrão usual. A Microsoft não queria ter senhas padrão em uso enquanto qualquer dispositivo estiver em um pool aguardando para ser provisionado pelos clientes. Isso não atenderia aos nossos requisitos de segurança estritos. Por esse motivo, definimos uma senha forte que é descartada no momento do provisionamento. Além disso, no tempo de provisionamento, criamos um novo usuário na função de administrador chamado "tenantadmin". Esse usuário tem a senha padrão e os clientes alteram isso como a primeira ação ao fazer logon primeiro no dispositivo provisionado recentemente. Esse processo garante altos níveis de segurança e mantém nossa promessa de controle administrativo exclusivo para nossos clientes. Deve-se observar que o usuário "tenantadmin" pode ser usado para redefinir a senha de usuário do administrador se um cliente preferir usar essa conta. 

### <a name="q-can-microsoft-or-anyone-at-microsoft-access-keys-in-my-dedicated-hsm"></a>P: A Microsoft ou qualquer pessoa na Microsoft pode acessar as chaves no meu HSM Dedicado?

Nº A Microsoft não tem acesso às chaves armazenadas no HSM Dedicado alocado pelo cliente.

### <a name="q-can-i-upgrade-softwarefirmware-on-hsms-allocated-to-me"></a>P: Posso atualizar o software/firmware nos HSMs alocados para mim?

Para obter o melhor suporte, a Microsoft recomenda enfaticamente não atualizar o software / firmware no HSM. No entanto, o cliente tem controle administrativo completo, incluindo atualização de software / firmware, se forem necessários recursos específicos de diferentes versões de firmware. Antes de fazer alterações, as implicações devem ser entendidas, pois isso poderia, por exemplo, afetar o status validado pelo FIPS. 

### <a name="q-how-do-i-manage-dedicated-hsm"></a>P: Como gerencio o HSM Dedicado?

Você pode gerenciar os HSMs Dedicados acessando-os usando o SSH.

### <a name="q-how-do-i-manage-partitions-on-the-dedicated-hsm"></a>P: Como gerencio partições no HSM Dedicado?

O software cliente HSM da Gemalto é usado para gerenciar os HSMs e as partições.

### <a name="q-how-do-i-monitor-my-hsm"></a>P: Como faço para monitorar meu HSM?

P: Como monitoro meu HSM? Um cliente tem acesso total aos logs de atividade do HSM via syslog e SNMP. Um cliente precisará configurar um servidor syslog ou um servidor SNMP para receber os logs ou eventos dos HSMs.

### <a name="q-can-i-get-full-access-log-of-all-hsm-operations-from-dedicated-hsm"></a>P: Posso obter log de acesso completo de todas as operações do HSM Dedicado?

Sim. Você pode enviar logs do dispositivo HSM para um servidor syslog

## <a name="high-availability"></a>Alta disponibilidade

### <a name="q-is-it-possible-to-configure-high-availability-in-the-same-region-or-across-multiple-regions"></a>P: É possível configurar a alta disponibilidade na mesma região ou em várias regiões?

Sim. A configuração e a configuração de alta disponibilidade são realizadas no software cliente HSM fornecido pela Gemalto. Os HSMs da mesma rede virtual ou outros VNETs na mesma região ou entre regiões, ou HSMs locais conectados a uma VNET usando VPN site a site ou ponto a ponto podem ser adicionados à mesma configuração de alta disponibilidade. Deve-se observar que isso sincroniza somente o material da chave e não itens de configuração específicos, como funções.

### <a name="q-can-i-add-hsms-from-my-on-premises-network-to-a-high-availability-group-with-azure-dedicated-hsm"></a>P: Posso adicionar HSMs da minha rede local a um grupo de alta disponibilidade com o HSM Dedicado do Azure?

Sim. Eles devem atender aos requisitos de alta disponibilidade para o SafeNet Luna Network HSM 7.

### <a name="q-can-i-add-luna-56-hsms-from-on-premises-networks-to-a-high-availability-group-with-azure-dedicated-hsm"></a>P: Posso adicionar Luna 5/6 HSMs de redes locais a um grupo de alta disponibilidade com o HSM Dedicado do Azure?

Nº

### <a name="q-how-many-hsms-can-i-add-to-the-same-high-availability-configuration-from-one-single-application"></a>P: Quantos HSMs posso adicionar à mesma configuração de alta disponibilidade a partir de um único aplicativo?

16 membros de um grupo de HA têm testes de aceleração total, com ótimos resultados.

## <a name="support"></a>Suporte

### <a name="q-what-is-the-sla-for-dedicated-hsm-service"></a>P: Qual é o SLA do serviço de HSM Dedicado?

Não há garantia de tempo de atividade específica fornecida para o serviço HSM dedicado. A Microsoft garantirá o acesso em nível de rede ao dispositivo e, portanto, os SLAs de rede padrão do Azure serão aplicados.

### <a name="q-how-are-the-hsms-used-in-azure-dedicated-hsm-protected"></a>P: Como os HSMs usados no HSM Dedicado do Azure são protegidos?

Os datacenters do Azure têm extensos controles de segurança físicos e de procedimentos. Além disso, os HSMs Dedicados são hospedados em uma área de acesso restrito adicional do datacenter. Essas áreas têm controles adicionais de acesso físico e vigilância por câmeras de vídeo para maior segurança.

### <a name="q-what-happens-if-there-is-a-security-breach-or-hardware-tampering-event"></a>P: O que acontece se houver uma violação de segurança ou evento de violação de hardware?

Serviço do HSM Dedicado usa dispositivos SafeNet 7 de HSM de rede. Esses dispositivos dão suporte à detecção de violação física e lógica. Se houver algum evento de violação, os HSMs serão automaticamente zerados.

### <a name="q-how-do-i-ensure-that-keys-in-my-dedicated-hsms-are-not-lost-due-to-error-or-a-malicious-insider-attack"></a>P: Como posso garantir que as chaves em meus HSMs Dedicados não sejam perdidas devido a erros ou ataques internos maliciosos?

É altamente recomendável usar um dispositivo de backup de HSM local para executar backup periódico regular dos HSMs para recuperação de desastre. Você precisará usar uma conexão VPN ponto a ponto ou site a site para uma estação de trabalho local conectada a um dispositivo de backup do HSM.

### <a name="q-how-do-i-get-support-for-dedicated-hsm"></a>P: Como obtenho suporte para o HSM Dedicado?

O suporte é fornecido pela Microsoft e pelo Gemalto.  Se você tiver um problema com o acesso ao hardware ou à rede, gere uma solicitação de suporte com a Microsoft e, se tiver um problema com a configuração do HSM, o software e o desenvolvimento de aplicativos, gere uma solicitação de suporte com o Gemalto. Se você tiver um problema indeterminado, gere uma solicitação de suporte com a Microsoft e, em seguida, Gemalto poderá ser envolvido conforme necessário. 

### <a name="q-how-do-i-get-the-client-software-documentation-and-access-to-integration-guidance-for-the-safenet-luna-7-hsm"></a>P: Como fazer obter o software cliente, a documentação e o acesso às diretrizes de integração para o HSM SafeNet Luna 7?

Após o registro para o serviço, será fornecida uma ID de cliente Gemalto que permite o registro no portal de suporte ao cliente do Gemalto. Isso permitirá o acesso a todos os softwares e documentações, bem como a habilitação de solicitações de suporte diretamente com o Gemalto.

### <a name="q-if-there-is-a-security-vulnerability-found-and-a-patch-is-released-by-gemalto-who-is-responsible-for-upgradingpatching-osfirmware"></a>P: Se houver uma vulnerabilidade de segurança encontrada e um patch for liberado pela Gemalto, quem é responsável pela atualização/atualização do OS/Firmware?

A Microsoft não tem a capacidade de se conectar a HSMs alocados a clientes. Os clientes devem atualizar e corrigir seus HSMs.

### <a name="q-what-if-i-need-to-reboot-my-hsm"></a>P: E se eu precisar reinicializar meu HSM?

O HSM tem uma opção de reinicialização de linha de comando. no entanto, estamos enfrentando problemas de reinicialização intermitente e, por esse motivo, é recomendado para a reinicialização mais segura que você gera uma solicitação de suporte com a Microsoft para que o dispositivo tenha reinicializado fisicamente. 

## <a name="cryptography-and-standards"></a>Criptografia e Padrões

### <a name="q-is-it-safe-to-store-encryption-keys-for-my-most-important-data-in-dedicated-hsm"></a>P: É seguro armazenar chaves de criptografia para meus dados mais importantes no HSM Dedicado?

Sim, o HSM Dedicado provisiona os appliances SafeNet Network HSM 7 que usam HSMs validados por FIPS 140-2 Nível 3. 

### <a name="q-what-cryptographic-keys-and-algorithms-are-supported-by-dedicated-hsm"></a>P: Quais chaves e algoritmos criptográficos são suportados pelo HSM Dedicado?

Serviço de HSM Dedicado provisiona os appliances SafeNet Network HSM 7. Eles suportam uma ampla gama de tipos de chaves criptográficas e algoritmos, incluindo: Suporte completo a Suite B

* Assimétrica:
  * RSA
  * DSA
  * Diffie-Hellman
  * Curva elíptica
  * Criptografia (ECDSA, ECDH, Ed25519, ECIES) com curvas nomeadas, definidas pelo usuário e de Brainpool, KCDSA
* Simétrica:
  * AES-GCM
  * DES triplo
  * DES
  * ARIA, SEED
  * RC2
  * RC4
  * RC5
  * CAST
  * Hash/Resumo da Mensagem/HMAC: SHA-1, SHA-2, SM3
  * Derivação de Chave: Modo de Contador SP800-108
  * Quebra de Chave: SP800-38F
  * Geração de Números Aleatórios: DRBG aprovado pelo FIPS 140-2 (modo SP 800-90 CTR), em conformidade com o BSI DRG.4

### <a name="q-is-dedicated-hsm-fips-140-2-level-3-validated"></a>P: O HSM Dedicado é validado para FIPS 140-2 de Nível 3?

Sim. Dispositivos de HSM dedicado provêm appliances SafeNet Network HSM 7 que usam HSMs validados por FIPS 140-2 Nível 3.

### <a name="q-what-do-i-need-to-do-to-make-sure-i-operate-dedicated-hsm-in-fips-140-2-level-3-validated-mode"></a>P: O que preciso fazer para ter certeza de operar o HSM Dedicado no modo validado FIPS 140-2 Nível 3?

O serviço HSM Dedicado provisiona os dispositivos SafeNet Luna Network HSM 7. Esses dispositivos usam HSMs validados por FIPS 140-2 Nível 3. A configuração padrão implementada, o sistema operacional e o firmware também são validados pelo FIPS. Você não precisa executar nenhuma ação para conformidade com o FIPS 140-2 Nível 3.

### <a name="q-how-does-a-customer-ensure-that-when-an-hsm-is-deprovisioned-all-the-key-material-is-wiped-out"></a>P: Como um cliente garante que, quando um HSM é desprovisionado, todo o material da chave seja eliminado?

Antes de solicitar o desprovisionamento, um cliente deve ter zerado o HSM usando as ferramentas do cliente HSM da Gemalto.

## <a name="performance-and-scale"></a>Desempenho e escala

### <a name="q-how-many-cryptographic-operations-are-supported-per-second-with-dedicated-hsm"></a>P: Quantas operações criptográficas são suportadas por segundo, com o HSM Dedicado?

O HSM Dedicado provisiona os dispositivos do SafeNet Network HSM 7 (modelo A790). Aqui está um resumo do desempenho máximo para algumas operações: 

* RSA-2048: 10.000 transações por segundo
* ECC P256: 20.000 transações por segundo
* AES-GCM: 17.000 transações por segundo

### <a name="q-how-many-partitions-can-be-created-in-dedicated-hsm"></a>P: Quantas partições podem ser criadas no HSM Dedicado?

O modelo A790 SafeNet Luna HSM 7 usado inclui uma licença para 10 partições no custo do serviço. O dispositivo tem um limite de 100 partições e a adição de partições até esse limite incorrerá em custos de licenciamento extras e exigirá a instalação de um novo arquivo de licença no dispositivo.

### <a name="q-how-many-keys-can-be-supported-in-dedicated-hsm"></a>P: Quantas chaves podem ser suportadas no HSM Dedicado?

O número máximo de chaves é uma função da memória disponível. O modelo SafeNet Luna 7 A790 em uso tem 32 MB de memória. Os números a seguir também se aplicam a pares de chaves se estiver usando chaves assimétricas.

* RSA-2048 - 19,000
* ECC-P256 - 91,000

A capacidade variará dependendo dos atributos-chave específicos definidos no modelo de geração de chaves e no número de partições.
