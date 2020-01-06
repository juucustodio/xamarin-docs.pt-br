---
title: Parte 4 – Lidar com várias plataformas
description: Este documento descreve como lidar com a divergência de aplicativos com base na plataforma ou no recurso. Ele discute o tamanho da tela, metáforas de navegação, toque e gestos, notificações por push e paradigmas de interface, como listas e guias.
ms.prod: xamarin
ms.assetid: BBE47BA8-78BC-6A2B-63BA-D1A45CB1D3A5
author: davidortinau
ms.author: daortin
ms.date: 03/23/2017
ms.openlocfilehash: e1fa76faf0313a21061af585052a3b137243db55
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75488641"
---
# <a name="part-4---dealing-with-multiple-platforms"></a>Parte 4 – Lidar com várias plataformas

## <a name="handling-platform-divergence-amp-features"></a>Lidando com recursos de &amp; divergência de plataforma

A divergência não é apenas um problema de "plataforma cruzada"; os dispositivos na plataforma ' mesma ' têm recursos diferentes (especialmente a ampla variedade de dispositivos Android que estão disponíveis). O mais óbvio e básico é o tamanho da tela, mas outros atributos de dispositivo podem variar e exigir que um aplicativo Verifique certos recursos e se comporte de forma diferente com base em sua presença (ou ausência).

Isso significa que todos os aplicativos precisam lidar com a degradação normal da funcionalidade, ou então apresentar um conjunto de recursos do denominador mais baixo e incomum. A profunda integração do Xamarin com os SDKs nativos de cada plataforma permite que os aplicativos tirem proveito da funcionalidade específica da plataforma, de modo que faz sentido criar aplicativos para usar esses recursos.

Consulte a documentação de recursos da plataforma para obter uma visão geral de como as plataformas diferem na funcionalidade.

## <a name="examples-of-platform-divergence"></a>Exemplos de divergência de plataforma

### <a name="fundamental-elements-that-exist-across-platforms"></a>Elementos fundamentais que existem entre plataformas

Há algumas características de aplicativos móveis que são universais.
Esses são conceitos de nível superior que geralmente são verdadeiros de todos os dispositivos e, portanto, podem formar a base do design do seu aplicativo:

- Seleção de recursos por meio de guias ou menus
- Listas de dados e rolagem
- Exibições únicas de dados
- Editando exibições únicas de dados
- Navegando de volta

Ao projetar seu fluxo de tela de alto nível, você pode basear uma experiência de usuário comum nesses conceitos.

### <a name="platform-specific-attributes"></a>Atributos específicos da plataforma

Além dos elementos básicos que existem em todas as plataformas, você precisará abordar as principais diferenças de plataforma no seu design. Talvez seja necessário considerar (e escrever código especificamente para manipular) essas diferenças:

- **Tamanhos de tela** – algumas plataformas (como Ios e versões de Windows Phone anteriores) têm tamanhos de tela padronizados que são relativamente simples de direcionar. Os dispositivos Android têm uma grande variedade de dimensões de tela, que exigem mais esforço para dar suporte ao seu aplicativo.
- **Metáforas de navegação** – diferem entre plataformas (por exemplo, botão "voltar" do hardware, controle de interface do usuário do panorama) e em plataformas (Android 2 e 4, iPhone vs iPad).
- **Teclados – alguns** dispositivos Android têm placas físicas, enquanto outras têm apenas um Keyboard de software. O código que detecta quando um teclado suave está ocultando parte da tela precisa ser sensível a essas diferenças.
- **Toque e gestos** – o suporte do sistema operacional para reconhecimento de gestos varia, especialmente em versões mais antigas de cada sistema operacional. As versões anteriores do Android têm suporte muito limitado para operações de toque, o que significa que o suporte a dispositivos mais antigos pode exigir código separado
- **Notificações por push** – há diferentes recursos/implementações em cada plataforma (por exemplo, Blocos dinâmicos no Windows).

### <a name="device-specific-features"></a>Recursos específicos do dispositivo

Determine quais os recursos mínimos necessários para o aplicativo devem ser; ou quando decidir quais recursos adicionais aproveitar em cada plataforma. O código será necessário para detectar recursos e desabilitar a funcionalidade ou as alternativas de oferta (por exemplo, uma alternativa para localização geográfica poderia ser permitir que o usuário digite um local ou escolha em um mapa):

