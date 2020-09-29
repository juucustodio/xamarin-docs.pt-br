---
title: Trabalhando com padrões do usuário no Xamarin. iOS
description: Este artigo aborda como trabalhar com NSUserDefaults para salvar as configurações padrão em um aplicativo ou extensão do Xamarin iOS. Ele descreve o NSUserDefaults em um alto nível e discute como ler e gravar valores.
ms.prod: xamarin
ms.assetid: DAE7FFC4-B8C9-4D9E-886A-9B2388452EEB
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/07/2016
ms.openlocfilehash: 587ce48f3d40bb4f16634409920b01d150d628ef
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436311"
---
# <a name="working-with-user-defaults-in-xamarinios"></a>Trabalhando com padrões do usuário no Xamarin. iOS

_Este artigo aborda como trabalhar com NSUserDefault para salvar as configurações padrão em um aplicativo Xamarin. iOS ou extensão._

A `NSUserDefaults` classe fornece uma maneira para que aplicativos e extensões do IOS interajam programaticamente com o sistema de padrões de todo o sistema. Usando o sistema de padrões, o usuário pode configurar o comportamento ou o estilo de um aplicativo para atender às suas preferências (com base no design do aplicativo). Por exemplo, para apresentar dados em medidas métricas vs Imperial ou selecione um determinado tema da interface do usuário.

Quando usado com grupos de aplicativos, `NSUserDefaults` o também fornece uma maneira de se comunicar entre aplicativos (ou extensões) em um determinado grupo.

<a name="About-User-Defaults"></a>

## <a name="about-user-defaults"></a>Sobre os padrões do usuário

Como mencionado acima, os padrões do usuário ( `NSUserDefaults` ) podem ser adicionados a um aplicativo (ou extensão) e usados para fornecer opções configuráveis que o usuário final pode modificar para ajustar a aparência ou a operação do aplicativo em tempo de execução.

Quando seu aplicativo é executado pela primeira vez, `NSUserDefaults` o lê as chaves e os valores do banco de dados de padrões do usuário do aplicativo e os armazena em cache para a memória para evitar abrir e ler o banco de dados sempre que um valor é necessário. 

> [!IMPORTANT]
> A Apple não recomenda mais que o desenvolvedor chame o `Synchronize` método para sincronizar o cache na memória diretamente com o banco de dados. Em vez disso, ele será chamado automaticamente em intervalos periódicos para manter o cache na memória sincronizado com o banco de dados de padrões do usuário.

