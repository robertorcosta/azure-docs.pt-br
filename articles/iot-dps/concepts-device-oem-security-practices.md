---
title: Práticas de segurança para fabricantes - Serviço de provisionamento de dispositivos Azure IoT
description: O Overviews das práticas comuns de segurança para OEMs e fabricantes de dispositivos que preparam dispositivos para se inscrever no Azure IoT Device Provisioning Service (DPS).
author: timlt
ms.author: timlt
ms.date: 3/02/2020
ms.topic: conceptual
ms.service: iot-dps
ms.custom: iot-p0-scenario, iot-devices-deviceOEM
ms.reviewer: nberdy
ms.openlocfilehash: 3854f353e4ea0b78c0162681e0b89d37419105d8
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80529515"
---
# <a name="security-practices-for-azure-iot-device-manufacturers"></a>Práticas de segurança para fabricantes de dispositivos Azure IoT
À medida que mais fabricantes liberam dispositivos IoT, é útil identificar orientações em torno de práticas comuns. Este artigo resume as práticas de segurança recomendadas a considerar quando você fabrica dispositivos para uso com o Azure IoT Device Provisioning Service (DPS).  

> [!div class="checklist"]
> * Selecionando opções de autenticação de dispositivos
> * Instalação de certificados em dispositivos IoT
> * Integrando um Módulo de Plataforma Confiável (TPM) no processo de fabricação

