---
title: "Parte 4 - lidar com várias plataformas"
ms.topic: article
ms.prod: xamarin
ms.assetid: BBE47BA8-78BC-6A2B-63BA-D1A45CB1D3A5
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: 21cd08ad2eb9c78ba1bcd6b31400a38266c65e51
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2018
---
# <a name="part-4---dealing-with-multiple-platforms"></a>Parte 4 - lidar com várias plataformas

## <a name="handling-platform-divergence-amp-features"></a>Tratamento de divergência de plataforma &amp; recursos

Divergência não é apenas um problema de 'cross-platform'; dispositivos na plataforma 'mesmo' têm recursos diferentes (especialmente a ampla variedade de dispositivos com Android que estão disponíveis). Mais óbvios e básico são o tamanho da tela, mas outros atributos do dispositivo podem variar e exigem que um aplicativo verifique determinados recursos e se comportam de maneira diferente com base na sua presença (ou ausência).

Isso significa que todos os aplicativos precisam lidar com degradação de forma suave da funcionalidade, caso contrário, apresentar um conjunto de recursos atraente, menor denominador comum. Profunda integração da Xamarin com os SDKs nativo de cada plataforma permitem que aplicativos tirar proveito da funcionalidade específica de plataforma, faz sentido projetar aplicativos para usar esses recursos.

Consulte a documentação de recursos de plataforma para obter uma visão geral de como as plataformas podem diferir em funcionalidade.

 <a name="Examples_of_Platform_Divergence" />


### <a name="examples-of-platform-divergence"></a>Exemplos de divergência de plataforma

 <a name="Fundamental_elements_that_exist_across_platforms" />


#### <a name="fundamental-elements-that-exist-across-platforms"></a>Elementos fundamentais que existem entre plataformas

Há algumas características de aplicativos móveis que são universais.
Estes são os conceitos de alto nível que são geralmente verdadeiro de todos os dispositivos e, portanto, podem formam a base de design do aplicativo:

-  Seleção de recursos por meio de guias ou menus
-  Listas de dados e rolagem
-  Modos de exibição únicos de dados
-  Editar modos de exibição únicos de dados
-  Navegar de volta


Ao projetar seu fluxo de tela de alto nível, você pode basear uma experiência de usuário comum esses conceitos.

 <a name="platform-specific_attributes" />


#### <a name="platform-specific-attributes"></a>atributos específicos de plataforma

Além dos elementos básicos que existem em todas as plataformas, você precisará abordar diferenças de plataforma de chave em seu design. Você precisará considerar (e escrever código especificamente para tratar) essas diferenças:

-   **Tamanhos de tela** – algumas plataformas (como iOS e versões anteriores do Windows Phone) têm como padrão os tamanhos de tela são relativamente simples de destino. Dispositivos Android tem uma grande variedade de dimensões de tela, o que requer mais esforço para dar suporte a seu aplicativo.
-   **Metáforas navegação** – diferem entre plataformas (por exemplo. botão de hardware 'Voltar', controle de Panorama da interface do usuário) e nas plataformas (Android 2 e 4, iPhone vs iPad).
-   **Teclados** – dispositivos Android alguns têm teclados físicos enquanto outros têm apenas um teclado de software. Código que detecta quando um teclado de software está impedindo a exibição de parte da tela deve estar atento a essas diferenças.
-   **Toque e gestos** – varia de suporte do sistema operacional para o reconhecimento de gesto, especialmente em versões mais antigas de cada sistema operacional. Versões anteriores do Android tem muito suporte limitado para operações de toque, que significa que suporte a dispositivos mais antigos pode exigir código separado
-   **Notificações por push** – há recursos/implementações diferentes em cada plataforma (por exemplo. Blocos dinâmicos no Windows).


 <a name="Device-specific_features" />


#### <a name="device-specific-features"></a>Recursos específicos do dispositivo

Determinar o que os recursos mínimos necessários para o aplicativo devem ser; ou, quando decidir quais recursos adicionais para tirar proveito em cada plataforma. Código será necessário para detectar recursos e funcionalidade de desabilitar ou oferecem alternativas (por exemplo. uma alternativa à localização geográfica pode ser permitir que o usuário digite um local ou escolha um mapa):

