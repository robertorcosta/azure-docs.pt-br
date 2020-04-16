---
title: Autenticação baseada em certificado X.509 em um cluster de malha de serviço
description: Saiba mais sobre autenticação baseada em certificados em clusters de malha de serviço e como detectar, mitigar e corrigir problemas relacionados a certificados.
ms.topic: conceptual
ms.date: 03/16/2020
ms.custom: sfrev
ms.openlocfilehash: 699015e322c599dea996b3a8b9dbc0a4589440ab
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429662"
---
# <a name="x509-certificate-based-authentication-in-service-fabric-clusters"></a>Autenticação baseada em certificado x.509 em clusters de malha de serviço

Este artigo complementa a introdução à [segurança do cluster Service Fabric](service-fabric-cluster-security.md)e entra nos detalhes da autenticação baseada em certificados em clusters de malha de serviço. Assumimos que o leitor está familiarizado com conceitos fundamentais de segurança, e também com os controles que a Service Fabric expõe para controlar a segurança de um cluster.  

Tópicos abordados neste título:

* Noções básicas de autenticação baseadas em certificados
* Identidades e seus respectivos papéis
* Regras de configuração de certificado
* Solução de problemas e perguntas frequentes

## <a name="certificate-based-authentication-basics"></a>Noções básicas de autenticação baseadas em certificados
Como uma breve atualização, em segurança, um certificado é um instrumento destinado a vincular informações sobre uma entidade (o sujeito) à sua posse de um par de chaves criptográficas assimétricas, e assim constitui uma construção central da criptografia de chave pública. As chaves representadas por um certificado podem ser usadas para proteger dados ou para comprovar a identidade dos titulares de chaves; quando usado em conjunto com um sistema de PKI (Public Key Infrastructure, infra-estrutura de chaves públicas), um certificado pode representar características adicionais de seu assunto, como a propriedade de um domínio da internet, ou certos privilégios concedidos a ele pelo emissor do certificado (conhecido como Autoridade de Certificação, ou CA). Uma aplicação comum de certificados está suportando o protocolo criptográfico TLS (Transport Layer Security, segurança de camada de transporte), permitindo comunicações seguras em uma rede de computadores. Especificamente, o cliente e o servidor usam certificados para garantir a privacidade e integridade de sua comunicação, e também para realizar autenticação mútua.

Em Service Fabric, a camada fundamental de um cluster (Federação) também se baseia em TLS (entre outros protocolos) para alcançar uma rede confiável e segura de nomes participantes. Conexões no cluster via Service Fabric Client APIs usam TLS também para proteger o tráfego, e também para estabelecer as identidades das partes. Especificamente, quando usado para autenticação em Malha de Serviço, um certificado pode ser usado para comprovar as seguintes alegações: a) o apresentador da credencial do certificado possui a tecla privada b) o hash SHA-1 do certificado ('thumbprint') corresponde a uma declaração incluída na definição do cluster, ou c) o distinto Nome Do Assunto Comum do certificado corresponde a uma declaração incluída na definição de cluster , e o emissor do certificado é conhecido ou confiável.

Na lista acima, 'b' é coloquialmente conhecido como 'penetramento de impressão digital'; neste caso, a declaração refere-se a um certificado específico e a força do esquema de autenticação baseia-se na premissa de que é computacionalmente inviável forjar um certificado que produz o mesmo valor de hash que outro, enquanto ainda é um objeto válido e bem formado em todos os outros aspectos. O item 'c' representa uma forma alternativa de declarar um certificado, onde a força do regime repousa sobre a combinação do sujeito do certificado e da autoridade emissora. Neste caso, a declaração refere-se a uma classe de certificados - quaisquer dois certificados com as mesmas características são considerados totalmente equivalentes. 

As seções a seguir explicarão em profundidade como o tempo de execução do Service Fabric usa e valida certificados para garantir a segurança do cluster.

## <a name="identities-and-their-respective-roles"></a>Identidades e seus respectivos papéis
Antes de mergulhar nos detalhes da autenticação ou garantir canais de comunicação, é importante listar os atores participantes e os papéis correspondentes que desempenham em um cluster:
- o tempo de execução do Service Fabric, conhecido como "sistema": o conjunto de serviços que fornecem as abstrações e funcionalidades que representam o cluster. Ao se referir à comunicação em cluster entre instâncias do sistema, usaremos o termo 'identidade de cluster'; ao se referir ao cluster como destinatário/alvo de tráfego de fora do cluster, usaremos o termo 'identidade do servidor'.
- aplicativos hospedados, chamados de "aplicativos": código fornecido pelo proprietário do cluster, que é orquestrado e executado no cluster
- clientes: entidades permitidas para se conectar e executar funcionalidades em um cluster, de acordo com a configuração do cluster. Nós distinguemos entre dois níveis de privilégios - 'usuário' e 'admin', respectivamente. Um cliente 'usuário' é restrito principalmente a operações somente leitura (mas não todas as funcionalidades somente leitura), enquanto um cliente 'administrador' tem acesso irrestrito à funcionalidade do cluster. (Para obter mais detalhes, consulte [as funções de segurança em um cluster de malha de serviço](service-fabric-cluster-security-roles.md).)
- (Somente azure) os serviços de Malha de Serviço que orquestram e expõem controles para operação e gerenciamento de clusters de malha de serviço, referidos como simplesmente "serviço". Dependendo do ambiente, o 'serviço' pode se referir ao Provedor de Recursos de Malha de Serviços do Azure, ou a outros provedores de recursos de propriedade e operados pela equipe de malha de serviços.

