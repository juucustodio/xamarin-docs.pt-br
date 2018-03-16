---
title: "Trabalhando com os padrões do usuário"
description: "Este artigo aborda o trabalho com NSUserDefault para salvar as configurações padrão em um aplicativo ou extensão do iOS Xamarin."
ms.topic: article
ms.prod: xamarin
ms.assetid: DAE7FFC4-B8C9-4D9E-886A-9B2388452EEB
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/07/2016
ms.openlocfilehash: ee79c79d7b3226f23851a3157e5a609d7cfc4cf4
ms.sourcegitcommit: 028936cd2fe547963c1cf82343c3ee16f658089a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/16/2018
---
# <a name="working-with-user-defaults"></a>Trabalhando com os padrões do usuário

_Este artigo aborda o trabalho com NSUserDefault para salvar as configurações padrão em uma extensão ou Xamarin.iOS App._


O `NSUserDefaults` classe fornece uma maneira para iOS, aplicativos e extensões para interagir programaticamente com o sistema de padrões de todo o sistema. Usando o sistema de padrões, o usuário pode configurar o comportamento de um aplicativo ou de estilo para atender às suas preferências (com base no design do aplicativo). Por exemplo, para apresentar dados de métricas vs medidas imperiais ou selecionar um tema de interface do usuário especificado.

Quando usado com grupos de aplicativos, `NSUserDefaults` também fornece uma maneira de se comunicar entre aplicativos (ou extensões) em um grupo específico.

<a name="About-User-Defaults" />

## <a name="about-user-defaults"></a>Sobre os padrões do usuário

Como mencionado acima, padrões do usuário (`NSUserDefaults`) pode ser adicionado a um aplicativo (ou extensão) e usado para fornecer opções configuráveis que o usuário final pode modificar para ajustar a aparência ou a operação do aplicativo em tempo de execução.

Quando seu aplicativo é executado pela primeira vez, `NSUserDefaults` lê as chaves e valores de padrões de dados de usuário do aplicativo e os armazena em cache na memória para evitar a abrir e ler o banco de dados cada vez que um valor é necessário. 

> [!IMPORTANT]
> **Observação**: Apple não recomenda mais que a chamada de desenvolvedor de `Synchronize` método para sincronizar o cache de memória com o banco de dados diretamente. Em vez disso, ele será chamado automaticamente em intervalos periódicos para manter o cache na memória em sincronia com o banco de dados de padrões do usuário.

O `NSUserDefaults` classe contém diversos métodos de conveniência para ler e gravar valores de preferência para tipos de dados comuns, como: cadeia de caracteres, inteiro, float, booliano e URLs. Outros tipos de dados podem ser arquivados usando `NSData`, em seguida, lido ou gravado no banco de dados padrão do usuário. Para obter mais informações, consulte da Apple [preferências e configurações do guia de programação](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/UserDefaults/Introduction/Introduction.html#//apple_ref/doc/uid/10000059i).

<a name="Accessing-the-Shared-NSUserDefaults-Instance" />

## <a name="accessing-the-shared-nsuserdefaults-instance"></a>Acessar a instância NSUserDefaults compartilhado 

A instância de padrões de usuário compartilhado fornece acesso para os padrões do usuário para o usuário atual do dispositivo. Se o objeto compartilhado padrão não existir, ele é criado na primeira vez que são acessados e inicializado com as seguintes informações:

- Um `NSArgumentDomain` consiste os padrões analisados a partir do aplicativo atual.
- Domínio do identificador de pacote do aplicativo.
- Um `NSGlobalDomain` consiste o padrão é compartilhado por todos os aplicativos.
- Um domínio separado para cada idioma de preferência do usuário.
- Um `NSRegistrationDomain` com um conjunto de padrões temporários que pode ser modificado pelo aplicativo para garantir que estejam sempre pesquisas com êxito.

Para acessar a instância compartilhada padrões do usuário, use o seguinte código:

```csharp
// Get Shared User Defaults
var plist = NSUserDefaults.StandardUserDefaults;
```

<a name="Accessing-an-App-Group-NSUserDefaults-Instance" />

## <a name="accessing-an-app-group-nsuserdefaults-instance"></a>Acessar uma instância de NSUserDefaults do grupo de aplicativos

Como mencionado acima, usando grupos de aplicativos, `NSUserDefaults` pode ser usado para comunicação entre aplicativos (ou extensões) em um grupo específico. Primeiro, você precisará garantir que o grupo de aplicativos e as IDs de aplicativo necessário foi corretamente configuradas no **certificados, identificadores e perfis** seção [Centro de desenvolvimento do iOS](https://developer.apple.com/devcenter/ios/) e foram instalados no ambiente de desenvolvimento.

Em seguida, seus projetos de aplicativo e/ou extensão precisam ter uma das IDs de aplicativo válido criado acima e o `Entitlements.plist` arquivo deve ser incluído no pacote de aplicativo com os grupos de aplicativo habilitado e especificado.

Com tudo no lugar, compartilhado padrões de usuário do aplicativo grupo podem ser acessados usando o código a seguir:

```csharp
// Get App Group User Defaults
var plist = new NSUserDefaults ("group.com.xamarin.todaysharing", NSUserDefaultsType.SuiteName);
```

Onde `group.com.xamarin.todaysharing` é o grupo de aplicativos criado na **certificados, identificadores e perfis** que você deseja acessar. Para obter mais informações, consulte o [recursos de grupo do aplicativo](~/ios/deploy-test/provisioning/capabilities/app-groups-capabilities.md) documentação.

<a name="Reading-Default-Values" />

## <a name="reading-default-values"></a>Ler os valores padrão

Depois de você ter acessado o usuário padrão banco de dados desejado, você pode ler os valores dos padrões usando pares chave/valor e vários métodos de conveniência, com base no tipo de dados que está sendo lidos:

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

Por exemplo, o código a seguir lê um valor booliano dos padrões do usuário:

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

- `SetBool` -Grava o valor booliano fornecido para a chave especificada.
- `SetDouble` -Grava o valor double fornecido a chave especificada.
- `SetFloat` -Grava o valor de float fornecida para a chave especificada.
- `SetString` -Grava o valor de cadeia de caracteres fornecida para a chave especificada.
- `SetURL` -Grava a URL fornecida (`NSUrl`) valor para a chave especificada.

Por exemplo, o código a seguir seria gravar um valor booleano para os padrões do usuário:

```csharp
// Get Shared User Defaults
var plist = NSUserDefaults.StandardUserDefaults;
...

// Save value
plist.SetBool(useHeader, "UseHeader");
...

```

> [!IMPORTANT]
> **Observação:** quando seu aplicativo é executado pela primeira vez, `NSUserDefaults` lê as chaves e valores de padrões de dados de usuário do aplicativo e os armazena em cache na memória para evitar a abrir e ler o banco de dados cada vez que um valor é necessário.



<a name="Summary" />

## <a name="summary"></a>Resumo

Este artigo abordou a `NSUserDefaults` classe e como ele pode ser usado para fornecer um conjunto de opções que o usuário final pode usar para configurar seu Xamarin.iOS App. Além disso, ele coberto usando grupos de aplicativos se comuniquem entre uma extensão e seu aplicativo-pai, ou entre aplicativos em um grupo.


## <a name="related-links"></a>Links relacionados

- [Amostras do tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [As preferências e configurações do guia de programação](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/UserDefaults/Introduction/Introduction.html#//apple_ref/doc/uid/10000059i)
- [NSUserDefaults](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/Foundation/Classes/NSUserDefaults_Class/#//apple_ref/doc/constant_group/NSUserDefaults_Domains)
