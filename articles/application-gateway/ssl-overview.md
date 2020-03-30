---
title: Habilitando o SSL de ponta a ponta no Gateway de Aplicativo Azure AD
description: Esta artigo oferece uma visão geral do suporte a SSL de ponta a ponta do Gateway de Aplicativo.
services: application-gateway
author: amsriva
ms.service: application-gateway
ms.topic: article
ms.date: 3/19/2019
ms.author: victorh
ms.openlocfilehash: 9c4e6124acdbb35233f8e829f43d2665fd4a5176
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80284799"
---
# <a name="overview-of-ssl-termination-and-end-to-end-ssl-with-application-gateway"></a>Visão geral do término do SSL e do SSL de ponta a ponta com o Gateway de aplicativo

Secure Sockets Layer (SSL) é a tecnologia de segurança padrão para estabelecer um link criptografado entre um servidor web e um navegador. Este link garante que todos os dados passados entre o servidor web e os navegadores permaneçam privados e criptografados. O gateway de aplicativo suporta tanto o término do SSL no gateway quanto a criptografia SSL de ponta a ponta.

## <a name="ssl-termination"></a>Terminação SSL

O Gateway de Aplicativo dá suporte a terminação SSL no gateway, pelo qual o tráfego flui geralmente descriptografado até os servidores de back-end. Há uma série de vantagens de fazer a rescisão do SSL no gateway de aplicativo:

- **Desempenho melhorado** – O maior sucesso de desempenho ao fazer descriptografia SSL é o aperto de mão inicial. Para melhorar o desempenho, o servidor que faz a descriptografia armazena IDs de sessão SSL e gerencia tickets de sessão TLS. Se isso for feito no gateway do aplicativo, todas as solicitações do mesmo cliente poderão usar os valores armazenados em cache. Se for feito nos servidores backend, cada vez que as solicitações do cliente forem para um servidor diferente, o cliente deve reautenticar. O uso de tickets TLS pode ajudar a mitigar esse problema, mas eles não são suportados por todos os clientes e podem ser difíceis de configurar e gerenciar.
- **Melhor utilização dos servidores backend** – o processamento SSL/TLS é muito intensivo em CPU, e está se tornando mais intensivo à medida que os tamanhos das chaves aumentam. A remoção deste trabalho dos servidores backend permite que eles se concentrem no que são mais eficientes, fornecendo conteúdo.
- **Roteamento inteligente** – Ao descriptografar o tráfego, o gateway do aplicativo tem acesso ao conteúdo da solicitação, como cabeçalhos, URI e assim por diante, e pode usar esses dados para direcionar solicitações.
- **Gerenciamento de certificados** – Os certificados só precisam ser adquiridos e instalados no gateway do aplicativo e não em todos os servidores back-end. Isso economiza tempo e dinheiro.

Para configurar a terminação SSL, é necessário adicionar um certificado SSL ao ouvinte para permitir que o gateway de aplicativo obtenha uma chave simétrica de acordo com a especificação do protocolo SSL. A chave simétrica é então usada para criptografar e descriptografar o tráfego enviado para o gateway. O certificado SSL precisa estar no formato PFX (Personal Information Exchange, troca de informações pessoais). Esse formato de arquivo permite exportar a chave privada que é exigida pelo gateway de aplicativo para realizar a criptografia e descriptografia do tráfego.

> [!NOTE] 
>
> O gateway de aplicativo não fornece qualquer capacidade para criar um novo certificado ou enviar uma solicitação de certificado para uma autoridade de certificação.

Para que a conexão SSL funcione, você precisa garantir que o certificado SSL atenda às seguintes condições:

- Que a data e a hora atuais estão dentro da faixa de data "Valid from" e "Valid to" no certificado.
- o "Common Name" (CN) do certificado corresponde ao cabeçalho de host na solicitação. Por exemplo, se o cliente estiver fazendo uma solicitação para `https://www.contoso.com/`, o CN deverá ser `www.contoso.com`.

### <a name="certificates-supported-for-ssl-termination"></a>Certificados suportados para rescisão de SSL

O gateway de aplicativo suporta os seguintes tipos de certificados:

- Certificado CA (Autoridade de Certificado): Um certificado CA é um certificado digital emitido por uma autoridade de certificado (CA)
- Certificado EV (Validação Estendida): Um certificado EV é um certificado que está em conformidade com as diretrizes de certificado padrão do setor. Isso tornará a barra de localização do navegador verde e publicará o nome da empresa também.
- Certificado curinga: Este certificado suporta qualquer número de subdomínios com base em *.site.com, onde seu subdomínio substituiria o *. No entanto, não suporta site.com, então caso os usuários estejam acessando seu site sem digitar o "www" líder, o certificado curinga não cobre isso.
- Certificados auto-assinados: os navegadores clientes não confiam nesses certificados e avisarão o usuário que o certificado do serviço virtual não faz parte de uma cadeia de confiança. Certificados auto-assinados são bons para testes ou ambientes onde os administradores controlam os clientes e podem ignorar com segurança os alertas de segurança do navegador. As cargas de trabalho de produção nunca devem usar certificados auto-assinados.

Para obter mais informações, consulte [configurar o término do SSL com o gateway de aplicativo](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal).

### <a name="size-of-the-certificate"></a>Tamanho do certificado
Verifique a seção [limites do Gateway de aplicativo](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#application-gateway-limits) para saber o tamanho máximo do certificado SSL suportado.

## <a name="end-to-end-ssl-encryption"></a>Criptografia SSL de ponta a ponta

Alguns clientes podem não desejar comunicação não criptografada aos servidores back-end. Isso pode ocorrer devido a requisitos de segurança, requisitos de conformidade ou o aplicativo pode aceitar apenas uma conexão segura. Para tais aplicativos, o gateway de aplicativo dá suporte à criptografia SSL de ponta a ponta.

O SSL de ponta a ponta permite transmitir com segurança dados confidenciais para o back-end criptografado aproveitando as vantagens dos recursos de balanceamento de carga da Camada 7 que o gateway de aplicativo fornece. Alguns desses recursos têm uma afinidade de sessão baseada em cookies, um roteamento baseado em URL, suporte para o roteamento com base em sites ou a capacidade de injetar cabeçalhos X-Forwarded-*.

Quando configurado com o modo de comunicação SSL de ponta a ponta, o gateway de aplicativo encerra as sessões SSL no gateway e descriptografa o tráfego do usuário. Ele aplica as regras configuradas para selecionar uma instância de pool de back-end apropriada para rotear o tráfego. Depois, o gateway de aplicativo inicia uma nova conexão SSL com o servidor de back-end e criptografa novamente os dados usando o certificado de chave pública do servidor de back-end antes de transmitir a solicitação ao back-end. Qualquer resposta do servidor Web passa pelo mesmo processo de volta para o usuário final. O SSL de ponta a ponta é habilitado definindo a configuração do protocolo na [configuração HTTP do Backend para](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http-settings) HTTPS, que é então aplicada a um pool de backend.

A política SSL aplica-se tanto ao tráfego frontend quanto ao backend. No front-end, o Application Gateway atua como servidor e reforça a política. No backend, o Application Gateway atua como cliente e envia as informações de protocolo/cifra como preferência durante o aperto de mão SSL.

O gateway de aplicativo só se comunica com as instâncias de backend que listaram seu certificado com o gateway de aplicativo ou cujos certificados são assinados por autoridades de CA conhecidas onde o certificado CN corresponde ao nome de host no HTTP configurações de backend. Estes incluem os serviços confiáveis do Azure, como aplicativos web de serviço do Azure App e gerenciamento de API do Azure.

Se os certificados dos membros no pool de backend não forem assinados por autoridades conhecidas da CA, cada instância no pool de backend com SSL de ponta a ponta habilitada deve ser configurada com um certificado para permitir uma comunicação segura. Adicionar o certificado garante que o gateway de aplicativo se comunique somente com instâncias de back-end conhecidas. Isso protege ainda mais a comunicação de ponta a ponta.

> [!NOTE] 
>
> A configuração do certificado de autenticação não é necessária para serviços confiáveis do Azure, como aplicativos web de serviço do Azure App e gerenciamento de API do Azure.

> [!NOTE] 
>
> O certificado adicionado ao **Backend HTTP Setting** para autenticar os servidores backend pode ser o mesmo que o certificado adicionado ao **ouvinte** para término de SSL no gateway de aplicativo ou diferente para maior segurança.

![cenário do ssl de ponta a ponta][1]

Neste exemplo, as solicitações que usam o TLS1.2 são roteadas para os servidores de back-end no Pool1 usando o SSL de ponta a ponta.

## <a name="end-to-end-ssl-and-whitelisting-of-certificates"></a>SSL de ponta a ponta e lista de exceções de certificados

O gateway de aplicativo se comunica somente com as instâncias de back-end conhecidas que têm o certificado na lista de exceções do gateway. Para habilitar a lista de exceções dos certificados, você precisa carregar a chave pública dos certificados do servidor de back-end no gateway de aplicativo (não no certificado-raiz). Somente as conexões com os back-ends conhecidos e na lista de exceções são permitidas. O back-ends restantes resultam em um erro de gateway. Os certificados autoassinados servem somente para teste e não são recomendados para cargas de trabalho de produção. Esses certificados devem estar na lista de exceções do gateway de aplicativo, conforme descrito nas etapas acima, antes de poder ser usados.

> [!NOTE]
> A configuração do certificado de autenticação não é necessária para serviços do Azure confiáveis, como aplicativos Serviço de Aplicativo do Azure.

## <a name="end-to-end-ssl-with-the-v2-sku"></a>SSL de ponta a ponta com a v2 do SKU

Os Certificados de Autenticação foram reprovados e substituídos por Certificados raiz confiáveis no SKU do Gateway de Aplicativo v2. Eles funcionam da mesma forma para Certificados de Autenticação com algumas diferenças importantes:

- Os certificados assinados por autoridades de certificação conhecidas cujo CN corresponde ao nome do host nas configurações de back-end de HTTP não exigem etapas adicionais para que o SSL de ponta a ponta funcione. 

   Por exemplo, se os certificados de back-end forem emitidos por uma autoridade de certificação bem conhecida e tiverem um CN da contoso.com, e o campo de host da configuração http de back-end também estiver definido como contoso.com, nenhuma etapa adicional será necessária. Você pode definir o protocolo de configuração http de back-end como HTTPS e, tanto a investigação de integridade quanto o caminho de dados, serão habilitados para SSL. Se você estiver usando o Azure App Service ou outros serviços web do Azure como seu backend, então estes também são implicitamente confiáveis e não são necessárias outras etapas para o SSL de ponta a ponta.
   
> [!NOTE] 
>
> Para que um certificado SSL seja confiável, esse certificado do servidor backend deve ter sido emitido por um CA incluído na loja confiável do Gateway de aplicativo.Se o certificado não foi emitido por um CA confiável, o Gateway de aplicativo verificará para ver se o certificado da CA emissora foi emitido por um CA confiável, e assim por diante até que um CA confiável seja encontrado (nesse ponto, uma conexão segura e confiável será estabelecida) ou nenhuma CA confiável pode ser encontrada (nesse ponto, o Gateway do aplicativo marcará o backend insalubre). Portanto, recomenda-se que o certificado de servidor backend contenha os CAs raiz e intermediário.

- Se o certificado for autoassinado ou assinado por intermediários desconhecidos, para ativar o SSL de ponta a ponta na SKU v2, um certificado raiz confiável deverá ser definido. O Gateway de Aplicativo só se comunicará com back-ends cujo certificado raiz do certificado do servidor corresponda a um da lista de certificados raiz confiáveis na configuração http de back-end associada ao pool.

> [!NOTE] 
>
> O certificado auto-assinado deve fazer parte de uma cadeia de certificados. Um único certificado auto-assinado sem cadeia não é suportado no V2 SKU.

- Além da correspondência do certificado raiz, o Gateway de Aplicativo também validará se a configuração do Host especificada na configuração http de back-end corresponde à configuração do nome comum (CN) apresentado pelo certificado SSL do servidor de back-end. Ao tentar estabelecer uma conexão SSL com o back-end, o Gateway de Aplicativo definirá a extensão de Indicação de Nome de Servidor (SNI) para o Host especificado na configuração http de back-end.
- Se **escolher o nome do host do endereço de back-end** for escolhido em vez do campo Host na configuração http de back-end, o cabeçalho SNI será sempre definido como o FQDN do pool de back-end e o CN no certificado SSL do servidor de back-end deverá corresponder ao FQDN. Os membros do backend pool com IPs não são suportados neste cenário.
- O certificado raiz é um certificado raiz codificado em base64 a partir dos certificados do servidor de back-end.

## <a name="next-steps"></a>Próximas etapas

Depois de aprender sobre SSL de ponta a ponta, vá para [Configurar um SSL de ponta a ponta usando o gateway de aplicativo com o PowerShell](application-gateway-end-to-end-ssl-powershell.md) para criar um Gateway de Aplicativo usando o SSL de ponta a ponta.

<!--Image references-->

[1]: ./media/ssl-overview/scenario.png