Em um cluster seguro, cada uma dessas funções pode ser configurada com sua própria identidade distinta, declarada como o emparelhamento de um nome de função predefinido e sua credencial correspondente. O Service Fabric suporta declarar credenciais como certificados ou diretor de serviço baseado em domínio. (As identidades baseadas no Windows/Kerberos também são suportadas, mas estão além do escopo deste artigo; consulte a [segurança baseada no Windows em clusters de malha de serviço](service-fabric-windows-cluster-windows-security.md).) Nos clusters Do Zure, as funções do cliente também podem ser declaradas como [identidades baseadas no Azure Active Directory](service-fabric-cluster-creation-setup-aad.md).

Como aludido a acima, o tempo de execução do Service Fabric define dois níveis de privilégio em um cluster: 'administrador' e 'usuário'. Um cliente administrador e um componente "sistema" operariam ambos com privilégios de administração, e por isso são indistinguíveis um do outro. Ao estabelecer uma conexão dentro/para o cluster, um chamador autenticado será concedido pelo tempo de execução do Service Fabric, uma das duas funções como base para a autorização subsequente. Examinaremos a autenticação em profundidade nas seguintes seções.

## <a name="certificate-configuration-rules"></a>Regras de configuração de certificado
### <a name="validation-rules"></a>Regras de validação
As configurações de segurança de um cluster de malha de serviço descrevem, em princípio, os seguintes aspectos:
- o tipo de autenticação; esta é uma característica de tempo de criação, imutável do cluster. Exemplos dessas configurações são 'ClusterCredentialType', 'ServerCredentialType' e os valores permitidos são 'nenhum', 'x509' ou 'windows'. Este artigo se concentra na autenticação do tipo x509.
- as regras de validação (autenticação); essas configurações são definidas pelo proprietário do cluster e descrevem quais credenciais devem ser aceitas para um determinado papel. Exemplos serão examinados em profundidade imediatamente abaixo.
- configurações utilizadas para ajustar ou alterar sutilmente o resultado da autenticação; exemplos aqui incluem sinalizadores (des)restringindo a aplicação de listas de revogação de certificados etc.

> [!NOTE]
> Exemplos de configuração de cluster fornecidos abaixo são trechos do manifesto de cluster no formato XML, como o formato mais digerido que suporta diretamente a funcionalidade Service Fabric descrita neste artigo. As mesmas configurações podem ser expressas diretamente nas representações JSON de uma definição de cluster, seja um manifesto de cluster json autônomo ou um modelo de Mangement de recursos do Azure.

Uma regra de validação de certificados compreende os seguintes elementos:
- a função correspondente: cliente, cliente de admin (função privilegiada)
- a credencial aceita para a função, declarada por impressão digital ou nome comum do assunto

#### <a name="thumbprint-based-certificate-validation-declarations"></a>Declarações de validação de certificados baseadas em impressão digital
No caso de regras de validação baseadas em impressão digital, as credenciais apresentadas por um chamador que solicita uma conexão dentro/para o cluster serão validadas da seguinte forma:
  - a credencial é um certificado válido e bem formado: sua cadeia pode ser construída, as assinaturas correspondem
  - o certificado é válido por tempo (NotBefore <= agora < NotAfter)
  - o hash SHA-1 do certificado corresponde à declaração, como uma comparação de string insensível a casos, excluindo todos os espaços brancos

Quaisquer erros de confiança encontrados durante a construção ou validação da cadeia serão suprimidos para declarações baseadas em impressão digital, exceto para certificados vencidos - embora existam disposições para esse caso também. Especificamente, falhas relacionadas a: status de revogação sendo desconhecido ou off-line, raiz não confiável, uso de chave inválida, cadeia parcial são considerados erros não fatais; a premissa, neste caso, é que o certificado é apenas um envelope para um par de chaves - a segurança reside no fato de que o proprietário do cluster estabeleceu em locais medidas para proteger a chave privada.

O trecho a seguir de um manifesto de cluster exemplifica esse conjunto de regras de validação baseadas em impressão digital:

```xml
<Section Name="Security">
  <Parameter Name="ClusterCredentialType" Value="X509" />
  <Parameter Name="ServerAuthCredentialType" Value="X509" />
  <Parameter Name="AdminClientCertThumbprints" Value="d5ec...4264" />
  <Parameter Name="ClientCertThumbprints" Value="7c8f...01b0" />
  <Parameter Name="ClusterCertThumbprints" Value="abcd...1234,ef01...5678" />
  <Parameter Name="ServerCertThumbprints" Value="ef01...5678" />
</Section>
```