-   **Câmera** – funcionalidade difere em todos os dispositivos: alguns dispositivos não tem uma câmera, outras pessoas tenham ambas as câmeras frontal e traseira. Algumas câmeras são capazes de gravação de vídeo.
-   **Localização geográfica e mapas** – suporte para GPS ou Wi-Fi local não está presente em todos os dispositivos. Aplicativos também precisam atender para vários níveis de precisão que tem suporte para cada método.
-   **Acelerômetro, giroscópio e bússola** – esses recursos geralmente são encontrados em apenas uma seleção de dispositivos em cada plataforma para aplicativos quase sempre precisam fornecer um fallback quando não há suporte para o hardware.
-   **Twitter e Facebook** – somente 'interna' iOS5 e iOS6 respectivamente. Em versões anteriores e outras plataformas você precisará fornecer suas próprias funções de autenticação e a interface diretamente com cada um de API dos serviços.
-   **Perto da comunicação de distância (NFC)** – somente em (alguns) telefones Android (em tempo de gravação).


 <a name="Dealing_with_Platform_Divergence" />


### <a name="dealing-with-platform-divergence"></a>Lidando com divergência de plataforma

Há duas abordagens diferentes para dar suporte a várias plataformas da mesma base de código, cada qual com seu próprio conjunto de vantagens e desvantagens.

-   **Abstração de plataforma** – padrão de fachada de negócios, fornece um acesso unificado entre plataformas e resume as implementações de plataforma específica em uma única API unificada.
-   **Implementação divergente** – recursos de invocação de plataforma específica por meio de implementações divergentes por meio de ferramentas de arquitetura como interfaces e herança de compilação condicional.


 <a name="Platform_Abstraction" />


## <a name="platform-abstraction"></a>Abstração de plataforma

 <a name="Class_Abstraction" />


### <a name="class-abstraction"></a>Abstração de classe

Usando as interfaces ou classes de base definido no código compartilhado e implementado ou estendido em projetos específicos de plataforma. Escrevendo e código compartilhado com abstrações de classe de extensão é particularmente adequada para bibliotecas de classes portáteis porque eles têm um subconjunto limitado do framework disponível para eles e não podem conter as diretivas de compilador para dar suporte a ramificações de código específico da plataforma.

 <a name="Interfaces" />


#### <a name="interfaces"></a>Interfaces

Usar interfaces permite que você implemente classes específicas da plataforma que ainda podem ser passadas em suas bibliotecas compartilhadas para aproveitar o código comum.

A interface é definida no código compartilhado e passada para a biblioteca compartilhada como um parâmetro ou uma propriedade.

Os aplicativos de plataforma específica podem implementar a interface e ainda aproveitar código compartilhado para 'processar'.

 **Vantagens**

A implementação pode conter código específico da plataforma e até mesmo fazer referência a bibliotecas externas específico da plataforma.

 **Desvantagens**

Necessidade de criar e passar implementações para o código compartilhado. Se a interface é usada profundas dentro do código compartilhado, em seguida, ele acaba sendo passados por meio de vários parâmetros de método ou caso contrário, propagada por meio da cadeia de chamada. Se o código compartilhado usa muitas das interfaces diferentes, em seguida, eles devem ser criados e definidos no código compartilhado em algum lugar.

 <a name="Inheritance" />


#### <a name="inheritance"></a>Herança

O código compartilhado pode implementar classes abstract ou virtuais que podem ser estendidas em um ou mais projetos de plataforma específica. Isso é semelhante ao uso de interfaces, mas com alguns comportamentos já foi implementado. Há diferentes pontos de Vista se interfaces ou herança são a melhor opção de design: em particular porque c# permite apenas herança única ele pode ditam uma forma suas APIs podem ser criados no futuro. Use a herança com cuidado.

As vantagens e desvantagens das interfaces se aplicam igualmente a herança, com a vantagem adicional de que a classe base pode conter um código de implementação (talvez uma plataforma inteira independente implementação que pode ser estendida opcionalmente).

<a name="Xamarin.Forms" />

### <a name="xamarinforms"></a>Xamarin.Forms

Consulte o [xamarin. Forms](~/xamarin-forms/get-started/index.md) documentação.


### <a name="plug-in-cross-platform-functionality"></a>Funcionalidade Plug-in de plataforma cruzada

Você também pode estender os aplicativos de plataforma cruzada de uma maneira consistente usando plug-ins.

