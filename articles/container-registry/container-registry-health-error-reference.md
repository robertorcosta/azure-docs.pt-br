---
title: Referência de erro para verificação de saúde
description: Códigos de erro e possíveis soluções para problemas encontrados executando o comando de diagnóstico de verificação de saúde az acr no Registro de Contêineres do Azure
ms.topic: article
ms.date: 07/02/2019
ms.openlocfilehash: 971b28b2bf8d9ac22cec0efe979837886762cf17
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80289134"
---
# <a name="health-check-error-reference"></a>Referência de erro de verificação de saúde

A seguir estão detalhes sobre códigos de erro retornados pelo comando [az acr check-health.][az-acr-check-health] Para cada erro, possíveis soluções são listadas.

## <a name="docker_command_error"></a>DOCKER_COMMAND_ERROR

Este erro significa que o cliente Docker para CLI não pôde ser encontrado. Como resultado, as seguintes verificações adicionais não são executadas: encontrando a versão do Docker, avaliando o status do daemon do Docker e executando um comando de tração docker.

*Soluções potenciais*: Instalar cliente Docker; adicionar o caminho do Docker às variáveis do sistema.

## <a name="docker_daemon_error"></a>DOCKER_DAEMON_ERROR

Este erro significa que o status do daemon do Docker não está disponível ou que não pôde ser alcançado usando o CLI. Como resultado, as operações `docker login` `docker pull`do Docker (como e ) não estão disponíveis através da CLI.

*Soluções potenciais*: Reiniciar o daemon do Docker ou validar que ele está instalado corretamente.

## <a name="docker_version_error"></a>DOCKER_VERSION_ERROR

Este erro significa que a CLI `docker --version`não foi capaz de executar o comando .

*Soluções potenciais*: Tente executar o comando manualmente, certifique-se de ter a versão CLI mais recente e investigue a mensagem de erro.

## <a name="docker_pull_error"></a>DOCKER_PULL_ERROR

Esse erro significa que a CLI não foi capaz de puxar uma imagem de amostra para o seu ambiente.

*Soluções potenciais*: Valide se todos os componentes necessários para puxar uma imagem estão funcionando corretamente.

## <a name="helm_command_error"></a>HELM_COMMAND_ERROR

Esse erro significa que o cliente Helm não pôde ser encontrado pela CLI, o que impede outras operações da Helm.

*Soluções potenciais*: Verifique se o cliente Helm está instalado e se seu caminho é adicionado às variáveis do ambiente do sistema.

## <a name="helm_version_error"></a>HELM_VERSION_ERROR

Este erro significa que a CLI não foi capaz de determinar a versão Helm instalada. Isso pode acontecer se a versão azure CLI (ou se a versão helm) estiver sendo usada.

*Soluções potenciais*: Atualize-se para a versão mais recente do Azure CLI ou para a versão recomendada do Helm; execute o comando manualmente e investigue a mensagem de erro.

## <a name="connectivity_dns_error"></a>CONNECTIVITY_DNS_ERROR

Esse erro significa que o DNS para o servidor de login do registro foi pingado, mas não respondeu, o que significa que ele não está disponível. Isso pode indicar alguns problemas de conectividade. Alternativamente, o registro pode não existir, o usuário pode não ter as permissões no registro (para recuperar seu servidor de login corretamente), ou o registro de destino está em uma nuvem diferente da usada no Cli do Azure.

*Soluções potenciais*: Validar a conectividade; verificar a ortografia do registro, e esse registro existe; verificar se o usuário tem as permissões certas nele e se a nuvem do registro é a mesma que é usada na CLI do Azure.

## <a name="connectivity_forbidden_error"></a>CONNECTIVITY_FORBIDDEN_ERROR

Esse erro significa que o ponto final do desafio para o registro dado respondeu com um status HTTP proibido 403. Esse erro significa que os usuários não têm acesso ao registro, provavelmente por causa de uma configuração de rede virtual. Para ver as regras de firewall `az acr show --query networkRuleSet --name <registry>`configuradas no momento, execute .

