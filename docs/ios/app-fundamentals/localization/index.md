---
title: "Localização do iOS"
description: "Este documento abrange os recursos de localização do SDK do iOS e como acessá-los com o Xamarin."
ms.topic: article
ms.prod: xamarin
ms.assetid: DFD9EB4A-E536-18E4-C8FD-679BA9C836D8
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 04/28/2017
ms.openlocfilehash: ea91dbcf7148651cb5d10acae4ada8bb6758c39e
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="ios-localization"></a>Localização do iOS

_Este documento abrange os recursos de localização do SDK do iOS e como acessá-los com o Xamarin._

Consulte o [codificações de internacionalização](encodings.md) para obter instruções sobre como páginas de código/conjuntos de caracteres em aplicativos que devem processar dados não Unicode.

## <a name="ios-platform-features"></a>Recursos de plataforma do iOS

Esta seção descreve alguns dos recursos de localização em iOS. Vá para o [próxima seção](#basics) para ver exemplos e código específico.

### <a name="language"></a>Idioma

Os usuários escolher seu idioma no **configurações** aplicativo. Essa configuração afeta a cadeias de caracteres do idioma e as imagens exibidas pelo sistema operacional, bem como os aplicativos que detectam as configurações de idioma.

Isso é onde os usuários serão decidir se desejam ver em inglês, espanhol, japonês, francês ou outro idioma exibido em seus aplicativos.

É a lista real de idiomas com suporte no iOS 7: inglês (EUA), em inglês (Reino Unido), chinês (simplificado), chinês (tradicional), francês, alemão, italiano, japonês, coreano, espanhol, árabe, catalão, croata, tcheco, dinamarquês, holandês, finlandês, grego, hebraico, Húngaro, indonésio, malaio, norueguês, polonês, português, português (Brasil), romeno, russo, eslovaco, sueco, tailandês, turco, ucraniano e vietnamita, em inglês (Austrália), espanhol (México).

O idioma atual pode ser consultado, acessando o primeiro elemento do `PreferredLanguages` matriz:

```csharp
var lang = NSBundle.MainBundle.PreferredLocalizations[0];
```

Esse valor será um código de idioma, como `en` para inglês, `es` para espanhol, `ja` para japonês, etc. _O valor retornado é restrito a uma de suas localizações com suporte do aplicativo (usando regras de fallback para determinar a melhor correspondência)._

Código do aplicativo não sempre precisará verificar esse valor - Xamarin e iOS ambos fornecem recursos que ajudam a fornecer automaticamente a cadeia de caracteres correta ou o recurso para o idioma do usuário. Esses recursos são descritos no restante deste documento.

> [!NOTE]
> **Observação:** antes do iOS 9, o código recomendado era `var lang = NSLocale.PreferredLanguages [0];`.
>
> Os resultados retornados por este código alterado no iOS 9 - consulte [TN2418 de observação técnica](https://developer.apple.com/library/content/technotes/tn2418/_index.html) para obter mais informações.
>
> Você pode continuar a usar `NSLocale.PreferredLanguages [0]` para determinar os valores reais selecionados pelo usuário (independentemente de suas localizações seu aplicativo aceita).

### <a name="locale"></a>Localidade

Os usuários escolher a localidade no **configurações** aplicativo. Essa configuração afeta a maneira que as datas, horas, números e moeda são formatados.

Isso permite aos usuários escolher se eles consulte formatos de hora de 12 ou 24 horas, se o separador decimal é uma vírgula ou um ponto e a ordem do dia, mês e ano na exibição de data.

Com o Xamarin, você tem acesso às classes de iOS da Apple, ambos os (`NSNumberFormatter`), bem como as classes do .NET em System. Globalization. Os desenvolvedores devem avaliar o que é mais adequado às suas necessidades, pois há diferentes recursos disponíveis em cada. Em particular, se você estiver recuperando e exibindo preços as compras no aplicativo usando StoreKit definitivamente deve usar classes de formatação da Apple para obter as informações de preço retornadas.

A localidade atual pode ser consultada pelo:

- `NSLocale.CurrentLocale.LocaleIdentifier`
- `NSLocale.AutoUpdatingCurrentLocale.LocaleIdentifier`

O primeiro valor pode ser armazenados em cache pelo sistema operacional e portanto pode nem sempre reflete a localidade do usuário atualmente selecionada. Use o valor de segundo para obter o local selecionado no momento.


### <a name="nscurrentlocaledidchangenotification"></a>NSCurrentLocaleDidChangeNotification

iOS gera um `NSCurrentLocaleDidChangeNotification` quando o usuário atualiza a localidade. Aplicativos podem escutar essa notificação enquanto eles estão em execução e faça as alterações apropriadas para a interface do usuário.

<a name="basics" />

## <a name="localization-basics-in-ios"></a>Noções básicas de localização no iOS

Os seguintes recursos do iOS serão facilmente utilizados no Xamarin para fornecer recursos localizados para exibição ao usuário. Consulte o [TaskyL10n exemplo](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n) para saber como implementar essas ideias.

### <a name="infoplist"></a>Info.plist

Antes de começar, configure o **Info. plist** arquivo com as seguintes chaves:

- `CFBundleDevelopmentRegion` -o idioma padrão para o aplicativo (normalmente o idioma falado pelos desenvolvedores e usado em storyboards e recursos de cadeia de caracteres e imagem). No exemplo a seguir, **en** (para inglês) é especificado.
- `CFBundleLocalizations` -uma matriz de outras localizações com suporte do aplicativo, também usando códigos de idioma como **es** (espanhol) e **pt-PT** (português falado em Portugal).

```xml
<key>CFBundleDevelopmentRegion</key>
<string>en</string>
<key>CFBundleLocalizations</key>
<array>
  <string>de</string>
  <string>es</string>
  <string>ja</string>
  ...
</array>
```

### <a name="localizedstring-method"></a>Método LocalizedString

O `NSBundle.MainBundle.LocalizedString` método procura texto localizado que foi armazenado no **.strings** arquivos no projeto. Esses arquivos são organizados por idioma, nos diretórios especialmente nomeados com um **.lproj** sufixo.

#### <a name="strings-file-locations"></a>locais de arquivo .strings

- **Base.lproj** é o diretório que contém recursos para o idioma padrão.
  Geralmente, ele está localizado na raiz do projeto (mas também pode ser colocado no **recursos** pasta).
- **<language>.lproj** diretórios são criados para cada idioma suportado, normalmente no **recursos** pasta.

Pode haver um número de diferentes **.strings** arquivos em cada pasta de idioma:

- **Localizable.strings** -lista principal de texto localizado.
- **InfoPlist.strings** - determinadas chaves específicas são permitidas neste arquivo para traduzir coisas como o nome do aplicativo.
- **< nome do storyboard > .strings** -arquivo opcional que contém traduções para elementos de interface do usuário em um storyboard.

O **ação de compilação** para esses arquivos devem ser **recursos do pacote**.

#### <a name="strings-file-format"></a>formato de arquivo .strings

A sintaxe para valores de cadeia de caracteres localizada é:

```csharp
/* comment */
"key"="localized-value";
```

Você deve retirar os seguintes caracteres em cadeias de caracteres:

* `\"`  aspas
* `\\`  barra invertida
* `\n`  nova linha

Este é um exemplo **es/Localizable.strings** (ie. Arquivo espanhol) do exemplo:

```csharp
"<new task>" = "<new task>";
"Task Details" = "Detalles de la tarea";
"Name" = "Nombre";
"task name" = "nombre de la tarea";
"Notes" = "Notas";
"other task info"= "otra información de tarea";
"Done" = "Completo";
"Save" = "Guardar";
"Delete" = "Eliminar";
```

### <a name="images"></a>Imagens

Para localizar uma imagem no iOS:

1. Consulte a imagem no código, por exemplo:

  ```csharp
  UIImage.FromBundle("flag");
  ```

2. Coloque o arquivo de imagem padrão **flag.png** na **Base.lproj** (o diretório de idioma de desenvolvimento nativo).

3. Opcionalmente, coloque as versões localizadas da imagem em **.lproj** pastas para cada idioma (por exemplo. **es.lproj**, **ja.lproj**). Use o mesmo nome de arquivo **flag.png** em cada pasta de idioma.

Se não houver uma imagem para um idioma específico, o iOS voltar para a pasta de idioma nativo padrão e carregar a imagem de lá.


#### <a name="launch-images"></a>Imagens de inicialização

Use as convenções de nomenclatura padrão para as imagens de inicialização (e o XIB ou um Storyboard para modelos de iPhone 6) quando colocá-los no **.lproj** diretórios para cada idioma.

```csharp
Default.png
Default@2x.png
Default-568h@2x.png
LaunchScreen.xib
```

### <a name="app-name"></a>Nome do aplicativo

Colocando um **InfoPlist.strings** do arquivo em um **.lproj** diretório permite que você substitua alguns valores do aplicativo **Info. plist**, incluindo o nome do aplicativo:

```csharp
"CFBundleDisplayName" = "LeónTodo";
```

Outras chaves que você pode usar para [localizar cadeias de caracteres específicas do aplicativo](https://developer.apple.com/library/ios/documentation/MacOSX/Conceptual/BPInternational/LocalizingYourApp/LocalizingYourApp.html#//apple_ref/doc/uid/10000171i-CH5-SW21) são:

- CFBundleName
- CFBundleShortVersionString
- NSHumanReadableCopyright


<!--
## App icon

Does not seem to be possible (although it definitely used to be!).
-->

### <a name="localization-native-development-region"></a>Região de desenvolvimento nativo de localização

As cadeias de caracteres padrão (localizado no **Base.lproj** pasta) será considerado como o idioma 'fallback'. Isso significa que, se uma tradução é solicitada no código e não foi encontrada para um idioma atual, o **Base.lproj** pasta será pesquisada a cadeia de caracteres padrão a ser usado (se nenhuma correspondência for encontrada, a cadeia de caracteres de identificador de tradução em si é exibido).

Os desenvolvedores podem escolher um idioma diferente para ser o fallback definindo a chave plist `CFBundleDevelopmentRegionKey`. Seu valor deve ser definido para o código de idioma para o idioma de desenvolvimento nativo. Esta captura de tela mostra que um plist no Xamarin Studio com a região de desenvolvimento nativo definido para Espanhol (es):

![](images/cfbundledevelopmentregion.png "Propriedade InfoPList.strings")

Se o idioma padrão usado no seu storyboards e em todo o código não seja o inglês, defina esse valor para refletir o idioma nativo que está sendo usado em todo o código do aplicativo.

### <a name="dates-and-times"></a>Datas e horas

Embora seja possível usar as funções internas de data e hora do .NET (junto com a atual `CultureInfo`) para formatar datas e horas para uma localidade, isso seria ignorar específicas da localidade-configurações de usuário (que podem ser definidas separadamente de linguagem).

Usar o iOS `NSDateFormatter` para produzir saída que corresponde a preferência de localidade do usuário. O código de exemplo a seguir demonstra a data básica e opções de formatação de hora:

```csharp
var date = NSDate.Now;
var df = new NSDateFormatter ();
df.DateStyle = NSDateFormatterStyle.Full;
df.TimeStyle = NSDateFormatterStyle.Long;
Debug.WriteLine ("Full,Long: " + df.StringFor(date));
df.DateStyle = NSDateFormatterStyle.Short;
df.TimeStyle = NSDateFormatterStyle.Short;
Debug.WriteLine ("Short,Short: " + df.StringFor(date));
df.DateStyle = NSDateFormatterStyle.Medium;
df.TimeStyle = NSDateFormatterStyle.None;
Debug.WriteLine ("Medium,None: " + df.StringFor(date));
```

Resultados para inglês dos Estados Unidos:

```csharp
Full,Long: Friday, August 7, 2015 at 10:29:32 AM PDT
Short,Short: 8/7/15, 10:29 AM
Medium,None: Aug 7, 2015
```

Resultados para espanhol da Espanha:

```csharp
Full,Long: viernes, 7 de agosto de 2015, 10:26:58 GMT-7
Short,Short: 7/8/15 10:26
Medium,None: 7/8/2015
```

Consulte o Apple [data formatadores](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/DataFormatting/Articles/dfDateFormatting10_4.html) documentação para obter mais informações. Ao testar sensíveis formatação de data e hora, marque ambos **iPhone idioma** e **região** configurações.

<a name="rtl" />

### <a name="right-to-left-rtl-layout"></a>Layout da direita para esquerda (RTL)

iOS fornece uma série de recursos para ajudar na criação de aplicativos com reconhecimento de RTL:

* Use **Layout automático** `leading` e `trailing` atributos para o controle aligment (que corresponde ao *esquerdo* e *direita* para inglês, por exemplo, mas é revertido para idiomas RTL).
  O [ `UIStackView` ](~/ios/user-interface/controls/uistackview.md) controle é particularmente útil para dispor controles a serem consideradas ao RTL.
* Use `TextAlignment = UITextAlignment.Natural` para alinhamento de texto (que será *esquerdo* da maioria dos idiomas, mas *direita* para RTL).
* `UINavigationController` Inverte o botão Voltar e inverte a direção de passe o dedo automaticamente.

O capturas de tela a seguir mostram o [localizada **Tasky** exemplo](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n) em árabe e hebraico (embora inglês foi inserido nos campos):

[ ![](images/rtl-ar-sml.png "Localização em árabe")](images/rtl-ar.png "Arabic") 

[ ![](images/rtl-he-sml.png "Localização em hebraico")](images/rtl-he.png "Hebrew")

iOS inverte automaticamente a `UINavigationController`, e os outros controles são colocados dentro de `UIStackView` ou alinhado com Layout automático.
Texto RTL está localizado usando **.strings** arquivos da mesma forma como texto LTR.


<a name="code"/>

## <a name="localizing-the-ui-in-code"></a>Localizando a interface do usuário no código

O [Tasky (localizado no código)](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n) exemplo mostra como localizar um aplicativo em que a interface do usuário é criada em código (em vez de XIBs ou storyboards).

### <a name="project-structure"></a>Estrutura do projeto



![](images/solution-code.png "Árvore de recursos")

### <a name="localizablestrings-file"></a>Arquivo Localizable.strings

Conforme descrito acima, o **Localizable.strings** formato de arquivo consiste em pares chave-valor, onde a chave é uma cadeia de caracteres selecionados pelo usuário que indica

Espanhol (**es**) localizações para o exemplo são mostradas abaixo:

```csharp
"<new task>" = "<new task>";
"Task Details" = "Detalles de la tarea";
"Name" = "Nombre";
"task name" = "nombre de la tarea";
"Notes" = "Notas";
"other task info"= "otra información de tarea";
"Done" = "Completo";
"Save" = "Guardar";
"Delete" = "Eliminar";
```

### <a name="performing-the-localization"></a>Executando a localização

No código do aplicativo, sempre que o texto de exibição da interface do usuário é definido (se for o texto do rótulo, ou o espaço reservado de uma entrada, etc) o código usa o iOS `LocalizedString` função para recuperar a tradução correta para exibir:

```csharp
var localizedString = NSBundle.MainBundle.LocalizedString ("key", "optional");
someControl.Text = localizedString;
```

<a name="storyboard"/>

## <a name="localizing-storyboard-uis"></a>Localizando as interfaces do Storyboard

O exemplo [Tasky (localizada storyboard)](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10nStoryboard) mostra como localizar texto em controles em um storyboard.

### <a name="project-structure"></a>Estrutura do projeto

O **Base.lproj** diretório contém o storyboard e também deve conter as imagens usadas no aplicativo.

Outros diretórios de idioma contêm um **Localizable.strings** arquivo para quaisquer recursos de cadeia de caracteres referenciada no código, bem como a **MainStoryboard.strings** arquivo que contém traduções para texto no Storyboard.

![](images/solution-storyboard.png "Árvore de recursos")

Os diretórios de idioma devem conter uma cópia de imagens que foram localizados, para substituir aquele presente no **Base.lproj**.

### <a name="object-id--localization-id"></a>ID de objeto / ID de localização

Durante a criação e edição de controles em um storyboard, selecione cada controle e verifique a ID a ser usado para localização:

* no Visual Studio para Mac, ele tem localizado no painel de propriedades e chamada **identificação da localização**.
* no Xcode, ele é chamado **ID de objeto**.

É um valor de cadeia de caracteres que geralmente tem um formato como **"NF3-h8-xmR"**:

![](images/xs-designer-localization-id.png "Exibição do Xcode de localização de Storyboard")

Esse valor é usado no **.strings** arquivo para atribuir o texto traduzido automaticamente a cada controle.

### <a name="mainstoryboardstrings"></a>MainStoryboard.strings

O formato do arquivo de tradução de storyboard é semelhante do **Localizable.strings** de arquivos, exceto que a chave (o valor à esquerda) não pode ser definido pelo usuário, mas em vez disso, deve ter um formato muito específico: `ObjectID.property`.

O exemplo **Mainstoryboard.strings** abaixo você pode ver `UITextField`s têm uma `placeholder` propriedade de texto que pode ser localizada; `UILabel`s têm uma `text` propriedade; e `UIButton`texto padrão de s é definido usando `normalTitle`:

```csharp
"SXg-TT-IwM.placeholder" = "nombre de la tarea";
"Pqa-aa-ury.placeholder"= "otra información de tarea";
"zwR-D9-hM1.text" = "Detalles de la tarea";
"bAM-2j-Rzw.text" = "Notas";           /* Notes */
"NF3-h8-xmR.text" = "Completo";        /* Done */
"MWt-Ya-pMf.normalTitle" = "Guardar";  /* Save */
"IGr-pR-05L.normalTitle" = "Eliminar"; /* Delete */
```

> [!IMPORTANT]
> **Usar um storyboard com Classes de tamanho** pode resultar nas traduções não aparecem. Isso provavelmente está relacionado ao [esse problema](http://stackoverflow.com/questions/24989208/xcode-6-does-not-localize-interface-builder) em lê a documentação da Apple: localização um storyboard ou XIB será não localizar corretamente se todas as três condições a seguir forem verdadeiras: O storyboard ou XIB usa as classes de tamanho. A localização de base e o destino de compilação são definidos como Universal. Os destinos de compilação iOS 7.0.
A correção é duplicar o arquivo de cadeias de caracteres de storyboard em dois arquivos idênticos: **MainStoryboard~iphone.strings** e **MainStoryboard~ipad.strings**:

> ![](images/xs-dup-strings.png "Arquivos de cadeias de caracteres")


<!--
# Native Formatting

Xamarin.iOS applications can take advantage of the .NET framework's formatting options for localizing
numbers and dates.

### Date string formatting

https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/DataFormatting/Articles/dfDateFormatting10_4.html#//apple_ref/doc/uid/TP40002369-SW1


NSDateFormatter formatter = new NSDateFormatter ();
formatter.DateFormat = "MMMM/dd/yyyy";
NSString dateString = new NSString (formatter.ToString (d));


### Number formatting

https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/DataFormatting/Articles/dfNumberFormatting10_4.html#//apple_ref/doc/uid/TP40002368-SW1

-->

<a name="appstore" />

## <a name="app-store-listing"></a>Listagem de loja de aplicativos

Segue FAQ da Apple [localização do repositório de aplicativo](https://itunespartner.apple.com/en/apps/faq/App%20Store_Localization) Inserir traduções para cada país em que seu aplicativo é de venda. Observe o aviso de que as traduções só aparecerá se seu aplicativo também contém um **.lproj** diretório para o idioma.

<!--

Once you’ve entered your application into iTunes Connect the default language
metadata and screenshots will appear as shown:

![]( "itunes connect 1")

Use the language list on the right to select other languages to provide
translated application name, description, search keywords, URLs and screenshots.
The complete list of languages is shown in this screenshot:

![]( "itunes connect 2")
-->

## <a name="summary"></a>Resumo

Este artigo aborda os conceitos básicos de localização de aplicativos do iOS usando os recursos de storyboard e manipulação de recursos internos.

Você pode aprender mais sobre i18n e L10n para aplicativos de plataforma cruzada, iOS e Android (incluindo xamarin. Forms) [este guia de plataforma cruzada](~/cross-platform/app-fundamentals/localization.md).


## <a name="related-links"></a>Links relacionados

- [Tasky (localizado no código) (amostra)](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n)
- [Tasky (localizada storyboard) (amostra)](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10nStoryboard)
- [Guia de localização da Apple](https://developer.apple.com/library/ios/documentation/MacOSX/Conceptual/BPInternational/InternationalizingYourUserInterface/InternationalizingYourUserInterface.html)
- [Visão geral de localização de plataforma cruzada](~/cross-platform/app-fundamentals/localization.md)
- [Localização do xamarin. Forms](~/xamarin-forms/app-fundamentals/localization.md)
- [Localização do Android](~/android/app-fundamentals/localization.md)