Vinculado do nosso [github de plug-ins](https://github.com/xamarin/plugins), a maioria dos plug-ins são o código-fonte aberto projetos (normalmente disponíveis para instalação via Nuget) que ajudarão-lo a implementam uma variedade de funcionalidades específicas de plataforma do status da bateria para configurações com um API comum que é fácil de consumir em aplicativos de plataforma Xamarin e xamarin. Forms.


<a name="Other_Cross-Platform_Libraries" />

### <a name="other-cross-platform-libraries"></a>Outras bibliotecas de plataforma cruzada

Há um número de 3º bibliotecas de terceiros disponíveis que fornecem funcionalidade de plataforma cruzada:

-   **MvvmCross** -  [https://github.com/slodge/MvvmCross/](https://github.com/slodge/MvvmCross/)
-   **Idioma** (para a localização) - [https://github.com/rdio/vernacular/](https://github.com/rdio/vernacular/)
-   **MonoGame** (para jogos XNA) - [http://monogame.codeplex.com/](http://monogame.codeplex.com/)
-   **NGraphics** - [NGraphics](https://github.com/praeclarum/NGraphics) e seu precursor [https://github.com/praeclarum/CrossGraphics](https://github.com/praeclarum/CrossGraphics)


 <a name="Divergent_Implementation" />


### <a name="divergent-implementation"></a>Implementação divergente

 <a name="Conditional_Compilation" />


#### <a name="conditional-compilation"></a>Compilação condicional

Há algumas situações em que seu código compartilhado ainda precisará funcionam de forma diferente em cada plataforma, possivelmente acessar classes ou recursos que se comportam de maneira diferente. Compilação condicional funciona melhor com projetos de ativo compartilhado, onde o mesmo arquivo de origem está sendo referenciado em vários projetos que têm diferentes símbolos definidos.

Sempre definem projetos de Xamarin `__MOBILE__` que é verdadeiro para iOS e projetos de aplicativo do Android (Observe o sublinhado duplo pré e pós-correção nesses símbolos).

```csharp
#if __MOBILE__
// Xamarin iOS or Android-specific code
#endif
```

<a name="iOS" />

##### <a name="ios"></a>iOS

Define xamarin `__IOS__` que você pode usar para detectar dispositivos iOS.

```csharp
#if __IOS__
// iOS-specific code
#endif
```

Também há símbolos específicos de TV e inspecionar:

```csharp
#if __TVOS__
// tv-specific stuff
#endif

#if __WATCHOS__
// watch-specific stuff
#endif
```

<a name="Android" />

##### <a name="android"></a>Android

Código que só deve ser compilado em aplicativos xamarin pode usar o seguinte

```csharp
#if __ANDROID__
// Android-specific code
#endif
```

Cada versão da API também define uma nova diretiva de compilador, para que o código como isso permitirá que você adicionar recursos se APIs mais recentes são direcionados. Cada nível de API inclui todos os símbolos de nível 'inferiores'. Este recurso não é realmente útil para dar suporte a várias plataformas; normalmente o `__ANDROID__` símbolo será suficiente.

```csharp
#if __ANDROID_11__
// code that should only run on Android 3.0 Honeycomb or newer
#endif
```

##### <a name="mac"></a>Mac

Atualmente não há um símbolo interno para Xamarin.Mac, mas você pode adicionar seu próprios no Mac projeto de aplicativo **opções > compilar > compilador** no **definir símbolos** caixa ou editar o **. csproj**  de arquivos e adicionar existe (por exemplo `__MAC__`)

```xml
<PropertyGroup><DefineConstants>__MAC__;$(DefineConstants)</DefineConstants></PropertyGroup>
```

<a name="Windows_Phone" />

##### <a name="windows-phone"></a>Windows Phone

Aplicativos do Windows Phone define dois símbolos – `WINDOWS_PHONE` e `SILVERLIGHT` – que pode ser usado para o código para a plataforma de destino. Eles não tem os sublinhados ao redor-los como os símbolos de plataforma Xamarin fazer.


<a name="Using_Conditional_Compilation" />

##### <a name="using-conditional-compilation"></a>Usando a compilação condicional

Um exemplo simples de estudo de caso de compilação condicional é definir o local do arquivo para o arquivo de banco de dados SQLite. As três plataformas têm requisitos ligeiramente diferentes para especificar o local do arquivo:

-   **iOS** – Apple prefere dados de usuário não sejam colocados em um local específico (o diretório de biblioteca), mas não há nenhuma constante de sistema para este diretório. Código de plataforma específica é necessária para criar o caminho correto.
-   **Android** – o caminho do sistema retornado por `Environment.SpecialFolder.Personal` é um local aceitável para armazenar o arquivo de banco de dados.
-   **Windows Phone** – o mecanismo de armazenamento isolado não permite um caminho completo seja especificado, apenas um caminho relativo e nome de arquivo.
-   **Plataforma universal do Windows** – usa `Windows.Storage` APIs.

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

O resultado é uma classe que pode ser criada e usada em todas as plataformas, colocando o arquivo de banco de dados SQLite em um local diferente em cada plataforma.

