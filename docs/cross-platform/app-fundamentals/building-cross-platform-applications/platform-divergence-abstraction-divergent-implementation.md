---
title: Parte 4 – Lidar com várias plataformas
description: Este documento descreve como manipular divergência de aplicativo com base na plataforma ou recurso. Ele aborda o tamanho da tela, metáforas de navegação, toque e gestos, as notificações por push e paradigmas de interface, como listas e guias.
ms.prod: xamarin
ms.assetid: BBE47BA8-78BC-6A2B-63BA-D1A45CB1D3A5
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: f4558d5172d7dcbd2e14e6b74a347dd2f1e3a234
ms.sourcegitcommit: 817d26585093cd180a36b28179eb354b0eb900b3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55292123"
---
# <a name="part-4---dealing-with-multiple-platforms"></a>Parte 4 – Lidar com várias plataformas

## <a name="handling-platform-divergence-amp-features"></a>Tratamento de divergência de plataforma &amp; recursos

Divergência não é apenas um problema de 'multiplataforma'; dispositivos na plataforma 'mesmo' têm recursos diferentes (especialmente a ampla variedade de dispositivos Android que estão disponíveis). O mais óbvio e básica são o tamanho da tela, mas outros atributos do dispositivo podem variar e exigem um aplicativo para verificar determinados recursos e se comportam de maneira diferente com base na sua presença (ou ausência).

Isso significa que todos os aplicativos precisam lidar com degradação gradual de funcionalidade, caso contrário, apresentar um conjunto de recursos de pouco atraente, o menor denominador comum. Integração profunda do Xamarin com os SDKs nativos de cada plataforma permitem que os aplicativos para tirar proveito da funcionalidade específica da plataforma, portanto, faz sentido criar aplicativos para usar esses recursos.

Consulte a documentação de recursos de plataforma para uma visão geral de como as plataformas diferem na funcionalidade.

## <a name="examples-of-platform-divergence"></a>Exemplos de divergência de plataforma

### <a name="fundamental-elements-that-exist-across-platforms"></a>Elementos fundamentais que existem entre plataformas

Há algumas características de aplicativos móveis que são universais.
Estes são os conceitos de nível superior que são geralmente verdadeiro para todos os dispositivos e, portanto, podem formar a base do design do seu aplicativo:

-  Seleção de recursos por meio de guias ou menus
-  Listas de dados e rolagem
-  Modos de exibição únicos dos dados
-  Editar modos de exibição únicos dos dados
-  Navegar de volta

Ao projetar seu fluxo de alto nível de tela, você pode basear uma experiência de usuário comum sobre esses conceitos.

### <a name="platform-specific-attributes"></a>Atributos específicos de plataforma

Além dos elementos básicos que existem em todas as plataformas, você precisará resolver diferenças de plataforma de chave em seu design. Você precisa considerar (e escrever o código especificamente para lidar com) essas diferenças:

-   **Tamanhos de tela** – algumas plataformas (como as versões anteriores do Windows Phone e iOS) têm como padrão os tamanhos de tela são relativamente simples de destino. Dispositivos com Android possuem uma grande variedade de dimensões de tela, que exigem mais esforço para dar suporte a seu aplicativo.
-   **As metáforas de navegação** – diferem entre as plataformas (por exemplo. hardware 'Voltar' controle de botão, Panorama da interface do usuário) e nas plataformas (Android 2 e 4, iPhone vs iPad).
-   **Teclados** – dispositivos Android alguns têm teclados físicos, enquanto outros têm apenas um teclado de software. Código que detecta quando um teclado de soft está ocultando a parte da tela precisa ser sensíveis a essas diferenças.
-   **Toque e gestos** – suporte do sistema operacional para o reconhecimento de gesto varia, especialmente em versões mais antigas de cada sistema operacional. Versões anteriores do Android tem muito suporte limitado para operações de toque, que significa que dão suporte a dispositivos mais antigos pode exigir um código separado
-   **Notificações por push** – há implementações/recursos diferentes em cada plataforma (por exemplo. Blocos dinâmicos no Windows).

### <a name="device-specific-features"></a>Recursos específicos do dispositivo

Determinar o que os recursos mínimos necessários para o aplicativo devem ser; ou, quando decidir quais recursos adicionais para aproveitar em cada plataforma. Código será necessário para detectar recursos e funcionalidade de desabilitar ou oferecem alternativas (por exemplo uma alternativa à localização geográfica pode ser permitir que o usuário digite um local ou escolha de um mapa):