- **Câmera** – a funcionalidade difere em dispositivos: alguns dispositivos não têm uma câmera, outros têm câmeras frente e traseira. Algumas câmeras são capazes de gravar vídeo.
- **Mapas de & de localização geográfica** – suporte para GPS ou local Wi-Fi não está presente em todos os dispositivos. Os aplicativos também precisam atender aos diferentes níveis de precisão com suporte em cada método.
- **Acelerômetro, giroscópio e bússola** – esses recursos geralmente são encontrados em apenas uma seleção de dispositivos em cada plataforma, de modo que os aplicativos quase sempre precisam fornecer um fallback quando o hardware não tem suporte.
- **Twitter e Facebook** – somente "interno" em IOS5 e iOS6, respectivamente. Em versões anteriores e outras plataformas, você precisará fornecer suas próprias funções e interface de autenticação diretamente com a API de cada serviço.
- **NFC (comunicação a curta distância)** – somente em (alguns) telefones Android (no momento da gravação).

## <a name="dealing-with-platform-divergence"></a>Lidando com a divergência de plataforma

Há duas abordagens diferentes para dar suporte a várias plataformas da mesma base de código, cada uma com seu próprio conjunto de benefícios e desvantagens.

- **Abstração de plataforma** – padrão de fachada de negócios, fornece um acesso unificado entre plataformas e abstrai as implementações de plataforma específicas em uma única API unificada.
- **Implementação divergente** – invocação de recursos específicos da plataforma por meio de implementações divergentes por meio de ferramentas arquitetônicas, como interfaces e herança ou compilação condicional.

## <a name="platform-abstraction"></a>Abstração de plataforma

### <a name="class-abstraction"></a>Abstração de classe

Usando interfaces ou classes base definidas no código compartilhado e implementadas ou estendidas em projetos específicos da plataforma. Escrever e estender código compartilhado com abstrações de classe é especialmente adequado para bibliotecas de classes portáteis porque eles têm um subconjunto limitado da estrutura disponível para eles e não podem conter diretivas de compilador para dar suporte a ramificações de código específicas da plataforma.

#### <a name="interfaces"></a>Interfaces

O uso de interfaces permite que você implemente classes específicas da plataforma que ainda podem ser passadas para suas bibliotecas compartilhadas para tirar proveito do código comum.

A interface é definida no código compartilhado e passada para a biblioteca compartilhada como um parâmetro ou propriedade.

Os aplicativos específicos da plataforma podem então implementar a interface e ainda aproveitar o código compartilhado para "Process".

 **Vantagens**

A implementação pode conter código específico da plataforma e até mesmo referenciar bibliotecas externas específicas da plataforma.

 **Desvantagens**

Ter que criar e passar implementações no código compartilhado. Se a interface for usada em profundidade no código compartilhado, ela acabará sendo passada por meio de vários parâmetros de método ou, de outra forma, por Push por meio da cadeia de chamadas. Se o código compartilhado usar muitas interfaces diferentes, todos eles deverão ser criados e definidos no código compartilhado em algum lugar.

#### <a name="inheritance"></a>{1&gt;Herança&lt;1}

O código compartilhado pode implementar classes abstratas ou virtuais que poderiam ser estendidas em um ou mais projetos específicos da plataforma. Isso é semelhante ao uso de interfaces, mas com algum comportamento já implementado. Há diferentes pontos de vista sobre se as interfaces ou herança são uma opção de design melhor: C# em particular porque só permite a herança única que pode determinar a maneira como suas APIs podem ser projetadas no futuro. Use a herança com cuidado.

As vantagens e desvantagens das interfaces se aplicam igualmente à herança, com a vantagem adicional de que a classe base pode conter algum código de implementação (talvez uma implementação independente de plataforma inteira que possa ser opcionalmente estendida).

## <a name="xamarinforms"></a>Xamarin.Forms

Consulte a documentação do [Xamarin. Forms](~/get-started/index.yml) .

### <a name="other-cross-platform-libraries"></a>Outras bibliotecas de plataforma cruzada

Essas bibliotecas também oferecem funcionalidade de plataforma cruzada C# para desenvolvedores:

- [**Xamarin. Essentials**](~/essentials/index.md) – APIs de plataforma cruzada para recursos comuns.
- [**SkiaSharp**](~/xamarin-forms/user-interface/graphics/skiasharp/index.md) – gráficos 2D entre plataformas.

