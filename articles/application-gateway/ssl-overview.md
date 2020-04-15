---
title: Ativando tLS de ponta a ponta no Gateway de aplicativo do Azure
description: Este artigo é uma visão geral do suporte ao TLS do Application Gateway.
services: application-gateway
author: amsriva
ms.service: application-gateway
ms.topic: article
ms.date: 3/19/2019
ms.author: victorh
ms.openlocfilehash: ae80b49c3bfb40743665768622d3f4a8a6990c12
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311857"
---
# <a name="overview-of-tls-termination-and-end-to-end-tls-with-application-gateway"></a>Visão geral do término do TLS e tLS de fim a fim com gateway de aplicativo

O TLS (Transport Layer Security, segurança de camada de transporte), anteriormente conhecido como Secure Sockets Layer (SSL), é a tecnologia de segurança padrão para estabelecer um link criptografado entre um servidor web e um navegador. Este link garante que todos os dados passados entre o servidor web e os navegadores permaneçam privados e criptografados. O gateway de aplicativo suporta tanto o término do TLS no gateway quanto a criptografia TLS de ponta a ponta.

## <a name="tls-termination"></a>Rescisão tls

O Application Gateway suporta o término do TLS no gateway, após o qual o tráfego normalmente flui descriptografado para os servidores back-end. Há uma série de vantagens de fazer o término do TLS no gateway de aplicativo:

- **Desempenho melhorado** – O maior sucesso de desempenho ao fazer descriptografia TLS é o aperto de mão inicial. Para melhorar o desempenho, o servidor que faz os caches de descriptografia de SITs iDs e gerencia tickets de sessão TLS. Se isso for feito no gateway do aplicativo, todas as solicitações do mesmo cliente poderão usar os valores armazenados em cache. Se for feito nos servidores backend, cada vez que as solicitações do cliente forem para um servidor diferente, o cliente deve reautenticar. O uso de tickets TLS pode ajudar a mitigar esse problema, mas eles não são suportados por todos os clientes e podem ser difíceis de configurar e gerenciar.
- **Melhor utilização dos servidores backend** – o processamento SSL/TLS é muito intensivo em CPU, e está se tornando mais intensivo à medida que os tamanhos das chaves aumentam. A remoção deste trabalho dos servidores backend permite que eles se concentrem no que são mais eficientes, fornecendo conteúdo.
- **Roteamento inteligente** – Ao descriptografar o tráfego, o gateway do aplicativo tem acesso ao conteúdo da solicitação, como cabeçalhos, URI e assim por diante, e pode usar esses dados para direcionar solicitações.
- **Gerenciamento de certificados** – Os certificados só precisam ser adquiridos e instalados no gateway do aplicativo e não em todos os servidores back-end. Isso economiza tempo e dinheiro.

Para configurar a terminação do TLS, é necessário adicionar um certificado TLS/SSL ao ouvinte para permitir que o gateway do aplicativo obtenha uma chave simétrica de acordo com a especificação do protocolo TLS/SSL. A chave simétrica é então usada para criptografar e descriptografar o tráfego enviado para o gateway. O certificado TLS/SSL precisa estar no formato PFX (Personal Information Exchange, troca de informações pessoais). Esse formato de arquivo permite exportar a chave privada que é exigida pelo gateway de aplicativo para realizar a criptografia e descriptografia do tráfego.

> [!NOTE] 
>
> O gateway de aplicativo não fornece qualquer capacidade para criar um novo certificado ou enviar uma solicitação de certificado para uma autoridade de certificação.

Para que a conexão TLS funcione, você precisa garantir que o certificado TLS/SSL atenda às seguintes condições:

- Que a data e a hora atuais estão dentro da faixa de data "Valid from" e "Valid to" no certificado.
- o "Common Name" (CN) do certificado corresponde ao cabeçalho de host na solicitação. Por exemplo, se o cliente estiver fazendo uma solicitação para `https://www.contoso.com/`, o CN deverá ser `www.contoso.com`.

### <a name="certificates-supported-for-tls-termination"></a>Certificados suportados para rescisão de TLS

O gateway de aplicativo suporta os seguintes tipos de certificados:

