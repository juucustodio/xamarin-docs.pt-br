---
title: fastlane para iOS - match
ms.topic: article
ms.prod: xamarin
ms.assetid: 92B35AB1-7AB7-3D3B-DB31-CC971E0B43AE
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/19/2017
ms.openlocfilehash: 435ba4c3799288724625ca06016770b3ecad56a5
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="fastlane-for-ios---match"></a>fastlane para iOS - match

Tradicionalmente, o provisionamento do dispositivo é executado por cada membro de uma equipe de desenvolvimento pelo Xcode ou no Portal do desenvolvedor da Apple. Ele consiste em várias etapas:

- Solicitando um certificado de desenvolvimento
- Adicionando um dispositivo ao portal
- Criando uma ID do aplicativo
- Criando um perfil de provisionamento
- Baixando perfis e certificados

Mais informações sobre as etapas necessárias para configurar um dispositivo para desenvolvimento manual ou pelo Xcode podem ser encontradas no guia [Provisionamento de dispositivo](~/ios/get-started/installation/device-provisioning/index.md).

Este guia mostra como usar as ferramentas do Fastlane como uma alternativa ao uso do Xcode.

## <a name="installation"></a>Instalação

Para saber mais sobre como instalar o fastlane, consulte o guia Introdução ao [fastlane](~/ios/deploy-test/provisioning/fastlane/index.md#Installation).

<a name="whatismatch" />

## <a name="what-is-match"></a>O que é o match?

O match se encarrega de criar e manter certificados de assinatura de código e provisionar perfis e permite que uma equipe de desenvolvimento do iOS compartilhe uma identidade de assinatura de código com todos os desenvolvedores.

Ao implantar um aplicativo na loja de aplicativos, conduzir testes beta ou instalar o aplicativo em um dispositivo, cada membro de uma equipe de desenvolvimento tem sua própria identidade de assinatura. Isso pode resultar em identidades e perfis conflitantes e na necessidade de criar, girar e gerenciar manualmente perfis e IDs de aplicativo.

Em vez disso, o Match cria e mantém todos os certificados e os perfis para você e os armazena em um repositório git privado. Isso permite que todos os desenvolvedores em uma equipe acessem e usem essas credenciais. Por sua vez, ele fornece segurança adicional para seus certificados: além de serem um repositório git privado, eles também são criptografados com uma [frase secreta](#passphrase). Armazenar artefatos de assinatura de código em um repositório permite que os administradores e os agentes de equipe atualizem e girem certificados conforme necessário, o que significa que menos tempo é gasto distribuindo novos certificados para cada desenvolvedor.

> [!IMPORTANT]
> o match não dá suporte para perfis empresariais internos.

<a name="initializing" />

## <a name="initializing-your-project-with-match"></a>Inicialização de projeto com o match

Se você for o administrador da equipe, crie um repositório git privado, por meio do github.com ou do bitbucket.com. Certifique-se de que adicionou todos os membros da equipe como contribuidores do repositório.

Usando seu terminal, altere o diretório para o diretório do projeto e execute:

    fastlane match init

Quando solicitado, digite a URL do repositório git:

 [ ![](match-images/fastlane-image7.png "Insira a URL do repositório git")](match-images/fastlane-image7.png)

A URL pode ser encontrada e copiada clicando no botão **Clonar ou Baixar** no github.com, conforme ilustrado abaixo:

[ ![](match-images/fastlane-image6.png "A URL no botão Clonar ou Baixar no github.com")](match-images/fastlane-image6.png)

A inicialização do projeto cria um matchfile – um arquivo de texto que pode ser editado para passar as variáveis de ambiente para a ferramenta de correspondência. Um exemplo de matchfile é ilustrado abaixo:

[ ![](match-images/fastlane-image8.png "Um exemplo de matchfile")](match-images/fastlane-image8.png)

<a name="running" />

## <a name="running-match"></a>Execução do match

> [!NOTE]
> O fastlane recomenda que, antes de executar o match pela primeira vez, você deve considerar a limpeza dos perfis e certificados existentes usando o [comando match nuke](#using).

Dependendo do ambiente exigido, você pode usar qualquer um dos comandos a seguir para criar um perfil de provisionamento e um novo certificado e armazená-los em seu novo repositório git:

    fastlane match appstore

    fastlane match adhoc

    fastlane match development

Além de criar novos certificados e perfis, usar qualquer um desses comandos adicionará (ou atualizará, se eles existirem) os seguintes itens ao repositório git:

- Pasta Certificados
- Pasta Perfis
- Um arquivo Leiame com instruções básicas
- Uma versão compatível

[ ![](match-images/fastlane-image9.png "A estrutura do projeto no repositório git")](match-images/fastlane-image9.png)

Os perfis de provisionamento estão instalados em `~/Library/MobileDevice/Provisioning Profiles`. Certificados e chaves privadas são instalados diretamente no seu conjunto de chaves.

<a name="using" />

### <a name="using-the-nuke-command"></a>Usando o comando `nuke`

Se tiver certificados desorganizados, você poderá usar `nuke` para revogar certificados e perfis para cada ambiente usando os seguintes comandos:

    fastlane match nuke

Como revogar todos os certificados e os perfis de provisionamento para um ambiente específico:

    fastlane match nuke development

 ou

    fastlane match nuke distribution

O fastlane confirmará os arquivos que serão removidos antes que algo seja excluído.

<a name="passphrase" />

### <a name="passphrase"></a>Frase secreta

Ao executar `match` pela primeira vez, será solicitado que você defina uma senha para o repositório git. Não se esqueça da senha, pois você precisará dela quando executar a correspondência em um computador diferente. Isso é uma camada adicional de segurança: cada um dos arquivos será criptografado usando OpenSSL. Cada execução subsequente do `match` em um novo computador solicitará essa frase secreta. Depois de inseri-la inicialmente, a frase secreta será adicionada ao conjunto de chaves local.

Para definir a frase secreta para descriptografar os perfis usando uma variável de ambiente, use `MATCH_PASSWORD`.

<a name="options" />

## <a name="additional-options"></a>Opções adicionais

As opções a seguir podem ser usadas para dar suporte adicional ao usar o Match:

- Use o sinalizador `-–help` para obter uma lista de todos os comandos disponíveis:

        fastlane match cert --help

- Use o sinalizador `-–verbose` para aumentar os detalhes da saída:

        fastlane match --development --verbose

- Use o sinalizador `--force_for_new_devices` para forçar a renovação dos perfis de provisionamento se a contagem do dispositivo no portal do desenvolvedor tiver sido alterada

        fastlane match development --force_for_new_devices

## <a name="related-links"></a>Links relacionados

- [fastlane - match](https://github.com/fastlane/fastlane/blob/master/match/README.md)