## <a name="selecting-device-authentication-options"></a>Selecionando opções de autenticação de dispositivos
O objetivo final de qualquer medida de segurança de dispositivos IoT é criar uma solução de IoT segura. Mas problemas como limitações de hardware, custo e nível de experiência em segurança impactam todas as opções que você escolher. Além disso, sua abordagem para a segurança impacta a forma como seus dispositivos IoT se conectam à nuvem. Embora [existam vários elementos de segurança de IoT](https://www.microsoft.com/research/publication/seven-properties-highly-secure-devices/) a considerar, um elemento-chave que cada cliente encontra é qual tipo de autenticação usar. 

Três tipos de autenticação amplamente utilizados são certificados X.509, Módulos de Plataforma Confiáveis (TPM) e chaves simétricas. Embora existam outros tipos de autenticação, a maioria dos clientes que constroem soluções no Azure IoT usa um desses três tipos. O resto deste artigo pesquisa prós e contras do uso de cada tipo de autenticação.

### <a name="x509-certificate"></a>Certificado X.509
Os certificados X.509 são um tipo de identidade digital que você pode usar para autenticação. O padrão do certificado X.509 está documentado no [IETF RFC 5280](https://tools.ietf.org/html/rfc5280). No Azure IoT, existem duas maneiras de autenticar certificados:
- Impressão digital. Um algoritmo de impressão digital é executado em um certificado para gerar uma seqüência hexadecimal. A seqüência gerada é uma identificação única para o certificado. 
- Autenticação ca baseada em uma cadeia completa. Uma cadeia de certificados é uma lista hierárquica de todos os certificados necessários para autenticar um certificado de entidade final (EE). Para autenticar um certificado EE, é necessário autenticar cada certificado na cadeia, incluindo um CA raiz confiável. 

Prós para X.509:
- X.509 é o tipo de autenticação mais seguro suportado no Azure IoT.
- X.509 permite um alto nível de controle para fins de gerenciamento de certificados.
- Muitos fornecedores estão disponíveis para fornecer soluções de autenticação baseadas em X.509.

Contras para X.509:
- Muitos clientes podem precisar contar com fornecedores externos para seus certificados.
- O gerenciamento de certificados pode ser caro e aumenta o custo total da solução.
- O gerenciamento do ciclo de vida do certificado pode ser difícil se a logística não for bem pensada. 

### <a name="trusted-platform-module-tpm"></a>TPM (Trusted Platform Module)
TPM, também conhecido como [ISO/IEC 11889,](https://www.iso.org/standard/66510.html)é um padrão para gerar e armazenar chaves criptográficas com segurança. TPM também se refere a um dispositivo de I/O virtual ou físico que interage com módulos que implementam o padrão. Um dispositivo TPM pode existir como hardware discreto, hardware integrado, um módulo baseado em firmware ou um módulo baseado em software. 

Existem duas diferenças principais entre TPMs e chaves simétricas: 
- Os chips TPM também podem armazenar certificados X.509.
- O atestado tpm em DPS usa a tecla de endosso TPM (EK), uma forma de autenticação assimétrica. Com autenticação assimétrica, uma chave pública é usada para criptografia, e uma chave privada separada é usada para descriptografia. Em contraste, as chaves simétricas usam autenticação simétrica, onde a chave privada é usada tanto para criptografia quanto para descriptografia. 

Prós para TPM:
- Os TPMs são incluídos como hardware padrão em muitos dispositivos Windows, com suporte integrado para o sistema operacional. 
- O atestado de acesso TPM é mais fácil de proteger do que o atestado de chave simétrica baseado em assinatura de acesso compartilhado (SAS).
- Você pode facilmente expirar e renovar, ou rolar, credenciais do dispositivo. O DPS rola automaticamente as credenciais do IoT Hub sempre que um dispositivo TPM é devido para reprovisionamento.

Contras para TPM: 
- Os TPMs são complexos e podem ser difíceis de usar. 
- O desenvolvimento de aplicativos com TPMs é difícil, a menos que você tenha um TPM físico ou um emulador de qualidade.
- Você pode ter que redesenhar a placa do seu dispositivo para incluir um TPM no hardware. 
- Se você rolar o EK em um TPM, ele destrói a identidade do TPM e cria um novo. Embora o chip físico permaneça o mesmo, ele tem uma nova identidade em sua solução ioT.

### <a name="symmetric-key"></a>Chave simétrica
Com chaves simétricas, a mesma chave é usada para criptografar e descriptografar mensagens. Como resultado, a mesma chave é conhecida tanto pelo dispositivo quanto pelo serviço que o autentica. O Azure IoT suporta conexões de chave simétricas baseadas em token SAS. A autenticação de chave simétrica requer uma responsabilidade significativa do proprietário para proteger as chaves e alcançar um nível de segurança igual com a autenticação X.509. Se você usar chaves simétricas, a prática recomendada é proteger as chaves usando um módulo de segurança de hardware (HSM).

Prós para chave simétrica:
- Usar chaves simétricas é a maneira mais simples e de menor custo de começar com a autenticação.
- O uso de chaves simétricas agiliza o seu processo porque não há nada extra para gerar. 

Contras para chave simétrica: 
- As chaves simétricas fazem um grau significativo de esforço para proteger as chaves. A mesma chave é compartilhada entre dispositivo e nuvem, o que significa que a chave deve ser protegida em dois lugares. Em contraste, o desafio com certificados TPM e X.509 é provar a posse da chave pública sem revelar a chave privada.
- As chaves simétricas facilitam o acompanhamento de más práticas de segurança. Uma tendência comum com chaves simétricas é codificar as chaves não criptografadas nos dispositivos. Embora essa prática seja conveniente, deixa as chaves vulneráveis. Você pode mitigar algum risco armazenando com segurança a chave simétrica no dispositivo. No entanto, se sua prioridade for, em última análise, segurança em vez de conveniência, use certificados X.509 ou TPM para autenticação. 

### <a name="shared-symmetric-key"></a>Chave simétrica compartilhada
Há uma variação de autenticação de chave simétrica conhecida como chave simétrica compartilhada. Esta abordagem envolve o uso da mesma chave simétrica em todos os dispositivos. A recomendação é evitar o uso de chaves simétricas compartilhadas em seus dispositivos. 

Pro para chave simétrica compartilhada:
- Simples de implementar e barato de produzir em escala. 

Contras para chave simétrica compartilhada: 
- Altamente vulnerável a ataques. O benefício da fácil implementação é muito superior ao risco. 
- Qualquer pessoa pode se passar por seus dispositivos se obter a chave compartilhada.
- Se você confiar em uma chave simétrica compartilhada que fica comprometida, você provavelmente perderá o controle dos dispositivos. 

### <a name="making-the-right-choice-for-your-devices"></a>Fazendo a escolha certa para seus dispositivos
Para escolher um método de autenticação, certifique-se de considerar os benefícios e custos de cada abordagem para o seu processo de fabricação único.  Para autenticação de dispositivos, geralmente há uma relação inversa entre o quão segura é uma abordagem e quão conveniente é.  

## <a name="installing-certificates-on-iot-devices"></a>Instalação de certificados em dispositivos IoT
Se você usa certificados X.509 para autenticar seus dispositivos IoT, esta seção oferece orientações sobre como integrar certificados ao seu processo de fabricação. Você vai precisar tomar várias decisões.  Estes incluem decisões sobre variáveis de certificados comuns, quando gerar certificados e quando instalá-los. 

Se você está acostumado a usar senhas, você pode perguntar por que você não pode usar o mesmo certificado em todos os seus dispositivos, da mesma forma que você seria capaz de usar a mesma senha em todos os seus dispositivos. Primeiro, usar a mesma senha em todos os lugares é perigoso. A prática expôs as empresas a grandes ataques DDoS, incluindo o que derrubou o DNS na costa leste dos EUA há vários anos. Nunca use a mesma senha em todos os lugares, mesmo com contas pessoais. Segundo, um certificado não é uma senha, é uma identidade única. Uma senha é como um código secreto que qualquer um pode usar para abrir uma porta em um prédio seguro.  É algo que você sabe, e você poderia dar a senha para qualquer um para ganhar entrada.  Um certificado é como uma carteira de motorista com sua foto e outros detalhes, que você pode mostrar a um guarda para entrar em um prédio seguro. Está ligado a quem você é.  Desde que o guarda combine com precisão pessoas com carteiras de motorista, apenas você pode usar sua carteira (identidade) para obter entrada. 

### <a name="variables-involved-in-certificate-decisions"></a>Variáveis envolvidas nas decisões de certificado
Considere as seguintes variáveis e como cada uma impacta o processo de fabricação global. 

#### <a name="where-the-certificate-root-of-trust-comes-from"></a>De onde vem a raiz do certificado de confiança
Pode ser caro e complexo gerenciar uma infra-estrutura de chave pública (PKI).  Especialmente se sua empresa não tem experiência em gerenciar um PKI. Suas opções são:
- Use um PKI de terceiros. Você pode comprar certificados intermediários de assinatura de um fornecedor de certificados de terceiros. Ou você pode usar uma Autoridade de Certificado (CA) privada. 
- Use um PKI auto-gerenciado. Você pode manter seu próprio sistema PKI e gerar seus próprios certificados.
- Use o serviço de segurança [Azure Sphere.](https://azure.microsoft.com/services/azure-sphere/) Esta opção se aplica apenas aos dispositivos Azure Sphere. 

#### <a name="where-certificates-are-stored"></a>Onde os certificados são armazenados
Existem alguns fatores que impactam a decisão sobre onde os certificados são armazenados. Esses fatores incluem o tipo de dispositivo, margens de lucro esperadas (se você pode pagar armazenamento seguro), recursos do dispositivo e tecnologia de segurança existente no dispositivo que você pode ser capaz de usar. Considere as seguintes opções:
- Em um módulo de segurança de hardware (HSM). O uso de um HSM é altamente recomendado. Verifique se a placa de controle do seu dispositivo já tem um HSM instalado. Se você sabe que não tem um HSM, trabalhe com o fabricante de hardware para identificar um HSM que atenda às suas necessidades.
- Em um lugar seguro no disco, como um ambiente de execução confiável (TEE).
- No sistema de arquivos local ou em uma loja de certificados. Por exemplo, a loja de certificados do Windows. 

#### <a name="connectivity-at-the-factory"></a>Conectividade na fábrica
A conectividade na fábrica determina como e quando você obterá os certificados para instalar nos dispositivos. As opções de conectividade são as seguintes:
- Conectividade. Ter conectividade é ótimo, agiliza o processo de geração de certificados localmente. 
- Sem conectividade. Neste caso, você usa um certificado assinado de uma CA para gerar certificados de dispositivo localmente e off-line. 
- Sem conectividade. Neste caso, você pode obter certificados que foram gerados com antecedência. Ou você pode usar um PKI offline para gerar certificados localmente.

#### <a name="audit-requirement"></a>Exigência de auditoria
Dependendo do tipo de dispositivos que você produz, você pode ter um requisito regulatório para criar um rastro de auditoria de como as identidades dos dispositivos são instaladas em seus dispositivos. A auditoria adiciona um custo de produção significativo. Então, na maioria dos casos, só faça isso se necessário. Se você não tem certeza se uma auditoria é necessária, verifique com o departamento jurídico da sua empresa. As opções de auditoria são: 
- Não é uma indústria sensível. Nenhuma auditoria é necessária.
- Indústria sensível. Os certificados devem ser instalados em uma sala segura de acordo com os requisitos de certificação de conformidade. Se você precisa de uma sala segura para instalar certificados, provavelmente já está ciente de como os certificados são instalados em seus dispositivos. E você provavelmente já tem um sistema de auditoria no local. 

#### <a name="length-of-certificate-validity"></a>Prazo de validade do certificado
Como uma carteira de motorista, os certificados têm uma data de validade definida quando são criadas. Aqui estão as opções para o tempo de validade do certificado:
- Renovação não é necessária.  Essa abordagem usa um longo período de renovação, para que você nunca precise renovar o certificado durante a vida útil do dispositivo. Embora tal abordagem seja conveniente, também é arriscado.  Você pode reduzir o risco usando armazenamento seguro como um HSM em seus dispositivos. No entanto, a prática recomendada é evitar o uso de certificados de longa duração.
- Renovação necessária.  Você precisará renovar o certificado durante a vida útil do dispositivo. A duração da validade do certificado depende do contexto, e você precisará de uma estratégia de renovação.  A estratégia deve incluir onde você está recebendo certificados e que tipo de funcionalidade over-the-air seus dispositivos têm que usar no processo de renovação. 

### <a name="when-to-generate-certificates"></a>Quando gerar certificados
Os recursos de conectividade com a internet em sua fábrica afetarão seu processo de geração de certificados. Você tem várias opções de quando gerar certificados: 

- Certificados pré-carregados.  Alguns fornecedores de HSM oferecem um serviço premium no qual o fornecedor HSM instala certificados para o cliente. Primeiro, os clientes dão ao fornecedor HSM acesso a um certificado de assinatura. Em seguida, o fornecedor HSM instala certificados assinados por esse certificado de assinatura em cada HSM que o cliente compra. Tudo o que o cliente tem que fazer é instalar o HSM no dispositivo. Embora este serviço aumente o custo, ele ajuda a agilizar o seu processo de fabricação.  E resolve a questão de quando instalar certificados.
- Certificados gerados por dispositivos.  Se seus dispositivos geram certificados internamente, então você deve extrair o certificado público X.509 do dispositivo para inscrevê-lo em DPS. 
- Fábrica conectada.  Se sua fábrica tiver conectividade, você pode gerar certificados de dispositivo sempre que precisar deles.
- Fábrica offline com seu próprio PKI. Se sua fábrica não tiver conectividade, e você estiver usando seu próprio PKI com suporte off-line, você pode gerar os certificados quando precisar deles.
- Fábrica offline com PKI de terceiros. Se sua fábrica não tiver conectividade e você estiver usando um PKI de terceiros, você deve gerar os certificados com antecedência. E será necessário gerar os certificados a partir de um local que tenha conectividade. 

### <a name="when-to-install-certificates"></a>Quando instalar certificados
Depois de gerar certificados para seus dispositivos IoT, você pode instalá-los nos dispositivos. 

Se você usar certificados pré-carregados com um HSM, o processo será simplificado. Depois que o HSM é instalado no dispositivo, o código do dispositivo pode acessá-lo. Em seguida, você chamará as APIs do HSM para acessar o certificado armazenado no HSM. Esta abordagem é a mais conveniente para o seu processo de fabricação. 

Se você não usar um certificado pré-carregado, você deve instalar o certificado como parte do seu processo de produção. A abordagem mais simples é instalar o certificado no HSM ao mesmo tempo em que você pisca a imagem inicial do firmware. Seu processo deve adicionar uma etapa para instalar a imagem em cada dispositivo. Após esta etapa, você pode executar verificações finais de qualidade e quaisquer outras etapas, antes de empacotar e enviar o dispositivo. 

Existem ferramentas de software disponíveis que permitem executar o processo de instalação e a verificação final da qualidade em uma única etapa. Você pode modificar essas ferramentas para gerar um certificado ou retirar um certificado de uma loja de certificados pré-gerada. Em seguida, o software pode instalar o certificado onde você precisa instalá-lo. Ferramentas de software deste tipo permitem que você execute a fabricação de qualidade de produção em escala. 

Depois de ter certificados instalados em seus dispositivos, o próximo passo é aprender a cadastrar os dispositivos com [DPS](about-iot-dps.md). 

## <a name="integrating-a-tpm-into-the-manufacturing-process"></a>Integrando um TPM no processo de fabricação
Se você usar um TPM para autenticar seus dispositivos IoT, esta seção oferecerá orientação. A orientação abrange os dispositivos TPM 2.0 amplamente utilizados que possuem suporte à chave HMAC (Hash, código de autenticação de mensagens baseado em hash). A especificação TPM para chips TPM é um padrão ISO que é mantido pelo Trusted Computing Group. Para obter mais informações sobre TPM, consulte as especificações de [TPM 2.0](https://trustedcomputinggroup.org/tpm-library-specification/) e [ISO/IEC 11889](https://www.iso.org/standard/66510.html). 

### <a name="taking-ownership-of-the-tpm"></a>Tomando posse do TPM
Um passo crítico na fabricação de um dispositivo com um chip TPM é tomar posse do TPM. Esta etapa é necessária para que você possa fornecer uma chave para o proprietário do dispositivo. O primeiro passo é extrair a tecla de endosso (EK) do dispositivo. O próximo passo é realmente reivindicar a propriedade. Como você consegue isso depende de qual TPM e sistema operacional você usa. Se necessário, entre em contato com o fabricante do TPM ou com o desenvolvedor do sistema operacional do dispositivo para determinar como reivindicar a propriedade. 

Em seu processo de fabricação, você pode extrair o EK e reivindicar a propriedade em momentos diferentes, o que adiciona flexibilidade. Muitos fabricantes aproveitam essa flexibilidade adicionando um módulo de segurança de hardware (HSM) para aumentar a segurança de seus dispositivos. Esta seção fornece orientações sobre quando extrair o EK, quando reivindicar a propriedade do TPM, e considerações para integrar essas etapas em um cronograma de fabricação. 

> [!IMPORTANT]
> A seguinte orientação pressupõe que você use um TPM discreto, firmware ou integrado. Em lugares onde é aplicável, a orientação adiciona notas sobre o uso de um TPM não discreto ou software. Se você usar um software TPM, pode haver etapas adicionais que essa orientação não inclui. Os TPMs de software têm uma variedade de implementações que estão além do escopo deste artigo.  Em geral, é possível integrar um software TPM no seguinte cronograma geral de fabricação. No entanto, enquanto um software emulado TPM é adequado para prototipagem e testes, ele não pode fornecer o mesmo nível de segurança que um Discreto, firmware ou TPM integrado. Como prática geral, evite usar um software TPM na produção.

### <a name="general-manufacturing-timeline"></a>Cronograma geral de fabricação
A linha do tempo a seguir mostra como um TPM passa por um processo de produção e acaba em um dispositivo. Cada processo de fabricação é único, e esta linha do tempo mostra os padrões mais comuns. A linha do tempo oferece orientações sobre quando tomar certas ações com as chaves. 

#### <a name="step-1-tpm-is-manufactured"></a>Passo 1: TPM é fabricado
- Se você comprar TPMs de um fabricante para uso em seus dispositivos, veja se eles extrairão chaves de endosso pública (EK_pubs) para você. É útil se o fabricante fornecer a lista de EK_pubs com os dispositivos enviados. 
    > [!NOTE]
    > Você pode dar ao fabricante tpm acesso à sua lista de inscrição usando políticas de acesso compartilhado em seu serviço de provisionamento.  Esta abordagem permite que eles adicionem os TPMs à sua lista de matrículas para você.  Mas isso é cedo no processo de fabricação, e requer confiança no fabricante TPM. Faça isso por sua conta e risco. 

- Se você fabrica TPMs para vender aos fabricantes de dispositivos, considere dar aos seus clientes uma lista de EK_pubs juntamente com seus TPMs físicos.  Fornecer aos clientes EK_pubs salva uma etapa em seu processo. 
- Se você fabrica TPMs para usar com seus próprios dispositivos, identifique qual ponto do seu processo é o mais conveniente para extrair o EK_pub. Você pode extrair o EK_pub em qualquer um dos pontos restantes na linha do tempo. 

#### <a name="step-2-tpm-is-installed-into-a-device"></a>Passo 2: O TPM é instalado em um dispositivo
Neste ponto do processo de produção, você deve saber com qual instância DPS o dispositivo será usado. Como resultado, você pode adicionar dispositivos à lista de inscrição para provisionamento automatizado. Para obter mais informações sobre o provisionamento automático do dispositivo, consulte a [documentação DPS](about-iot-dps.md).
- Se você não extraiu o EK_pub, agora é uma boa hora para fazê-lo. 
- Dependendo do processo de instalação do TPM, esta etapa também é um bom momento para tomar posse do TPM. 

#### <a name="step-3-device-has-firmware-and-software-installed"></a>Passo 3: Dispositivo tem firmware e software instalados
Neste ponto do processo, instale o cliente DPS juntamente com o escopo de ID e url global para provisionamento.
- Agora é a última chance de extrair o EK_pub. Se um terceiro instalar o software em seu dispositivo, é uma boa idéia extrair o EK_pub primeiro.
- Este ponto no processo de fabricação é ideal para tomar posse do TPM.  
    > [!NOTE]
    > Se você estiver usando um software TPM, você pode instalá-lo agora.  Extrair o EK_pub ao mesmo tempo.

#### <a name="step-4-device-is-packaged-and-sent-to-the-warehouse"></a>Passo 4: O dispositivo é embalado e enviado para o armazém
Um dispositivo pode ficar em um armazém por 6-12 meses antes de ser implantado. 

#### <a name="step-5-device-is-installed-into-the-location"></a>Passo 5: O dispositivo é instalado no local
Depois que o dispositivo chega à sua localização final, ele passa por provisionamento automatizado com DPS.

Para obter mais informações, consulte [conceitos de autoprovisionamento](concepts-auto-provisioning.md) e [atestado de TPM](concepts-tpm-attestation.md). 

## <a name="resources"></a>Recursos

Além das práticas de segurança recomendadas neste artigo, o Azure IoT fornece recursos para ajudar na seleção de hardware seguro e na criação de implantações seguras de IoT: 
- Recomendações de [segurança](../iot-fundamentals/security-recommendations.md) do Azure IoT para orientar o processo de implantação. 
- O [Azure Security Center](https://azure.microsoft.com/services/security-center/) oferece um serviço para ajudar a criar implantações seguras de IoT. 
- Para obter ajuda para avaliar o ambiente de hardware, consulte o whitepaper [Avaliando sua Segurança ioT](https://download.microsoft.com/download/D/3/9/D3948E3C-D5DC-474E-B22F-81BA8ED7A446/Evaluating_Your_IOT_Security_whitepaper_EN_US.pdf). 
- Para obter ajuda para selecionar hardware seguro, consulte [O hardware seguro certo para sua implantação de IoT](https://download.microsoft.com/download/C/0/5/C05276D6-E602-4BB1-98A4-C29C88E57566/The_right_secure_hardware_for_your_IoT_deployment_EN_US.pdf). 