*Soluções potenciais*: Remova as regras de rede virtuais ou adicione o endereço IP do cliente atual à lista permitida.

## <a name="connectivity_challenge_error"></a>CONNECTIVITY_CHALLENGE_ERROR

Esse erro significa que o ponto final do desafio do registro de destino não emitiu um desafio.

*Soluções potenciais*: Tente novamente depois de algum tempo. Se o erro persistir, abra https://aka.ms/acr/issuesum problema em .

## <a name="connectivity_aad_login_error"></a>CONNECTIVITY_AAD_LOGIN_ERROR

Esse erro significa que o ponto final do desafio do registro de destino emitiu um desafio, mas o registro não suporta a autenticação do Azure Active Directory.

*Soluções potenciais*: Tente uma maneira diferente de autenticar, por exemplo, com credenciais de administrador. Se os usuários precisarem autenticar usando o Azure https://aka.ms/acr/issuesActive Directory, abra um problema em .

## <a name="connectivity_refresh_token_error"></a>CONNECTIVITY_REFRESH_TOKEN_ERROR

Esse erro significa que o servidor de login do registro não respondeu com um token de atualização, então o acesso ao registro de destino foi negado. Esse erro pode ocorrer se o usuário não tiver as permissões certas no registro ou se as credenciais do usuário para o Azure CLI estiverem obsoletas.

*Soluções potenciais*: Verificar se o usuário tem as permissões certas no registro; executar `az login` para atualizar permissões, tokens e credenciais.

## <a name="connectivity_access_token_error"></a>CONNECTIVITY_ACCESS_TOKEN_ERROR

Esse erro significa que o servidor de login do registro não respondeu com um token de acesso, de modo que o acesso ao registro de destino foi negado. Esse erro pode ocorrer se o usuário não tiver as permissões certas no registro ou se as credenciais do usuário para o Azure CLI estiverem obsoletas.

*Soluções potenciais*: Verificar se o usuário tem as permissões certas no registro; executar `az login` para atualizar permissões, tokens e credenciais.

## <a name="connectivity_ssl_error"></a>CONNECTIVITY_SSL_ERROR

Esse erro significa que o cliente não conseguiu estabelecer uma conexão segura com o registro do contêiner. Esse erro geralmente ocorre se você estiver executando ou usando um servidor proxy.

*Soluções potenciais*: Mais informações sobre o trabalho por trás de um proxy podem ser [encontradas aqui](https://github.com/Azure/azure-cli/blob/master/doc/use_cli_effectively.md#working-behind-a-proxy).

## <a name="login_server_error"></a>LOGIN_SERVER_ERROR

Esse erro significa que a CLI não conseguiu encontrar o servidor de login do registro dado, e nenhum sufixo padrão foi encontrado para a nuvem atual. Esse erro pode ocorrer se o registro não existir, se o usuário não tiver as permissões certas no registro, se a nuvem do registro e a nuvem cli atual do Azure não coincidirem, ou se a versão cli do Azure estiver obsoleta.

*Soluções potenciais*: Verifique se a ortografia está correta e se o registro existe; verificar se o usuário tem as permissões certas no registro e que as nuvens do registro e do ambiente CLI correspondem; atualizar a Cli do Azure para a versão mais recente.

## <a name="notary_version_error"></a>NOTARY_VERSION_ERROR

Este erro significa que a CLI não é compatível com a versão atualmente instalada do Docker/Notary. Tente rebaixar sua versão notary.exe para uma versão anterior ao 0.6.0, substituindo manualmente o cliente Notary da instalação do Docker para resolver esse problema.

## <a name="next-steps"></a>Próximas etapas

Para obter opções para verificar a saúde de um registro, consulte [Verificar a saúde de um registro de contêineres do Azure](container-registry-check-health.md).

Consulte o [FAQ](container-registry-faq.md) para perguntas frequentes e outras questões conhecidas sobre o Registro de Contêineres do Azure.





<!-- LINKS - internal -->
[az-acr-check-health]: /cli/azure/acr#az-acr-check-health