Cada uma das entradas acima refere-se a uma identidade específica como descrito anteriormente; cada entrada também permite especificar múltiplos valores, como uma lista separada por comma de strings. Neste exemplo, ao validar com sucesso as credenciais recebidas, o apresentador de um certificado com a impressão digital SHA-1 'd5ec... 4264' será concedido o papel de 'admin'; por outro lado, um interlocutor autenticando com certificado '7c8f... 01b0' será concedido um papel 'usuário', restrito a operações exclusivas de leitura. Um chamador de entrada que apresenta um certificado cuja impressão digital é ou 'abcd... 1234' ou "ef01... 5678' será aceito como um nó de pares no cluster. Por fim, um cliente conectado a um ponto final de gerenciamento do cluster esperará que a impressão digital do certificado do servidor seja 'ef01... 5678'. 

Como mencionado anteriormente, a Service Fabric faz provisões para aceitar certificados vencidos; a razão é que os certificados têm uma vida útil limitada e, quando declarados por impressão digital (que se refere a uma instância específica do certificado), permitir que um certificado expire resultará em uma falha na conexão com o cluster ou em um colapso total do cluster. É muito fácil esquecer ou negligenciar a rotação de um certificado com impressões digitais, e infelizmente a recuperação de tal situação é difícil.

Para isso, o proprietário do cluster pode afirmar explicitamente que os certificados auto-assinados declarados por impressão digital serão considerados válidos, da seguinte forma:

```xml
  <Section Name="Security">
    <Parameter Name="AcceptExpiredPinnedClusterCertificate" Value="true" />
  </Section>
```
Esse comportamento não se estende aos certificados emitidos pelo CA; se esse fosse o caso, um certificado vencido revogado e conhecido por ser comprometido poderia se tornar "válido" assim que não figurasse mais na lista de revogação de certificados da CA e, assim, apresentasse um risco à segurança. Com certificados auto-assinados, o proprietário do cluster é considerado o único responsável por proteger a chave privada do certificado, o que não é o caso dos certificados emitidos pelo CA - o proprietário do cluster pode não estar ciente de como ou quando seu certificado foi declarado comprometido.

#### <a name="common-name-based-certificate-validation-declarations"></a>Declarações comuns de validação de certificados baseadas em nomes
As declarações baseadas em nomes comuns tomam uma das seguintes formas:
- nome comum do assunto (apenas)
- assunto nome comum com fixação emissor

Consideremos primeiro um trecho de um manifesto de cluster que exemplifica ambos os estilos de declaração:
```xml
    <Section Name="Security/ServerX509Names">
      <Parameter Name="server.demo.system.servicefabric.azure-int" Value="" />
    </Section>
    <Section Name="Security/ClusterX509Names">
      <Parameter Name="cluster.demo.system.servicefabric.azure-int" Value="1b45...844d,d7fe...26c8,3ac7...6960,96ea...fb5e" />
    </Section>
```
As declarações referem-se às identidades do servidor e do cluster, respectivamente; observe que as declarações baseadas em CN têm suas próprias seções no manifesto de cluster, separadas da padrão 'Segurança'. Em ambas as declarações, o 'Nome' representa o nome comum do certificado, e o campo 'Valor' representa o emissor esperado, da seguinte forma:

- no primeiro caso, a declaração afirma que o elemento nome comum do objeto distinto do certificado do servidor deverá corresponder à string "server.demo.system.servicefabric.azure-int"; o campo 'Valor' vazio denota a expectativa de que a raiz da cadeia de certificados seja confiável no nó/máquina onde o certificado do servidor está sendo validado; no Windows, isso significa que o certificado pode ser acorrentado a qualquer um dos certificados instalados na loja 'Trusted Root CA';
- no segundo caso, a declaração afirma que o apresentador de um certificado é aceito como um nó de pares no cluster se o nome comum do certificado corresponder à string "cluster.demo.system.servicefabric.azure-int", *e* a impressão digital do emissor direto do certificado corresponde a uma das entradas separadas por comma no campo 'Valor'. (Este tipo de regra é coloquialmente conhecido como 'nome comum com fixação de emissor'.)

Em ambos os casos, a cadeia do certificado é construída e espera-se que seja livre de erros; ou seja, erros de revogação, cadeia parcial ou erros de confiança inválidos por tempo são considerados fatais, e a validação do certificado falhará. Fixar os emissores resultará em considerar o status de 'raiz não confiável' como um erro não fatal; apesar das aparências, esta é uma forma mais rigorosa de validação, pois permite que o proprietário do cluster restrinja o conjunto de emissores autorizados/aceitos ao seu próprio PKI.