- Certificado CA (Autoridade de Certificado): Um certificado CA é um certificado digital emitido por uma autoridade de certificado (CA)
- Certificado EV (Validação Estendida): Um certificado EV é um certificado que está em conformidade com as diretrizes de certificado padrão do setor. Isso tornará a barra de localização do navegador verde e publicará o nome da empresa também.
- Certificado curinga: Este certificado suporta qualquer número de subdomínios com base em *.site.com, onde seu subdomínio substituiria o *. No entanto, não suporta site.com, então caso os usuários estejam acessando seu site sem digitar o "www" líder, o certificado curinga não cobre isso.
- Certificados auto-assinados: os navegadores clientes não confiam nesses certificados e avisarão o usuário que o certificado do serviço virtual não faz parte de uma cadeia de confiança. Certificados auto-assinados são bons para testes ou ambientes onde os administradores controlam os clientes e podem ignorar com segurança os alertas de segurança do navegador. As cargas de trabalho de produção nunca devem usar certificados auto-assinados.

Para obter mais informações, consulte [configurar o término do TLS com o gateway de aplicativo](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal).

### <a name="size-of-the-certificate"></a>Tamanho do certificado
Verifique a seção [Limites do Gateway de Aplicativo](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#application-gateway-limits) para saber o tamanho máximo do certificado TLS/SSL suportado.

## <a name="end-to-end-tls-encryption"></a>Criptografia TLS de ponta a ponta

Alguns clientes podem não desejar comunicação não criptografada aos servidores back-end. Isso pode ocorrer devido a requisitos de segurança, requisitos de conformidade ou o aplicativo pode aceitar apenas uma conexão segura. Para esses aplicativos, o gateway de aplicativos suporta criptografia TLS de ponta a ponta.

O TLS de ponta a ponta permite que você transmita dados confidenciais com segurança para o backend criptografado enquanto ainda aproveita os benefícios dos recursos de balanceamento de carga da Camada 7 que o gateway de aplicativo fornece. Alguns desses recursos têm uma afinidade de sessão baseada em cookies, um roteamento baseado em URL, suporte para o roteamento com base em sites ou a capacidade de injetar cabeçalhos X-Forwarded-*.

Quando configurado com o modo de comunicação TLS de ponta a ponta, o gateway do aplicativo encerra as sessões de TLS no gateway e descriptografa o tráfego do usuário. Ele aplica as regras configuradas para selecionar uma instância de pool de back-end apropriada para rotear o tráfego. O gateway de aplicativo inicia então uma nova conexão TLS ao servidor backend e recriptografa os dados usando o certificado de chave pública do servidor backend antes de transmitir a solicitação para o backend. Qualquer resposta do servidor Web passa pelo mesmo processo de volta para o usuário final. O TLS de ponta a ponta é habilitado definindo a configuração do protocolo na [configuração HTTP do Backend para](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http-settings) HTTPS, que é então aplicada a um pool de backend.

A política TLS aplica-se tanto ao tráfego frontend quanto ao backend. No front-end, o Application Gateway atua como servidor e reforça a política. No backend, o Application Gateway atua como cliente e envia as informações de protocolo/cifra como preferência durante o aperto de mão TLS.

O gateway de aplicativo só se comunica com as instâncias de backend que listaram seu certificado com o gateway de aplicativo ou cujos certificados são assinados por autoridades de CA conhecidas, onde o certificado CN corresponde ao nome do host nas configurações http backend. Estes incluem os serviços confiáveis do Azure, como aplicativos web de serviço do Azure App e gerenciamento de API do Azure.

Se os certificados dos membros no pool de backend não forem assinados por autoridades conhecidas da CA, cada instância no pool de backend com TLS de ponta a ponta habilitada deve ser configurada com um certificado para permitir uma comunicação segura. Adicionar o certificado garante que o gateway de aplicativo se comunique somente com instâncias de back-end conhecidas. Isso protege ainda mais a comunicação de ponta a ponta.

> [!NOTE] 
>
> A configuração do certificado de autenticação não é necessária para serviços confiáveis do Azure, como aplicativos web de serviço do Azure App e gerenciamento de API do Azure.

> [!NOTE] 
>
> O certificado adicionado ao **Backend HTTP Setting** para autenticar os servidores backend pode ser o mesmo que o certificado adicionado ao **ouvinte** para término de TLS no gateway de aplicativo ou diferente para maior segurança.

![cenário de fim a fim tls][1]

Neste exemplo, as solicitações que usam o TLS1.2 são encaminhadas para servidores back-end no Pool1 usando TLS de ponta a ponta.

## <a name="end-to-end-tls-and-whitelisting-of-certificates"></a>TLS de ponta a ponta e listagem de certificados

O gateway de aplicativo se comunica somente com as instâncias de back-end conhecidas que têm o certificado na lista de exceções do gateway. Para habilitar a lista de exceções dos certificados, você precisa carregar a chave pública dos certificados do servidor de back-end no gateway de aplicativo (não no certificado-raiz). Somente as conexões com os back-ends conhecidos e na lista de exceções são permitidas. O back-ends restantes resultam em um erro de gateway. Os certificados autoassinados servem somente para teste e não são recomendados para cargas de trabalho de produção. Esses certificados devem estar na lista de exceções do gateway de aplicativo, conforme descrito nas etapas acima, antes de poder ser usados.

> [!NOTE]
> A configuração do certificado de autenticação não é necessária para serviços do Azure confiáveis, como aplicativos Serviço de Aplicativo do Azure.

## <a name="end-to-end-tls-with-the-v2-sku"></a>TLS de ponta a ponta com o V2 SKU

Os Certificados de Autenticação foram reprovados e substituídos por Certificados raiz confiáveis no SKU do Gateway de Aplicativo v2. Eles funcionam da mesma forma para Certificados de Autenticação com algumas diferenças importantes:

- Os certificados assinados por autoridades conhecidas da CA cujo CN corresponde ao nome de host nas configurações de backend HTTP não exigem nenhuma etapa adicional para que o TLS de ponta a ponta funcione. 

   Por exemplo, se os certificados de back-end forem emitidos por uma autoridade de certificação bem conhecida e tiverem um CN da contoso.com, e o campo de host da configuração http de back-end também estiver definido como contoso.com, nenhuma etapa adicional será necessária. Você pode definir o protocolo de configuração http backend para HTTPS e tanto o teste de saúde quanto o caminho de dados seriam habilitados para TLS. Se você estiver usando o Azure App Service ou outros serviços web do Azure como seu backend, então estes também são implicitamente confiáveis e não são necessárias outras etapas para o TLS de ponta a ponta.
   
> [!NOTE] 
>
> Para que um certificado TLS/SSL seja confiável, esse certificado do servidor backend deve ter sido emitido por um CA incluído na loja confiável do Gateway de aplicativo.Se o certificado não foi emitido por um CA confiável, o Gateway de aplicativo verificará se o certificado da CA emissora foi emitido por um CA confiável, e assim por diante até que um CA confiável seja encontrado (nesse ponto, uma conexão segura e confiável será estabelecida) ou nenhum CA confiável pode ser encontrado (nesse ponto, o Gateway do aplicativo marcará o backend insalubre). Portanto, recomenda-se que o certificado de servidor backend contenha os CAs raiz e intermediário.

- Se o certificado for auto-assinado ou assinado por intermediários desconhecidos, então para habilitar o TLS final no v2 SKU, um certificado raiz confiável deve ser definido. O Gateway de Aplicativo só se comunicará com back-ends cujo certificado raiz do certificado do servidor corresponda a um da lista de certificados raiz confiáveis na configuração http de back-end associada ao pool.

> [!NOTE] 
>
> O certificado auto-assinado deve fazer parte de uma cadeia de certificados. Um único certificado auto-assinado sem cadeia não é suportado no V2 SKU.

- Além da correspondência de certificado raiz, o Application Gateway também valida se a configuração Host especificada na configuração http backend corresponder à do nome comum (CN) apresentada pelo certificado TLS/SSL do servidor backend. Ao tentar estabelecer uma conexão TLS com o backend, o Application Gateway define a extensão SNI (Server Name Indication, indicação de nome do servidor) para o Host especificado na configuração http backend.
- Se **escolher o nome de host do endereço backend** for escolhido em vez do campo Host na configuração http backend, o cabeçalho SNI será sempre definido para o pool de backend FQDN e o CN no certificado TLS/SSL do servidor backend deve corresponder ao seu FQDN. Os membros do backend pool com IPs não são suportados neste cenário.
- O certificado raiz é um certificado raiz codificado em base64 a partir dos certificados do servidor de back-end.

## <a name="next-steps"></a>Próximas etapas

Depois de aprender sobre TLS de ponta a ponta, vá para [Configurar TLS de ponta a ponta usando o Application Gateway com o PowerShell](application-gateway-end-to-end-ssl-powershell.md) para criar um gateway de aplicativo usando TLS de ponta a ponta.

<!--Image references-->

[1]: ./media/ssl-overview/scenario.png
