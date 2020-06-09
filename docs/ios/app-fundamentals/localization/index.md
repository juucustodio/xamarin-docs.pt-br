---
title: Localização no Xamarin. iOS
description: Este documento descreve os recursos de localização do iOS e como usar esses recursos em aplicativos Xamarin. iOS. Ele aborda linguagem, localidade, arquivos de cadeias de caracteres, imagens de inicialização e muito mais.
ms.prod: xamarin
ms.assetid: DFD9EB4A-E536-18E4-C8FD-679BA9C836D8
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 04/28/2017
ms.openlocfilehash: c42b41f9b853fba58ef70b8bd2f8ab20a3369647
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84569227"
---
# <a name="localization-in-xamarinios"></a>Localização no Xamarin. iOS

_Este documento aborda os recursos de localização do SDK do iOS e como acessá-los com o Xamarin._

Consulte as [codificações de internacionalização](encodings.md) para obter instruções sobre como incluir conjuntos de caracteres/páginas de código em aplicativos que devem processar dados não Unicode.

## <a name="ios-platform-features"></a>recursos da plataforma iOS

Esta seção descreve alguns dos recursos de localização no iOS. Pule para a [próxima seção](#localization-basics-in-ios) para ver o código e os exemplos específicos.

### <a name="language"></a>Idioma

Os usuários escolhem seu idioma no aplicativo **configurações** . Essa configuração afeta as cadeias de caracteres de idioma e as imagens exibidas pelo sistema operacional e em aplicativos.

Para determinar o idioma que está sendo usado em um aplicativo, obtenha o primeiro elemento de `NSBundle.MainBundle.PreferredLocalizations` :

```csharp
var lang = NSBundle.MainBundle.PreferredLocalizations[0];
```

Esse valor será um código de idioma, como `en` para inglês, `es` para espanhol, `ja` para japonês, etc. O valor retornado é restrito a uma das localizações com suporte pelo aplicativo (usando regras de fallback para determinar a melhor correspondência).

O código do aplicativo nem sempre precisa verificar esse valor – o Xamarin e o iOS fornecem recursos que ajudam a fornecer automaticamente a cadeia de caracteres ou o recurso correto para o idioma do usuário. Esses recursos são descritos no restante deste documento.

> [!NOTE]
> Use `NSLocale.PreferredLanguages` para determinar as preferências de idioma do usuário, independentemente das localizações com suporte do aplicativo. Os valores retornados por esse método foram alterados no iOS 9; consulte a [Nota técnica TN2418](https://developer.apple.com/library/content/technotes/tn2418/_index.html) para obter detalhes.

### <a name="locale"></a>Localidade

Os usuários escolhem sua localidade no aplicativo **configurações** . Essa configuração afeta a forma como as datas, as horas, os números e a moeda são formatados.

Isso permite que os usuários escolham se verão formatos de 12 horas ou de 24 horas, se o seu separador decimal for uma vírgula ou um ponto, e a ordem de dia, mês e ano na exibição de data.

Com o Xamarin, você tem acesso às classes iOS da Apple ( `NSNumberFormatter` ), bem como às classes .net em System. Globalization. Os desenvolvedores devem avaliar o que é mais adequado às suas necessidades, pois há diferentes recursos disponíveis em cada um. Em particular, se você estiver recuperando e exibindo os preços de compra no aplicativo usando o StoreKit, deverá usar as classes de formatação da Apple para obter as informações de preço retornadas.

A localidade atual pode ser consultada de uma das duas maneiras:

- `NSLocale.CurrentLocale.LocaleIdentifier`
- `NSLocale.AutoUpdatingCurrentLocale.LocaleIdentifier`

O primeiro valor pode ser armazenado em cache pelo sistema operacional e, portanto, talvez nem sempre reflita a localidade atualmente selecionada do usuário. Use o segundo valor para obter a localidade selecionada no momento.

> [!NOTE]
> Mono (o tempo de execução do .NET no qual o Xamarin. iOS é baseado) e as APIs do iOS da Apple não dão suporte a conjuntos idênticos de combinações de idiomas/regiões.
> Por isso, é possível selecionar uma combinação de idioma/região no aplicativo de **configurações** do IOS que não é mapeado para um valor válido no mono. Por exemplo, definir o idioma de um iPhone como Inglês e sua região para a Espanha fará com que as seguintes APIs produzam valores diferentes:
>
> - `CurrentThead.CurrentCulture`: en-US (API mono)
> - `CurrentThread.CurrentUICulture`: en-US (API mono)
> - `NSLocale.CurrentLocale.LocaleIdentifier`: en_ES (API da Apple)
>
> Como o mono usa `CurrentThread.CurrentUICulture` para selecionar recursos e `CurrentThread.CurrentCulture` formatar datas e moedas, a localização baseada em mono (por exemplo, com arquivos. resx) pode não produzir resultados esperados para essas combinações de idioma/região. Nessas situações, dependem das APIs da Apple para localizar, conforme necessário.

### <a name="nscurrentlocaledidchangenotification"></a>NSCurrentLocaleDidChangeNotification

o iOS gera um `NSCurrentLocaleDidChangeNotification` quando o usuário atualiza sua localidade. Os aplicativos podem escutar essa notificação enquanto estão em execução e podem fazer as alterações apropriadas na interface do usuário.

## <a name="localization-basics-in-ios"></a>Noções básicas de localização no iOS

Os seguintes recursos do iOS são facilmente aproveitados no Xamarin para fornecer recursos localizados para exibição para o usuário. Consulte o [exemplo de TaskyL10n](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n) para ver como implementar essas ideias.

### <a name="specifying-default-and-supported-languages-in-infoplist"></a>Especificando idiomas padrão e com suporte no info. plist

Em [Q Technical&um QA1828: como o Ios determina o idioma do seu aplicativo](https://developer.apple.com/library/content/qa/qa1828/_index.html), a Apple descreve como o Ios seleciona um idioma para usar em um aplicativo. Os fatores a seguir afetam qual idioma é exibido:

- Os idiomas preferenciais do usuário (encontrados no aplicativo de **configurações** )
- As localizações agrupadas com o aplicativo (pastas. lproj)
- `CFBundleDevelopmentRegion`(Valor**info. plist** especificando o idioma padrão para o aplicativo)
- `CFBundleLocalizations`(Matriz**info. plist** especificando todas as localizações com suporte)

Conforme indicado no Q técnico&A, `CFBundleDevelopmentRegion` representa a região e o idioma padrão de um aplicativo. Se o aplicativo não der suporte explicitamente a qualquer um dos idiomas preferenciais de um usuário, ele usará o idioma especificado por esse campo.

> [!IMPORTANT]
> o iOS 11 aplica esse mecanismo de seleção de idioma mais estritamente do que as versões anteriores do sistema operacional. Por isso, qualquer aplicativo iOS 11 que não declare explicitamente suas localizações com suporte – seja incluindo pastas. lproj ou definindo um valor para `CFBundleLocalizations` – pode exibir um idioma diferente no Ios 11 do que no Ios 10.

Se `CFBundleDevelopmentRegion` não tiver sido especificado no arquivo **info. plist** , as ferramentas de Build do Xamarin. Ios usarão atualmente um valor padrão de `en_US` . Embora isso possa ser alterado em uma versão futura, isso significa que o idioma padrão é inglês.

Para garantir que seu aplicativo selecione um idioma esperado, execute as seguintes etapas:

- Especifique um idioma padrão. Abra o **info. plist** e use a exibição de **código-fonte** para definir um valor para a `CFBundleDevelopmentRegion` chave; em XML, ele deve ser semelhante ao seguinte:

```xml
<key>CFBundleDevelopmentRegion</key>
<string>es</string>
```

Este exemplo usa "es" para especificar que, quando nenhum dos idiomas preferenciais de um usuário tiver suporte, o padrão será espanhol.

- Declare todas as localizações com suporte. No **info. plist**, use a exibição de **origem** para definir uma matriz para a `CFBundleLocalizations` chave; em XML, ela deve ser semelhante ao seguinte:

```xml
<key>CFBundleLocalizations</key>
<array>
    <string>en</string>
    <string>es</string>
    ...
</array>
```

Os aplicativos Xamarin. iOS que foram localizados usando mecanismos .NET como arquivos. resx também devem fornecer esses valores **info. plist** .

Para obter mais informações sobre essas chaves **info. plist** , dê uma olhada na [referência da chave da lista de propriedades de informações](https://developer.apple.com/library/content/documentation/General/Reference/InfoPlistKeyReference/Articles/CoreFoundationKeys.html)da Apple.

### <a name="getlocalizedstring-method"></a>Método gettraduzistring

O `NSBundle.MainBundle.GetLocalizedString` método pesquisa o texto localizado que foi armazenado em arquivos **. Strings** no projeto. Esses arquivos são organizados por idioma, em diretórios especialmente nomeados com um sufixo **. lproj** (Observe que a primeira letra da extensão é um "L" minúsculo).

#### <a name="strings-file-locations"></a>. locais de arquivo de cadeias de caracteres

- **Base. lproj** é o diretório que contém recursos para o idioma padrão.
  Geralmente, ele está localizado na raiz do projeto (mas também pode ser colocado na pasta de **recursos** ).
- os diretórios ** &lt; &gt; . lproj de idioma** são criados para cada idioma com suporte, geralmente na pasta de **recursos** .

Pode haver vários arquivos **. Strings** diferentes em cada diretório de idioma:

- **Inlocalizáveis. Strings** – a lista principal de texto localizado.
- **InfoPlist. Strings** – determinadas chaves específicas são permitidas neste arquivo para converter itens como o nome do aplicativo.
- ** \<storyboard-name> . Strings** – arquivo opcional que contém traduções para elementos da interface do usuário em um Storyboard.

A **ação de Build** para esses arquivos deve ser **recurso de pacote**.

#### <a name="strings-file-format"></a>formato de arquivo. Strings

A sintaxe para valores de cadeia de caracteres localizados é:

```console
/* comment */
"key"="localized-value";
```

Você deve escapar os seguintes caracteres em Strings:

- `\"`citar
- `\\`barra invertida
- `\n`separados

Este é um exemplo de **es/localizáveis. Strings** (isto é, Espanhol) do exemplo:

```console
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

2. Coloque o arquivo de imagem padrão **Flag. png** em **base. lproj** (o diretório de linguagem de desenvolvimento nativo).

3. Opcionalmente, coloque as versões localizadas da imagem em pastas **. lproj** para cada idioma (por exemplo, **es. lproj**, **ja. lproj**). Use o mesmo sinalizador de nome de arquivo **. png** em cada diretório de idioma.

Se uma imagem não estiver presente para um idioma específico, o iOS fará fallback para a pasta padrão de idioma nativo e carregará a imagem a partir daí.

#### <a name="launch-images"></a>Imagens de inicialização

Use as convenções de nomenclatura padrão para as imagens de inicialização (e o XIB ou Storyboard para modelos do iPhone 6) ao colocá-las nos diretórios **. lproj** para cada idioma.

```console
Default.png
Default@2x.png
Default-568h@2x.png
LaunchScreen.xib
```

### <a name="app-name"></a>Nome do aplicativo

Colocar um arquivo **InfoPlist. Strings** em um diretório **. lproj** permite que você substitua alguns valores do **info. plist**do aplicativo, incluindo o nome do aplicativo:

```console
"CFBundleDisplayName" = "LeónTodo";
```

Outras chaves que você pode usar para [Localizar cadeias de caracteres específicas do aplicativo](https://developer.apple.com/library/ios/documentation/MacOSX/Conceptual/BPInternational/LocalizingYourApp/LocalizingYourApp.html#//apple_ref/doc/uid/10000171i-CH5-SW21) são:

- CFBundleName
- CFBundleShortVersionString
- NSHumanReadableCopyright

### <a name="dates-and-times"></a>Datas e horas

Embora seja possível usar as funções internas de data e hora do .NET (junto com a atual `CultureInfo` ) para formatar datas e horários para uma localidade, isso ignorará as configurações de usuário específicas de localidade (que podem ser definidas separadamente do idioma).

Use o iOS `NSDateFormatter` para produzir a saída que corresponde à preferência de localidade do usuário. O código de exemplo a seguir demonstra as opções básicas de formatação de data e hora:

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

Resultados para inglês no Estados Unidos:

```console
Full,Long: Friday, August 7, 2015 at 10:29:32 AM PDT
Short,Short: 8/7/15, 10:29 AM
Medium,None: Aug 7, 2015
```

Resultados para espanhol na Espanha:

```console
Full,Long: viernes, 7 de agosto de 2015, 10:26:58 GMT-7
Short,Short: 7/8/15 10:26
Medium,None: 7/8/2015
```

Consulte a documentação de [formatadores de data](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/DataFormatting/Articles/dfDateFormatting10_4.html) da Apple para obter mais informações. Ao testar a formatação de data e hora sensível à localidade, verifique as configurações de idioma e **região** do **iPhone** .

<a name="rtl"></a>

### <a name="right-to-left-rtl-layout"></a>Layout da direita para a esquerda (RTL)

o iOS fornece uma série de recursos para ajudar na criação de aplicativos com reconhecimento de RTL:

- Use layouts automáticos `leading` e `trailing` atributos para alinhamento de controle (que corresponde à esquerda e à direita para inglês, mas é invertido para idiomas RTL).
  O [`UIStackView`](~/ios/user-interface/controls/uistackview.md) controle é particularmente útil para dispor controles que reconhecem a RTL.
- Use `TextAlignment = UITextAlignment.Natural` para alinhamento de texto (que será deixado para a maioria dos idiomas, mas adequado para RTL).
- `UINavigationController`inverte automaticamente o botão voltar e reverte a direção do dedo.

As capturas de tela a seguir mostram o [exemplo de tarefa localizada](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n) em árabe e Hebraico (embora o inglês tenha sido inserido nos campos):

[![](images/rtl-ar-sml.png "Localization in Arabic")](images/rtl-ar.png#lightbox "Arabic")

[![](images/rtl-he-sml.png "Localization in Hebrew")](images/rtl-he.png#lightbox "Hebrew")

o iOS reverte automaticamente o `UINavigationController` , e os outros controles são colocados dentro `UIStackView` ou alinhados com o layout automático.
O texto RTL é localizado usando arquivos **. Strings** da mesma forma que o texto ltr.

<a name="code"></a>

## <a name="localizing-the-ui-in-code"></a>Localizando a interface do usuário no código

O exemplo de [tarefa (localizada no código)](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n) mostra como localizar um aplicativo no qual a interface do usuário é criada no código (em vez de XIBs ou storyboards).

### <a name="project-structure"></a>Estrutura do Projeto

![](images/solution-code.png "Resources tree")

### <a name="localizablestrings-file"></a>Arquivo. Strings localizável

Conforme descrito acima, o formato de arquivo **localizável. Strings** consiste em pares chave-valor. A chave descreve a intenção da cadeia de caracteres e o valor é o texto traduzido a ser usado no aplicativo.

As localizações do espanhol (**es**) para o exemplo são mostradas abaixo:

```console
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

No código do aplicativo, sempre que o texto de exibição de uma interface do usuário é definido (seja o texto de um rótulo ou o espaço reservado de uma entrada, etc), o código usa a `GetLocalizedString` função Ios para recuperar a tradução correta a ser exibida:

```csharp
var localizedString = NSBundle.MainBundle.GetLocalizedString ("key", "optional");
someControl.Text = localizedString;
```

<a name="storyboard"></a>

## <a name="localizing-storyboard-uis"></a>Localizando as UIs do storyboard

A tarefa de exemplo [(storyboard localizado)](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10nStoryboard) mostra como localizar texto em controles em um Storyboard.

### <a name="project-structure"></a>Estrutura do projeto

O diretório **base. lproj** contém o storyboard e também deve conter todas as imagens usadas no aplicativo.

Os outros diretórios de linguagem contêm um arquivo **localizável. Strings** para qualquer recurso de cadeia de caracteres referenciado no código, bem como um arquivo **MainStoryboard. String** que contém traduções para texto no storyboard.

![](images/solution-storyboard.png "Resources tree")

Os diretórios de idioma devem conter uma cópia de todas as imagens que foram localizadas, para substituir aquela presente em **base. lproj**.

### <a name="object-id--localization-id"></a>ID do objeto/ID da localização

Ao criar e editar controles em um storyboard, selecione cada controle e verifique a ID a ser usada para localização:

- No Visual Studio para Mac, ele está localizado na **painel de propriedades** e é chamado de **ID de localização**.
- No Xcode, ele é chamado de **ID de objeto**.

Esse valor de cadeia de caracteres geralmente tem um formato como "NF3-H8-xmR", conforme mostrado na seguinte captura de tela:

![](images/xs-designer-localization-id.png "Xcode view of Storyboard localization")

Esse valor é usado no arquivo **. Strings** para atribuir texto traduzido automaticamente a cada controle.

### <a name="mainstoryboardstrings"></a>MainStoryboard. Strings

O formato do arquivo de tradução do storyboard é semelhante ao arquivo **localizável. Strings** , exceto que a chave (o valor à esquerda) não pode ser definida pelo usuário, mas em vez disso, deve ter um formato muito específico: `ObjectID.property` .

No exemplo **Mainstoryboard. Strings** abaixo, você pode ver `UITextField` que s têm uma `placeholder` propriedade Text que pode ser localizada; `UILabel` s têm uma `text` propriedade; e o `UIButton` texto padrão é definido usando `normalTitle` :

```console
"SXg-TT-IwM.placeholder" = "nombre de la tarea";
"Pqa-aa-ury.placeholder"= "otra información de tarea";
"zwR-D9-hM1.text" = "Detalles de la tarea";
"bAM-2j-Rzw.text" = "Notas";           /* Notes */
"NF3-h8-xmR.text" = "Completo";        /* Done */
"MWt-Ya-pMf.normalTitle" = "Guardar";  /* Save */
"IGr-pR-05L.normalTitle" = "Eliminar"; /* Delete */
```

> [!IMPORTANT]
> Usar um storyboard com classes de tamanho pode resultar em traduções que não aparecem no aplicativo. As [notas de versão do Xcode da Apple](https://developer.apple.com/library/content/releasenotes/DeveloperTools/RN-Xcode/Chapters/Introduction.html) indicam que um storyboard ou XIB não será localizado corretamente se três coisas forem verdadeiras: ele usa classes de tamanho, a localização de base e o destino da compilação são definidos como universal, e a compilação se destina a Ios 7,0. A correção é duplicar o arquivo de cadeias de caracteres de storyboard em dois arquivos idênticos: **MainStoryboard ~ iPhone. Strings** e **MainStoryboard ~ iPad. Strings**, conforme mostrado na seguinte captura de tela:
>
> ![](images/xs-dup-strings.png "Strings files")

<a name="appstore"></a>

## <a name="app-store-listing"></a>Listagem da loja de aplicativos

Segue as perguntas frequentes da Apple sobre a [localização da loja de aplicativos](https://itunespartner.apple.com/en/apps/faq/App%20Store_Localization) para inserir traduções para cada país em que seu aplicativo está em venda. Observe seu aviso de que as traduções só serão exibidas se o aplicativo também contiver um diretório **. lproj** localizado para o idioma.

## <a name="summary"></a>Resumo

Este artigo aborda as noções básicas de localização de aplicativos iOS usando os recursos internos de controle de recursos e storyboard.

Você pode aprender mais sobre o i18n e o l10n para aplicativos iOS, Android e multiplataforma (incluindo Xamarin. Forms) neste [Guia de plataforma cruzada](~/cross-platform/app-fundamentals/localization.md).

## <a name="related-links"></a>Links relacionados

- [Tarefa (localizada no código) (exemplo)](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n)
- [Tarefa (storyboard localizado) (exemplo)](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10nStoryboard)
- [Guia de localização da Apple](https://developer.apple.com/library/ios/documentation/MacOSX/Conceptual/BPInternational/InternationalizingYourUserInterface/InternationalizingYourUserInterface.html)
- [Visão geral da localização entre plataformas](~/cross-platform/app-fundamentals/localization.md)
- [Localização do Xamarin.Forms](~/xamarin-forms/app-fundamentals/localization/index.md)
- [Localização no Android](~/android/app-fundamentals/localization.md)