Depois que a cadeia de certificados é construída, ela é validada contra uma política TLS/SSL padrão com o assunto declarado como nome remoto; um certificado será considerado uma correspondência se o seu nome comum de assunto ou qualquer um de seus nomes alternativos de assunto corresponder à declaração CN do manifesto de cluster. Curingas são suportados neste caso, e a correspondência de cordas é insensível ao caso.

(Devemos esclarecer que a seqüência descrita acima pode ser executada para cada tipo de uso de chave declarado pelo certificado; se o certificado especificar o uso da chave de autenticação do cliente, a cadeia é construída e avaliada primeiro para uma função do cliente. Em caso de sucesso, a avaliação é concluída e a validação é bem sucedida. Se o certificado não tiver o uso da autenticação do cliente ou a falha na validação, o tempo de execução do Service Fabric irá criar e avaliar a cadeia para autenticação do servidor.)

Para completar o exemplo, o seguinte trecho ilustra a declaração de certificados de cliente por nome comum:
```xml
    <Section Name="Security/AdminClientX509Names">
      <Parameter Name="admin.demo.client.servicefabric.azure-int" Value="1b45...844d,d7fe...26c8,3ac7...6960,96ea...fb5e" />
    </Section>
    <Section Name="Security/ClientX509Names">
      <Parameter Name="user.demo.client.servicefabric.azure-int" Value="1b45...844d,d7fe...26c8,3ac7...6960,96ea...fb5e" />
    </Section>
```

As declarações acima correspondem às identidades do admin e do usuário, respectivamente; a validação dos certificados declarados dessa forma é exatamente como descrito para os exemplos anteriores, de certificados de cluster e servidor.

> [!NOTE]
> As declarações baseadas em nomes comuns visam simplificar a rotação e, em geral, o gerenciamento de certificados de cluster. No entanto, recomenda-se aderir às seguintes recomendações para garantir a disponibilidade e a segurança contínuas do cluster:
  * preferem emissor pinning a confiar em raízes confiáveis
  * evitar misturar emissores de diferentes PKIs
  * garantir que todos os emissores esperados estejam listados na declaração do certificado; um emissor incompatível resultará em uma validação falha
  * garantir que os pontos finais da política de certificados do PKI sejam descobertos, disponíveis e acessíveis - isso significa que os pontos finais AIA, CRL ou OCSP são declarados no certificado de folha e que eles estão acessíveis para que a construção da cadeia de certificados possa ser concluída.

Unindo tudo isso, ao receber uma solicitação de conexão em um cluster protegido com certificados X.509, o tempo de execução do Service Fabric usará as configurações de segurança do cluster para validar as credenciais da parte remota conforme descrito acima; se for bem-sucedido, o chamador/parte remota é considerado autenticado. Se a credencial corresponder a várias regras de validação, o tempo de execução concederá ao chamador o papel mais privilegiado de qualquer uma das regras combinadas. 

### <a name="presentation-rules"></a>Regras de apresentação
A seção anterior descreveu como a autenticação funciona em um cluster seguro por certificados; esta seção explicará como o próprio tempo de execução do Service Fabric descobre e carrega os certificados que usa para comunicação em cluster; chamamos essas regras de "apresentação".

Assim como as regras de validação, as regras de apresentação especificam uma função e a declaração de credencial associada, expressa por impressão digital ou nome comum. Ao contrário das regras de validação, as declarações baseadas em nomes comuns não possuem disposições para fixação de emissor; isso permite maior flexibilidade, bem como melhor desempenho. As regras de apresentação são declaradas na seção 'NodeType' do manifesto de cluster, para cada tipo de nó distinto; as configurações são divididas das seções de segurança do cluster para permitir que cada tipo de nó tenha sua configuração completa em uma única seção. Nos clusters azure Service Fabric, as declarações de certificado de tipo de nó são padrão às suas configurações correspondentes na seção Segurança da definição do cluster.

#### <a name="thumbprint-based-certificate-presentation-declarations"></a>Declarações de apresentação de certificados baseadas em impressão digital
Como descrito anteriormente, o tempo de execução do Service Fabric distingue entre seu papel como o par de outros nós no cluster e como o servidor para operações de gerenciamento de cluster. Em princípio, essas configurações podem ser configuradas distintamente, mas na prática tendem a se alinhar. Para o restante deste artigo, assumiremos que as configurações correspondem à simplicidade.

Consideremos o seguinte trecho de um manifesto de cluster:
```xml
  <NodeTypes>
    <NodeType Name="nt1vm">
      <Certificates>
        <ClusterCertificate X509FindType="FindByThumbprint" X509FindValue="cc71...1984" X509FindValueSecondary="49e2...19d6" X509StoreName="my" Name="ClusterCertificate" />
        <ServerCertificate X509FindValue="cc71...1984" Name="ServerCertificate" />
        <ClientCertificate X509FindValue="cc71...1984" Name="ClientCertificate" />
      </Certificates>
    </NodeType>
  </NodeTypes>
```
O elemento 'ClusterCertificate' demonstra o esquema completo, incluindo parâmetros opcionais ('X509FindValueSecondary') ou aqueles com padrões apropriados ('X509StoreName'); as outras declarações mostram uma forma abreviada. A declaração de certificado de cluster acima afirma que as configurações de segurança dos nós do tipo 'nt1vm' são inicializadas com o certificado 'cc71.. 1984 como primária, e a 49e2. Certificado 19d6' como secundário; Espera-se que ambos os certificados\'sejam encontrados na loja de certificados LocalMachine My (ou no caminho equivalente ao Linux, *var/lib/sfcerts*).