-   **Câmera** – funcionalidade difere entre os dispositivos: alguns dispositivos não tem uma câmera, outros têm ambas as câmeras frontal e traseira. Algumas câmeras são capazes de gravação de vídeo.
-   **& Mapas de localização geográfica** – suporte para GPS ou Wi-Fi local não está presente em todos os dispositivos. Aplicativos também precisam atender os vários níveis de precisão é suportado por cada método.
-   **Acelerômetro, giroscópio e bússola** – esses recursos normalmente são encontrados em apenas uma seleção de dispositivos em cada plataforma, portanto, os aplicativos quase sempre precisam fornecer um fallback quando não há suporte para o hardware.
-   **Twitter e Facebook** – apenas 'interno' no iOS5 e iOS6, respectivamente. Em versões anteriores e outras plataformas você precisará fornecer suas próprias funções de autenticação e a interface diretamente com cada API dos serviços.
-   **Próximo campo comunicações (NFC)** – apenas em (alguns) telefones Android (no momento da gravação).

## <a name="dealing-with-platform-divergence"></a>Lidando com divergência de plataforma

Há duas abordagens diferentes para dar suporte a várias plataformas da mesma base de código, cada um com seu próprio conjunto de vantagens e desvantagens.

-   **Abstração de plataforma** – padrão de fachada de negócios, fornece um acesso unificado entre plataformas e abstrai as implementações de plataforma específica em uma API única e unificada.
-   **Implementação divergente** – recursos de invocação de plataforma específica por meio de implementações divergentes por meio de ferramentas de arquiteturas como interfaces e herança ou compilação condicional.

## <a name="platform-abstraction"></a>Abstração de plataforma

### <a name="class-abstraction"></a>Abstração de classe

Usando as interfaces ou classes base definida no código compartilhado e implementado ou estendido em projetos específicos da plataforma. Escrevendo e estender o código compartilhado com abstrações de classe é particularmente adequada para bibliotecas de classes portáteis, porque eles têm um subconjunto limitado da estrutura disponível para eles e não podem conter diretivas de compilador para oferecer suporte de ramificações de código específico da plataforma.

#### <a name="interfaces"></a>Interfaces

Usar interfaces permite que você implementar classes específicas da plataforma que ainda podem ser passadas em suas bibliotecas compartilhadas para aproveitar o código comum.

A interface é definida no código compartilhado e passada para a biblioteca compartilhada como um parâmetro ou uma propriedade.

Os aplicativos específicos da plataforma podem, em seguida, implementar a interface e ainda aproveitar o código compartilhado ' processar '-lo.

 **Vantagens**

A implementação pode conter código específico da plataforma e até mesmo fazer referência a bibliotecas externas específicas da plataforma.

 **Desvantagens**

Necessidade de criar e passar as implementações para o código compartilhado. Se a interface é usada profundas dentro do código compartilhado, em seguida, ele acaba sendo passada por vários parâmetros de método ou caso contrário, propagada por meio da cadeia de chamada. Se o código compartilhado usa muitas das interfaces diferentes, em seguida, eles devem todos ser criados e definidos no código compartilhado em algum lugar.

#### <a name="inheritance"></a>Herança

O código compartilhado pode implementar as classes abstratas ou virtuais poderia ser estendidas em um ou mais projetos específicos da plataforma. Isso é semelhante a usar interfaces, mas com algum comportamento já implementado. Há diferentes pontos de Vista se interfaces ou herança são a melhor opção de design: em particular porque c# só permite a herança única ele pode ditar a maneira como suas APIs podem ser criadas no futuro. Use a herança com cuidado.

As vantagens e desvantagens das interfaces se aplicam igualmente a herança, com a vantagem adicional de classe base pode conter um código de implementação (talvez uma toda a plataforma independente implementação que pode ser opcionalmente estendido).

## <a name="xamarinforms"></a>Xamarin.Forms

Consulte a [xamarin. Forms](~/get-started/index.md) documentação.

### <a name="other-cross-platform-libraries"></a>Outras bibliotecas de plataforma cruzada

Essas bibliotecas também oferecem funcionalidade de plataforma cruzada para C# os desenvolvedores:

- [**Xamarin.Essentials** ](~/essentials/index.md) – APIs de plataforma cruzada para recursos comuns.
- [**SkiaSharp** ](~/xamarin-forms/user-interface/graphics/skiasharp/index.md) – gráficos 2D de plataforma cruzada.

## <a name="conditional-compilation"></a>Compilação condicional

