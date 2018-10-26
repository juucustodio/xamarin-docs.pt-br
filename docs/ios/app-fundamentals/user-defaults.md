---
title: Trabalhando com os padrões do usuário no xamarin. IOS
description: Este artigo aborda o trabalho com NSUserDefaults para salvar as configurações padrão em um aplicativo Xamarin. iOS ou uma extensão. Ele descreve NSUserDefaults em um alto nível e discute como ler e gravar valores.
ms.prod: xamarin
ms.assetid: DAE7FFC4-B8C9-4D9E-886A-9B2388452EEB
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/07/2016
ms.openlocfilehash: 688db534d6c99a8fadb7535f0532f9c1e9564707
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50120886"
---
# <a name="working-with-user-defaults-in-xamarinios"></a>Trabalhando com os padrões do usuário no xamarin. IOS

_Este artigo aborda o trabalho com NSUserDefault para salvar as configurações padrão em uma extensão ou Xamarin.iOS App._


O `NSUserDefaults` classe fornece uma maneira para iOS, aplicativos e extensões para interagir programaticamente com o sistema de padrões de todo o sistema. Ao usar o sistema usa como padrão, o usuário pode configurar o comportamento de um aplicativo ou definição de estilo para atender às suas preferências (com base no design do aplicativo). Por exemplo, para apresentar dados da métrica vs Imperial medidas ou selecione um tema da interface do usuário fornecida.

Quando usado com grupos de aplicativos, `NSUserDefaults` também fornece uma maneira para se comunicar entre aplicativos (ou extensões) em um grupo específico.

<a name="About-User-Defaults" />

## <a name="about-user-defaults"></a>Sobre os padrões do usuário

Conforme mencionado acima, padrões do usuário (`NSUserDefaults`) podem ser adicionados a um aplicativo (ou extensão) e usado para fornecer opções configuráveis que o usuário final pode modificar para ajustar a aparência ou a operação do aplicativo em tempo de execução.

Quando seu aplicativo é executado pela primeira vez, `NSUserDefaults` lê as chaves e valores do banco de dados do aplicativo usuário assume como padrão e armazena em cache na memória para evitar abrir e ler o banco de dados cada vez que um valor é necessário. 

> [!IMPORTANT]
> A Apple não recomenda que a chamada de desenvolvedor a `Synchronize` método para sincronizar o cache na memória com o banco de dados diretamente. Em vez disso, ele será chamado automaticamente em intervalos periódicos para manter o cache na memória em sincronia com o banco de dados de padrões do usuário.