## <a name="conditional-compilation"></a>{1&gt;Compilação condicional&lt;1}

Há algumas situações em que seu código compartilhado ainda precisará trabalhar de forma diferente em cada plataforma, possivelmente acessando classes ou recursos que se comportam de forma diferente. A compilação condicional funciona melhor com projetos de ativos compartilhados, em que o mesmo arquivo de origem está sendo referenciado em vários projetos que têm diferentes símbolos definidos.

Os projetos do Xamarin sempre definem `__MOBILE__` que é verdadeiro para projetos de aplicativos iOS e Android (Observe os dois pontos de pontuação dupla pré e pós-correção desses símbolos).

```csharp
#if __MOBILE__
// Xamarin iOS or Android-specific code
#endif
```

#### <a name="ios"></a>iOS

O Xamarin. iOS define `__IOS__` que você pode usar para detectar dispositivos iOS.

```csharp
#if __IOS__
// iOS-specific code
#endif
```

Também há símbolos específicos de inspeção e de TV:

```csharp
#if __TVOS__
// tv-specific stuff
#endif

#if __WATCHOS__
// watch-specific stuff
#endif
```

#### <a name="android"></a>Android

O código que só deve ser compilado em aplicativos Xamarin. Android pode usar o seguinte

```csharp
#if __ANDROID__
// Android-specific code
#endif
```

Cada versão de API também define uma nova diretiva de compilador, portanto, um código como esse permitirá que você adicione recursos se as APIs mais novas forem destinadas. Cada nível de API inclui todos os símbolos de nível "inferior". Esse recurso não é realmente útil para dar suporte a várias plataformas; Normalmente, o símbolo de `__ANDROID__` será suficiente.

```csharp
#if __ANDROID_11__
// code that should only run on Android 3.0 Honeycomb or newer
#endif
```

#### <a name="mac"></a>Mac

Atualmente, não há um símbolo interno para o Xamarin. Mac, mas você pode adicionar o seu próprio nas opções de projeto de aplicativo do Mac **> compilar > compilador** na caixa **definir símbolos** ou editar o arquivo **. csproj** e adicionar lá (por exemplo `__MAC__`)

```xml
<PropertyGroup><DefineConstants>__MAC__;$(DefineConstants)</DefineConstants></PropertyGroup>
```

#### <a name="universal-windows-platform-uwp"></a>UWP (Plataforma Universal do Windows)

Use `WINDOWS_UWP`. Não há nenhum sublinhado ao redor da cadeia de caracteres como os símbolos da plataforma Xamarin.

```csharp
#if WINDOWS_UWP
// UWP-specific code
#endif
```

#### <a name="using-conditional-compilation"></a>Usando a compilação condicional

Um exemplo simples de estudo de caso de compilação condicional é definir o local do arquivo do banco de dados SQLite. As três plataformas têm requisitos um pouco diferentes para especificar o local do arquivo:

- **Ios** – a Apple prefere que os dados de não-usuário sejam colocados em um local específico (o diretório de biblioteca), mas não há nenhuma constante de sistema para esse diretório. O código específico da plataforma é necessário para criar o caminho correto.
- **Android** – o caminho do sistema retornado por `Environment.SpecialFolder.Personal` é um local aceitável para armazenar o arquivo de banco de dados.
- **Windows Phone** – o mecanismo de armazenamento isolado não permite que um caminho completo seja especificado, apenas um caminho relativo e um nome de arquivo.
- **Plataforma universal do Windows** – usa APIs de `Windows.Storage`.

O código a seguir usa a compilação condicional para garantir que a `DatabaseFilePath` esteja correta para cada plataforma:

```csharp
public static string DatabaseFilePath
{
    get
    {
        var filename = "TodoDatabase.db3";
#if SILVERLIGHT
        // Windows Phone 8
        var path = filename;
#else

#if __ANDROID__
        string libraryPath = Environment.GetFolderPath(Environment.SpecialFolder.Personal);
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
        var path = Path.Combine(libraryPath, filename);
#endif
        return path;
    }
}
```

O resultado é uma classe que pode ser criada e usada em todas as plataformas, colocando o arquivo de banco de dados SQLite em um local diferente em cada plataforma.