#### <a name="common-name-based-certificate-presentation-declarations"></a>Declarações comuns de apresentação de certificados baseadas em nomes
Os certificados de tipo de nó também podem ser declarados pelo nome comum do assunto, conforme exemplificado abaixo:

```xml
  <NodeTypes>
    <NodeType Name="nt1vm">
      <Certificates>
        <ClusterCertificate X509FindType="FindBySubjectName" X509FindValue="demo.cluster.azuredocpr.system.servicefabric.azure-int" Name="ClusterCertificate" />
      </Certificates>
    </NodeType>
  </NodeTypes>
```

Para qualquer tipo de declaração, um nó de malha de serviço lerá a configuração na inicialização, localizará e carregará os certificados especificados e os classificará em ordem decrescente de seu atributo NotAfter; os certificados expirados são ignorados, e o primeiro elemento da lista é selecionado como a credencial do cliente para qualquer conexão Service Fabric tentada por este nó. (Na verdade, a Service Fabric favorece o certificado de expiração mais distante.)

Observe que, para declarações de apresentação baseadas em nomes comuns, um certificado é considerado uma correspondência se o nome comum do assunto for igual ao campo X509FindValue (ou X509FindValueSecondary) da declaração como uma comparação de seqüência exata e sensível a maiúsculas. Isso contrasta com as regras de validação, que suportam a correspondência de curinga, bem como comparações de strings insensíveis a casos.  

### <a name="miscellaneous-certificate-configuration-settings"></a>Configurações de configuração de certificados diversos
Foi mencionado anteriormente que as configurações de segurança de um cluster Service Fabric também permitem alterar sutilmente o comportamento do código de autenticação. Embora o artigo sobre [configurações de cluster Service Fabric](service-fabric-cluster-fabric-settings.md) represente a lista de configurações abrangente e atualizada, expandiremos o significado de algumas das configurações de segurança selecionadas aqui, para concluir a exposição completa na autenticação baseada em certificados. Para cada configuração, explicaremos a intenção, o valor/comportamento padrão, como ele afeta a autenticação e quais valores são aceitáveis.