O `NSUserDefaults` classe contém vários métodos de conveniência para ler e gravar valores de preferência para tipos de dados comuns, como: cadeia de caracteres, inteiro, float, booliano e URLs. Outros tipos de dados podem ser arquivados usando `NSData`, em seguida, lidos ou gravados no banco de dados do usuário assume como padrão. Para obter mais informações, consulte da Apple [preferências e configurações de guia de programação](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/UserDefaults/Introduction/Introduction.html#//apple_ref/doc/uid/10000059i).

<a name="Accessing-the-Shared-NSUserDefaults-Instance" />

## <a name="accessing-the-shared-nsuserdefaults-instance"></a>Acessando a instância NSUserDefaults compartilhado 

A instância de padrões do usuário compartilhado fornece acesso para os padrões do usuário para o usuário atual do dispositivo. Se o objeto compartilhado assume como padrão não existir, ele é criado na primeira vez que ele é acessado e inicializado com as seguintes informações:

- Um `NSArgumentDomain` que consistem em padrões analisado a partir do aplicativo atual.
- Domínio do identificador de pacote do aplicativo.
- Um `NSGlobalDomain` que consistem em padrões compartilhado por todos os aplicativos.
- Um domínio separado para cada um dos idiomas de preferência do usuário.
- Um `NSRegistrationDomain` com um conjunto de padrões temporários que podem ser modificadas pelo aplicativo para garantir que pesquisas sempre são bem-sucedidas.

Para acessar a instância de padrões do usuário compartilhada, use o seguinte código:

```csharp
// Get Shared User Defaults
var plist = NSUserDefaults.StandardUserDefaults;
```

<a name="Accessing-an-App-Group-NSUserDefaults-Instance" />

## <a name="accessing-an-app-group-nsuserdefaults-instance"></a>Acessando uma instância de NSUserDefaults do grupo de aplicativos

Conforme mencionado acima, usando grupos de aplicativos, `NSUserDefaults` pode ser usado para comunicação entre aplicativos (ou extensões) em um grupo específico. Primeiro, você precisará garantir que o grupo de aplicativos e as IDs do aplicativo necessários foram corretamente configuradas na **certificados, identificadores e perfis** seção [Centro de desenvolvimento do iOS](https://developer.apple.com/devcenter/ios/) e tiver sido instalado no ambiente de desenvolvimento.

Em seguida, seus projetos de aplicativo e/ou extensão precisam ter uma das IDs do aplicativo válido criada acima e o `Entitlements.plist` arquivo tem de ser incluído no pacote de aplicativo com os grupos de aplicativo habilitado e especificado.

Com tudo isso em vigor, compartilhado padrões de usuário do aplicativo de grupo pode ser acessados usando o seguinte código:

```csharp
// Get App Group User Defaults
var plist = new NSUserDefaults ("group.com.xamarin.todaysharing", NSUserDefaultsType.SuiteName);
```

Em que `group.com.xamarin.todaysharing` é o grupo de aplicativos criado na **certificados, identificadores e perfis** que você deseja acessar. Para obter mais informações, consulte o [recursos do grupo de aplicativo](~/ios/deploy-test/provisioning/capabilities/app-groups-capabilities.md) documentação.

<a name="Reading-Default-Values" />

## <a name="reading-default-values"></a>Lendo os valores padrão

Depois que você acessou o usuário padrão banco de dados desejado, você pode ler os valores dos padrões usando pares chave/valor e vários métodos de conveniência, com base no tipo de dados que está sendo lidos:

- `ArrayForKey` -Retorna uma matriz de `NSObjects` para determinado valor de chave.
- `BoolForKey` -Retorna um valor booliano para a chave especificada.
- `DataForKey` -Retorna um `NSData` objeto para a chave especificada.
- `DictionaryForKey` -Retorna um `NSDictionary` para a chave especificada.
- `DoubleForKey` -Retorna um valor duplo para a chave especificada.
- `FloatForKey` -Retorna um valor de float para a chave especificada.
- `IntForKey` -Retorna um valor inteiro para a chave especificada.
- `StringArrayForKey` -Retorna uma matriz de `String` objetos a partir do valor de chave especificado.
- `StringForKey` -Retorna um valor de cadeia de caracteres para a chave especificada.
- `URLForKey` -Retorna um `NSUrl` valor para a chave especificada.

Por exemplo, o código a seguir seria ler um valor booliano dos padrões do usuário:

```csharp
// Get Shared User Defaults
var plist = NSUserDefaults.StandardUserDefaults;
...

// Get value
var useHeader = plist.BoolForKey("UseHeader");

```

<a name="Writing-Default-Values" />

## <a name="writing-default-values"></a>Gravando valores padrão

Como ler os valores acima, após você ter acessado o usuário padrão banco de dados desejado, você pode gravar valores para os padrões usando pares chave/valor e vários métodos de conveniência, com base no tipo de dados que estão sendo gravados:

- `SetBool` -Grava o valor booliano especificado para a chave especificada.
- `SetDouble` -Grava o valor duplo fornecido para a chave especificada.
- `SetFloat` -Grava o valor de float fornecida para a chave especificada.
- `SetString` -Grava o valor de cadeia de caracteres fornecida para a chave especificada.
- `SetURL` -Grava a URL fornecida (`NSUrl`) o valor para a chave especificada.

Por exemplo, o código a seguir escreveria um valor booliano para os padrões do usuário:

```csharp
// Get Shared User Defaults
var plist = NSUserDefaults.StandardUserDefaults;
...

// Save value
plist.SetBool(useHeader, "UseHeader");
...

```

> [!IMPORTANT]
> Quando seu aplicativo é executado pela primeira vez, `NSUserDefaults` lê as chaves e valores do banco de dados do aplicativo usuário assume como padrão e armazena em cache na memória para evitar abrir e ler o banco de dados cada vez que um valor é necessário.



<a name="Summary" />

## <a name="summary"></a>Resumo

Este artigo abordou a `NSUserDefaults` classe e como ele pode ser usado para fornecer um conjunto de opções que o usuário final pode usar para configurar seu App Xamarin.iOS. Além disso, ele abordou usando grupos de aplicativos para se comunicar entre uma extensão e seu aplicativo pai ou entre aplicativos em um grupo.


## <a name="related-links"></a>Links relacionados

- [Amostras do tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [As preferências e configurações de guia de programação](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/UserDefaults/Introduction/Introduction.html#//apple_ref/doc/uid/10000059i)
- [NSUserDefaults](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/Foundation/Classes/NSUserDefaults_Class/#//apple_ref/doc/constant_group/NSUserDefaults_Domains)