A `NSUserDefaults` classe contém vários métodos de conveniência para leitura e gravação de valores de preferência para tipos de dados comuns, como: String, Integer, float, Boolean e URLs. Outros tipos de dados podem ser arquivados usando `NSData` o e, em seguida, lidos ou gravados no banco de dados de padrões do usuário. Para obter mais informações, consulte o [Guia de programação de preferências e configurações](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/UserDefaults/Introduction/Introduction.html#//apple_ref/doc/uid/10000059i)da Apple.

<a name="Accessing-the-Shared-NSUserDefaults-Instance"></a>

## <a name="accessing-the-shared-nsuserdefaults-instance"></a>Acessando a instância NSUserDefaults compartilhada 

A instância de padrões de usuário compartilhado fornece acesso aos padrões do usuário para o usuário atual do dispositivo. Se o objeto de padrões compartilhados não existir, ele será criado na primeira vez que for acessado e inicializado com as seguintes informações:

- Um `NSArgumentDomain` que consiste nos padrões analisados a partir do aplicativo atual.
- O domínio do identificador de pacote do aplicativo.
- Um `NSGlobalDomain` que consiste nos padrões compartilhados por todos os aplicativos.
- Um domínio separado para cada um dos idiomas preferenciais do usuário.
- Um `NSRegistrationDomain` com um conjunto de padrões temporários que podem ser modificados pelo aplicativo para garantir que as pesquisas sempre sejam bem-sucedidas.

Para acessar a instância de padrões de usuário compartilhado, use o seguinte código:

```csharp
// Get Shared User Defaults
var plist = NSUserDefaults.StandardUserDefaults;
```

<a name="Accessing-an-App-Group-NSUserDefaults-Instance"></a>

## <a name="accessing-an-app-group-nsuserdefaults-instance"></a>Acessando uma instância do grupo de aplicativos NSUserDefaults

Conforme mencionado acima, usando grupos de aplicativos, o `NSUserDefaults` pode ser usado para se comunicar entre aplicativos (ou extensões) em um determinado grupo. Primeiro, você precisará garantir que o grupo de aplicativos e as IDs de aplicativos necessários tenham sido configurados corretamente na seção **certificados, identificadores & de perfis** no centro de desenvolvimento do [Ios](https://developer.apple.com/devcenter/ios/) e tenham sido instalados no ambiente de desenvolvimento.

Em seguida, seus projetos de aplicativo e/ou extensão precisam ter uma das IDs de aplicativo válidas criadas acima, e o `Entitlements.plist` arquivo deve ser incluído no pacote de aplicativos com os grupos de aplicativos habilitados e especificados.

Com tudo isso em vigor, os padrões do usuário do grupo de aplicativos compartilhados podem ser acessados usando o seguinte código:

```csharp
// Get App Group User Defaults
var plist = new NSUserDefaults ("group.com.xamarin.todaysharing", NSUserDefaultsType.SuiteName);
```

Em que `group.com.xamarin.todaysharing` é o grupo de aplicativos criado em **certificados, identificadores & perfis** que você deseja acessar. Para obter mais informações, consulte a documentação de [recursos do grupo de aplicativos](~/ios/deploy-test/provisioning/capabilities/app-groups-capabilities.md) .

<a name="Reading-Default-Values"></a>

## <a name="reading-default-values"></a>Lendo valores padrão

Depois de ter acessado o banco de dados padrão do usuário desejado, você pode ler valores dos padrões usando pares de chave/valor e vários métodos de conveniência com base no tipo de dados que estão sendo lidos:

- `ArrayForKey` -Retorna uma matriz de `NSObjects` para o valor de chave fornecido.
- `BoolForKey` -Retorna um valor booliano para a chave especificada.
- `DataForKey` -Retorna um `NSData` objeto para a chave fornecida.
- `DictionaryForKey` -Retorna um `NSDictionary` para a chave fornecida.
- `DoubleForKey` -Retorna um valor duplo para a chave especificada.
- `FloatForKey` -Retorna um valor float para a chave fornecida.
- `IntForKey` -Retorna um valor inteiro para a chave especificada.
- `StringArrayForKey` -Retorna uma matriz de `String` objetos do valor de chave fornecido.
- `StringForKey` -Retorna um valor de cadeia de caracteres para a chave fornecida.
- `URLForKey` -Retorna um `NSUrl` valor para a chave especificada.

Por exemplo, o código a seguir deve ler um valor booliano dos padrões do usuário:

```csharp
// Get Shared User Defaults
var plist = NSUserDefaults.StandardUserDefaults;
...

// Get value
var useHeader = plist.BoolForKey("UseHeader");

```

<a name="Writing-Default-Values"></a>

## <a name="writing-default-values"></a>Gravando valores padrão

Assim como na leitura de valores acima, depois de ter acessado o banco de dados padrão do usuário desejado, você pode gravar valores nos padrões usando pares de chave/valor e vários métodos de conveniência com base no tipo de dados que estão sendo gravados:

- `SetBool` -Grava o valor booliano fornecido para a chave especificada.
- `SetDouble` -Grava o valor duplo fornecido para a chave especificada.
- `SetFloat` -Grava o valor float fornecido para a chave especificada.
- `SetString` -Grava o valor de cadeia de caracteres fornecido para a chave especificada.
- `SetURL` -Grava o valor de URL ( `NSUrl` ) fornecido para a chave especificada.

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
> Quando seu aplicativo é executado pela primeira vez, `NSUserDefaults` o lê as chaves e os valores do banco de dados de padrões do usuário do aplicativo e os armazena em cache para a memória para evitar abrir e ler o banco de dados sempre que um valor é necessário.

<a name="Summary"></a>

## <a name="summary"></a>Resumo

Este artigo abordou a `NSUserDefaults` classe e como ela pode ser usada para fornecer um conjunto de opções que o usuário final pode usar para configurar seu aplicativo Xamarin. Ios. Além disso, ele abordou o uso de grupos de aplicativos para se comunicar entre uma extensão e seu aplicativo pai ou entre aplicativos em um grupo.

## <a name="related-links"></a>Links Relacionados

- [Exemplos do tvOS](/samples/browse/?products=xamarin&term=Xamarin.iOS%2btvOS)
- [Guia de programação de preferências e configurações](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/UserDefaults/Introduction/Introduction.html#//apple_ref/doc/uid/10000059i)
- [NSUserDefaults](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/Foundation/Classes/NSUserDefaults_Class/#//apple_ref/doc/constant_group/NSUserDefaults_Domains)