Como mencionado, a validação do certificado implica sempre construir e avaliar a cadeia do certificado. Para certificados emitidos pela CA, essa chamada de API do SISTEMA OPERACIONAL aparentemente simples normalmente implica várias chamadas de saída para vários pontos finais do PKI emissor, cache de respostas e assim por diante. Dada a prevalência de chamadas de validação de certificados em um cluster De Malha de Serviço, quaisquer problemas nos pontos finais do PKI podem resultar em uma disponibilidade reduzida do cluster ou uma decomposição total. Embora as chamadas de saída não possam ser suprimidas (veja abaixo na seção FAQ para obter mais sobre isso), as seguintes configurações podem ser usadas para mascarar erros de validação causados por chamadas CRL falhadas.

  * CrlCheckingFlag - na seção 'Segurança', string convertido em UINT. O valor desta configuração é usado pela Service Fabric para mascarar erros de status da cadeia de certificados alterando o comportamento da construção de cadeias; ele é passado para a chamada Win32 CryptoAPI [CertGetCertificateChain](https://docs.microsoft.com/windows/win32/api/wincrypt/nf-wincrypt-certgetcertificatechain) como o parâmetro 'dwFlags' e pode ser definido para qualquer combinação válida de sinalizadores aceitos pela função. Um valor de 0 força o tempo de execução do Service Fabric a ignorar quaisquer erros de status de confiança - isso não é recomendado, pois seu uso constituiria uma exposição significativa à segurança. O valor padrão é 0x4000000000 (CERT_CHAIN_REVOCATION_CHECK_CHAIN_EXCLUDE_ROOT).

  Quando usar: para testes locais, com certificados auto-assinados ou certificados de desenvolvedor que não estão totalmente formados/não possuem uma infra-estrutura de chave pública adequada para suportar os certificados. Também pode usar como mitigação em ambientes com espaço para ar durante a transição entre PKIs.

  Como usar: tomaremos um exemplo que força a verificação de revogação para acessar urls cacheados apenas. Assumindo:
  ```C++
  #define CERT_CHAIN_REVOCATION_CHECK_CACHE_ONLY         0x80000000
  ```
  então a declaração no manifesto do cluster torna-se:
  ```xml
    <Section Name="Security">
      <Parameter Name="CrlCheckingFlag" Value="0x80000000" />
    </Section>
  ```

  * IgnoreCrlOfflineError - na seção 'Segurança', boolean o booleano com um valor padrão de 'falso'. Representa um atalho para suprimir um status de erro de construção de cadeia 'revogação offline' (ou um status de erro de validação de diretiva de cadeia subsequente).

  Quando usar: testes locais ou com certificados de desenvolvedor não apoiados por um PKI adequado. Use como mitigação em ambientes com espaço para ar ou quando o PKI for conhecido por ser inacessível.

  Como usar:
  ```xml
    <Section Name="Security">
      <Parameter Name="IgnoreCrlOfflineError" Value="true" />
    </Section>
  ```

  Outras configurações notáveis (todas na seção 'Segurança'):
  * AcceptExpiredPinnedClusterCertificate - discutido na seção dedicada à validação de certificado baseado em impressão digital; permite aceitar certificados de cluster auto-assinados expirados. 
  * CertificateExpirySafetyMargin - intervalo, expresso em minutos anteriores ao carimbo de tempo do certificado NotAfter, e durante o qual o certificado é considerado em risco de expiração. A Service Fabric monitora certificados de cluster e emite periodicamente relatórios de saúde sobre sua disponibilidade restante. Dentro do intervalo de "segurança", esses relatórios de saúde são elevados ao estado de 'alerta'. O valor padrão é 30 dias.
  * CertificateHealthReportingInterval - controla a frequência de relatórios de saúde relativos à validade de tempo restante dos certificados de cluster. Os relatórios só serão emitidos uma vez por este intervalo. O valor é expresso em segundos, com um padrão de 8 horas.
  * EnforcePrevalidationOnSecurityChanges - booleano, controla o comportamento da atualização de cluster ao detectar alterações nas configurações de segurança. Se definido como 'verdadeiro', a atualização do cluster tentará garantir que pelo menos um dos certificados correspondentes a qualquer uma das regras de apresentação possa passar uma regra de validação correspondente. A pré-validação é executada antes que as novas configurações sejam aplicadas a qualquer nó, mas é executada apenas no nó que hospeda a réplica primária do serviço Cluster Manager no momento de iniciação da atualização. A partir desta escrita, a configuração tem um padrão de 'false', e será definida como 'true' para novos clusters azure Service Fabric com uma versão em tempo de execução começando com 7.1.
 
### <a name="end-to-end-scenario-examples"></a>Cenário de ponta a ponta (exemplos)
Nós olhamos para as regras de apresentação, regras de validação e ajustamento de bandeiras, mas como isso tudo funciona em conjunto? Nesta seção, trabalharemos através de dois exemplos de ponta a ponta demonstrando como as configurações de segurança podem ser aproveitadas para upgrades seguros de cluster. Note que isso não se destina a ser uma dissertação abrangente sobre o gerenciamento adequado de certificados em Malha de Serviço, procure um artigo complementar sobre esse tema.

A separação das regras de apresentação e validação coloca a questão óbvia (ou preocupação) sobre se elas podem divergir e quais seriam as consequências. É possível, de fato, que a seleção de um certificado de autenticação por um nó não passe pelas regras de validação de outro nó. Na verdade, essa discrepância é a principal causa de incidentes relacionados à autenticação. Ao mesmo tempo, a separação dessas regras permite que um cluster continue operando durante uma atualização que altera as configurações de segurança do cluster. Considere que, aumentando primeiro as regras de validação como um primeiro passo, todos os nós do cluster convergirão para as novas configurações enquanto ainda usam as credenciais atuais. 

Lembre-se que, em um cluster Service Fabric, um upgrade progride através de (até 5) 'domínios de upgrade', ou UDs. Apenas nós no UD atual estão sendo atualizados/alterados em um determinado momento, e a atualização continuará para o próximo UD somente se a disponibilidade do cluster permitir. (Consulte [atualizações](service-fabric-cluster-upgrade.md) de cluster de malha de serviço e outros artigos sobre o mesmo tópico para obter mais detalhes.) As alterações de certificado/segurança são particularmente arriscadas, uma vez que podem isolar nós do cluster ou deixar o cluster na borda da perda de quórum.

Usaremos a seguinte notação para descrever as configurações de segurança de um nó:

Nk: {P:{TP=A}, V:{TP=A}}, onde:
  - 'Nk' representa um nó no domínio de upgrade *k*
  - 'P' representa as regras atuais de apresentação do nó (assumindo que estamos nos referindo apenas a certificados de cluster); 
  - 'V' representa as regras de validação atuais do nó (apenas certificado de cluster)
  - 'TP=A' representa uma declaração baseada em impressão digital (TP), com 'A' sendo uma impressão digital do certificado
  - 'CN=B' representa uma declaração comum baseada em nomes (CN), com 'B' sendo o nome comum do certificado 

#### <a name="rotating-a-cluster-certificate-declared-by-thumbprint"></a>Rotação de um certificado de cluster declarado por impressão digital
A seqüência a seguir descreve como uma atualização de dois estágios pode ser usada para introduzir com segurança um certificado de cluster secundário, declarado por impressão digital; a primeira fase introduz a nova declaração de certificado nas regras de validação, e a segunda fase introduz-a nas regras de apresentação:
  - estado inicial: N0 = {P:{TP=A}, V:{TP=A}}, ... Nk = {P:{TP=A}, V:{TP=A}} - o cluster está em repouso, todos os nós compartilham uma configuração comum
  - ao completar o domínio de atualização 0: N0 = {P:{TP=A}, V:{TP=A, TP=B}}, ... Nk = {P:{TP=A}, V:{TP=A}} - nós no UD0 apresentarão certificado A e aceitarão certificados A ou B; todos os outros nós presentes e aceitar certificado A apenas
  - ao completar o último domínio de atualização: N0 = {P:{TP=A}, V:{TP=A, TP=B}}, ... Nk = {P:{TP=A}, V:{TP=A, TP=B}} - todos os nós apresentam certificado A, todos os nós aceitariam o certificado A ou B
      
Neste ponto, o cluster está novamente em equilíbrio, e a segunda fase das configurações de segurança de atualização/alteração pode começar:
  - ao completar o domínio de atualização 0: N0 = {P:{TP=A, TP=B}, V:{TP=A, TP=B}}, ... Nk = {P:{TP=A}, V:{TP=A, TP=B}} - os nódulos em UD0 começarão a apresentar B, o que é aceito por qualquer outro nó no cluster.
  - ao completar o último domínio de atualização: N0 = {P:{TP=A, TP=B}, V:{TP=A, TP=B}}, ... Nk = {P:{TP=A, TP=B}, V:{TP=A, TP=B}} - todos os nós mudaram para apresentar o certificado B. O certificado A agora pode ser retirado/removido da definição de cluster com um conjunto subsequente de upgrades.

#### <a name="converting-a-cluster-from-thumbprint--to-common-name-based-certificate-declarations"></a>Convertendo um cluster de impressão digital para declarações de certificado baseadas em nomes comuns
Da mesma forma, alterar o tipo de declaração do certificado (de impressão digital para nome comum) seguirá o mesmo padrão acima. Observe que as regras de validação permitem declarar os certificados de uma determinada função por impressão digital e nome comum na mesma definição de cluster. Em contrapartida, porém, as regras de apresentação permitem apenas uma forma de declaração. Aliás, a abordagem segura para converter um certificado de cluster de impressão digital para nome comum é introduzir o certificado de destino pretendido primeiro por impressão digital e, em seguida, alterar essa declaração para uma comum baseada em nomes. No exemplo a seguir, assumiremos que a impressão digital 'A' e o nome comum 'B' se referem ao mesmo certificado. 

  - estado inicial: N0 = {P:{TP=A}, V:{TP=A}}, ... Nk = {P:{TP=A}, V:{TP=A}} - o cluster está em repouso, todos os nós compartilham uma configuração comum, com A sendo a impressão digital do certificado principal
  - ao completar o domínio de atualização 0: N0 = {P:{TP=A}, V:{TP=A, CN=B}}, ... Nk = {P:{TP=A}, V:{TP=A}} - nós no UD0 apresentarão certificado A e aceitarão certificados com impressão digital A ou nome comum B; todos os outros nós presentes e aceitar certificado A apenas
  - ao completar o último domínio de atualização: N0 = {P:{TP=A}, V:{TP=A, CN=B}}, ... Nk = {P:{TP=A}, V:{TP=A, CN=B}} - todos os nós apresentam o certificado A, todos os nós aceitariam o certificado A (TP) ou B (CN)

Neste ponto, podemos prosseguir com a alteração das regras de apresentação com uma atualização subseqüente:
  - ao completar o domínio de atualização 0: N0 = {P:{CN=B}, V:{TP=A, CN=B}}, ... Nk = {P:{TP=A}, V:{TP=A, CN=B}} - os nós no UD0 apresentarão o certificado B encontrado pela CN e aceitarão certificados com impressão digital A ou nome comum B; todos os outros nós apresentam e aceitam certificado A somente, selecionado por impressão digital
  - ao completar o último domínio de atualização: N0 = {P:{CN=B}, V:{TP=A, CN=B}}, ... Nk = {P:{CN=B}, V:{TP=A, CN=B}} - todos os nós apresentam o certificado B encontrado pela CN, todos os nós aceitariam o certificado A (TP) ou B (CN)
    
A conclusão da fase 2 também marca a conversão do cluster para certificados comuns baseados em nomes; as declarações de validação baseadas em impressão digital podem ser removidas em uma atualização subseqüente de cluster.

> [!NOTE]
> Nos clusters de malha de serviço do Azure, os fluxos de trabalho apresentados acima são orquestrados pelo Provedor de Recursos de Malha de Serviços; o proprietário do cluster ainda é responsável pelo provisionamento de certificados no cluster de acordo com as regras indicadas (apresentação ou validação) e é encorajado a realizar alterações em várias etapas.

Em um artigo separado, abordaremos o tema de gerenciamento e provisionamento de certificados em um cluster Service Fabric.

## <a name="troubleshooting-and-frequently-asked-questions"></a>Solução de problemas e perguntas frequentes
Embora a depuração de problemas relacionados à autenticação em clusters de malha de serviço não seja fácil, esperamos que as seguintes dicas e dicas possam ajudar. A maneira mais fácil de iniciar investigações é examinar os registros de eventos do Service Fabric nos nós do cluster - não necessariamente aqueles que apresentam sintomas, mas também nós que estão em cima, mas são incapazes de se conectar a um de seus vizinhos. No Windows, eventos de significância são normalmente registrados nos canais 'Registros de aplicativos e serviços\Microsoft-ServiceFabric\Admin' ou 'Operacional', respectivamente. Às vezes pode ser útil ativar o [registro capi2,](https://docs.microsoft.com/archive/blogs/benjaminperkins/enable-capi2-event-logging-to-troubleshoot-pki-and-ssl-certificate-issues)para capturar mais detalhes sobre a validação do certificado, recuperação de CRL/CTL etc. (Lembre-se de desabilitá-lo após completar a repro, pode ser bastante verboso.)

Sintomas típicos que se manifestam em um cluster com problemas de autenticação são: 
  - nós estão para baixo/ciclismo 
  - tentativas de conexão são rejeitadas
  - tentativas de conexão estão cronometrando

Cada um dos sintomas pode ser causado por problemas diferentes, e a mesma causa raiz pode apresentar manifestações diferentes; como tal, vamos apenas listar uma pequena amostra de problemas típicos, com recomendações para corrigi-los. 

* Os nódulos podem trocar mensagens, mas não podem se conectar. Uma possível causa para que as tentativas de conexão sejam encerradas é o erro "certificado não correspondido" - uma das partes em uma conexão Service Fabric-to-Service Fabric está apresentando um certificado que não cumpre as regras de validação do destinatário. Pode ser acompanhado por qualquer um dos seguintes erros: 
  ```C++
  0x80071c44    -2147017660 FABRIC_E_SERVER_AUTHENTICATION_FAILED
  ```
  Diagnosticar/investigar ainda mais: em cada um dos nós que tentam a conexão, determine qual certificado está sendo apresentado; examinar o certificado e tentar emular as regras de validação (verifique se há impressão digital ou igualdade de nome comum, verifique as impressões digitais do emissor, se especificado).

  Outro código de erro comum de acompanhamento pode ser:
  ```C++
  0x800b0109    -2146762487 CERT_E_UNTRUSTEDROOT
  ```
  Neste caso, o certificado é declarado por nome comum, e qualquer um dos seguintes se aplica:
    - os emissores não são fixados, e o certificado raiz não é confiável, ou
    - os emissores estão fixados, mas a declaração não inclui a impressão digital do emissor direto deste certificado

* Um nó está em cima, mas não pode se conectar a outros nós; outros nódulos não recebem tráfego de entrada do nó falhando. Neste caso, é possível que o carregamento do certificado falhe no nó local. Procure os seguintes erros:
  - certificado não encontrado - certifique-se de que os certificados declarados nas regras de apresentação possam ser resolvidos pelo conteúdo da loja de certificados LocalMachine\My (ou conforme especificado). 
    As possíveis causas para a falha podem incluir: 
      - caracteres inválidos na declaração de impressão digital
      - o certificado não está instalado
      - o certificado está vencido
      - a declaração de nome comum inclui o prefixo 'CN='
      - a declaração especifica um curinga e não existe correspondência exata na loja cert (declaração: CN=*.mydomain.com, certificado real: CN=server.mydomain.com)

  - credenciais desconhecidas - indica uma chave privada ausente correspondente ao certificado, normalmente acompanhada de código de erro: 
    ```C++ 
    0x8009030d  -2146893043 SEC_E_UNKNOWN_CREDENTIALS
    0x8009030e  -2146893042 SEC_E_NO_CREDENTIALS
    ```
    Para remediar, verificar a existência da chave privada; verificar se os SFAdmins têm acesso 'read|execute' à chave privada.

  - tipo de provedor ruim - indica um certificado Crypto New Generation (CNG) ("Microsoft Software Key Storage Provider"); neste momento, a Service Fabric só suporta certificados CAPI1. Normalmente acompanhado de código de erro:
    ```C++
    0x80090014  -2146893804 NTE_BAD_PROV_TYPE
    ```
    Para remediar, recrie o certificado de cluster usando um provedor CAPI1 (por exemplo, "Microsoft Enhanced RSA and AES Cryptographic Provider"). Para obter mais detalhes sobre provedores de criptomoedas, consulte [Entendendo provedores criptográficos](https://docs.microsoft.com/windows/win32/seccertenroll/understanding-cryptographic-providers)