Há algumas situações em que seu código compartilhado ainda precisará funcionam de forma diferente em cada plataforma, possivelmente acessar classes ou recursos que se comportam de maneira diferente. Compilação condicional funciona melhor com projetos de ativos compartilhados, onde o mesmo arquivo de origem está sendo referenciado em vários projetos que têm diferentes símbolos definidos.

Projetos do Xamarin sempre definem `__MOBILE__` qual é a verdadeira para iOS e os projetos de aplicativo do Android (Observe o sublinhado duplo pré e pós-correção esses símbolos).

```csharp
#if __MOBILE__
// Xamarin iOS or Android-specific code
#endif
```
#### <a name="ios"></a>iOS

Xamarin. IOS define `__IOS__` que você pode usar para detectar dispositivos iOS.

```csharp
#if __IOS__
// iOS-specific code
#endif
```

Também há símbolos específicos de inspeção e TV:

```csharp
#if __TVOS__
// tv-specific stuff
#endif

#if __WATCHOS__
// watch-specific stuff
#endif
```

#### <a name="android"></a>Android

Código que deve ser compilado somente em aplicativos xamarin. Android pode usar o seguinte

```csharp
#if __ANDROID__
// Android-specific code
#endif
```

Cada versão de API também define uma nova diretiva de compilador, para que o código como este permitirá que você adicionar recursos se APIs mais recentes são direcionadas. Cada nível de API inclui todos os símbolos de nível 'inferiores'. Esse recurso não é realmente útil para dar suporte a várias plataformas; normalmente o `__ANDROID__` símbolo será suficiente.

```csharp
#if __ANDROID_11__
// code that should only run on Android 3.0 Honeycomb or newer
#endif
```

#### <a name="mac"></a>Mac

Atualmente não há um símbolo embutido para xamarin. Mac, mas você pode adicionar seu próprio em Mac projeto de aplicativo **opções > Build > compilador** na **definir símbolos** caixa ou editar o **. csproj**  arquivo e adicione existe (por exemplo `__MAC__`)

```xml
<PropertyGroup><DefineConstants>__MAC__;$(DefineConstants)</DefineConstants></PropertyGroup>
```

#### <a name="universal-windows-platform-uwp"></a>UWP (Plataforma Universal do Windows)

Use `WINDOWS_UWP`. Não há nenhum sublinhados que cercam a cadeia de caracteres, como os símbolos de plataforma do Xamarin.

```csharp
#if WINDOWS_UWP
// UWP-specific code
#endif
```

#### <a name="using-conditional-compilation"></a>Usando a compilação condicional

Um exemplo simples de estudo de caso de compilação condicional é definir o local do arquivo para o arquivo de banco de dados SQLite. As três plataformas têm requisitos ligeiramente diferentes para especificar o local do arquivo:

-   **iOS** – Apple prefere dados de não usuário a ser colocado em um local específico (o diretório de biblioteca), mas não há nenhuma constante de sistema para este diretório. Código específico da plataforma é necessário para criar o caminho correto.
-   **Android** – o caminho do sistema retornado por `Environment.SpecialFolder.Personal` é um local para armazenar o arquivo de banco de dados que seja aceitável.
-   **Windows Phone** – o mecanismo de armazenamento isolado não permite que um caminho completo seja especificado, apenas um caminho relativo e nome de arquivo.
-   **Plataforma Windows universal** – usa `Windows.Storage` APIs.

O código a seguir usa a compilação condicional para garantir a `DatabaseFilePath` está correto para cada plataforma:

```csharp
public static string DatabaseFilePath {
        get {
    var filename = "TodoDatabase.db3";
#if SILVERLIGHT
    // Windows Phone 8
    var path = filename;
#else

#if __ANDROID__
    string libraryPath = Environment.GetFolderPath(Environment.SpecialFolder.Personal); ;
#else
#if __IOS__
        // we need to put in /Library/ on iOS5.1 to meet Apple's iCloud terms
        // (they don't want non-user-generated data in Documents)
        string documentsPath = Environment.GetFolderPath (Environment.SpecialFolder.Personal); // Documents folder
        string libraryPath = Path.Combine (documentsPath, "..", "Library");
#else
        // UWP
        string libraryPath = Windows.Storage.ApplicationData.Current.LocalFolder.Path;
#endif
#endif
        var path = Path.Combine (libraryPath, filename);
#endif
        return path;
}
```

O resultado é uma classe que pode ser criada e usada em todas as plataformas, colocar o arquivo de banco de dados do SQLite em um local diferente em cada plataforma.
