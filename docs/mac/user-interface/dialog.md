---
title: "Caixas de diálogo"
description: "Este artigo aborda a trabalhar com caixas de diálogo e janelas modais em um aplicativo Xamarin.Mac. Ele descreve como criar janelas modais no construtor de Interface e Xcode, trabalhando com caixas de diálogo padrão e interagir com esses controles em código c#."
ms.topic: article
ms.prod: xamarin
ms.assetid: 55451990-B77B-4D44-B8BB-F874EC503B0C
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: 661874ae8599fed35ce10213fece383eb81de94d
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2018
---
# <a name="dialogs"></a>Caixas de diálogo

Ao trabalhar com c# e .NET em um aplicativo de Xamarin.Mac, você tem acesso a caixas de diálogo e janelas modais mesmo que um desenvolvedor trabalhando em *Objective-C* e *Xcode* does. Como Xamarin.Mac se integra diretamente com o Xcode, você pode usar do Xcode _Interface construtor_ para criar e manter as janelas modais (ou, opcionalmente, criá-los diretamente no código do c#).

Uma caixa de diálogo aparece em resposta a uma ação do usuário e geralmente fornece aos usuários de maneiras podem concluir a ação. Uma caixa de diálogo requer uma resposta do usuário antes de ser encerrado.

Windows pode ser usado em um estado sem janela restrita (como um editor de texto que pode ter vários documentos abertos simultaneamente) ou restrita (como uma caixa de diálogo de exportação que deve ser descartada antes que o aplicativo pode continuar).

[![](dialog-images/dialog03.png "Uma caixa de diálogo Abrir")](dialog-images/dialog03.png#lightbox)

Neste artigo, vamos abordar os fundamentos de trabalhar com caixas de diálogo e janelas modais em um aplicativo Xamarin.Mac. É altamente recomendável que você leia o [Hello, Mac](~/mac/get-started/hello-mac.md) artigo primeiro, especificamente o [Introdução ao construtor da Interface e Xcode](~/mac/get-started/hello-mac.md#Introduction_to_Xcode_and_Interface_Builder) e [tomadas e ações](~/mac/get-started/hello-mac.md#Outlets_and_Actions) seções, como ele aborda os principais conceitos e técnicas que será usado neste artigo.

Talvez você queira dar uma olhada a [classes expondo c# / métodos para Objective-C](~/mac/internals/how-it-works.md) seção o [Xamarin.Mac internos](~/mac/internals/how-it-works.md) de documento, ele explica o `Register` e `Export` comandos usado com a transmissão suas classes c# objetos Objective-C e elementos de interface do usuário.

<a name="Introduction_to_Dialogs" />

## <a name="introduction-to-dialogs"></a>Introdução às caixas de diálogo

Uma caixa de diálogo aparece em resposta a uma ação do usuário (como salvar um arquivo) e fornece uma maneira para os usuários concluir essa ação. Uma caixa de diálogo requer uma resposta do usuário antes de ser encerrado.

De acordo com a Apple, há três maneiras de apresentar uma caixa de diálogo:

- **Documento Modal** -caixa de diálogo Modal de um documento impede que o usuário fazer tudo dentro de um determinado documento até que seja descartada.
- **Aplicativo Modal** - uma caixa de diálogo Modal impede que o usuário interagir com o aplicativo até que seja descartada do aplicativo.
- **Sem janela restrita** um diálogo de sem janela restrita permite aos usuários alterar as configurações na caixa de diálogo enquanto ainda interage com a janela do documento.

### <a name="modal-window"></a>Janela Modal

Qualquer padrão `NSWindow` pode ser usado como uma caixa de diálogo personalizada exibindo modalmente:

[![](dialog-images/modal01.png "Uma janela modal do exemplo")](dialog-images/modal01.png#lightbox)

### <a name="document-modal-dialog-sheets"></a>Folhas de caixa de diálogo Modal do documento

Um _folha_ é uma caixa de diálogo modal está anexada a uma janela de documento específico, impedindo que os usuários interajam com a janela até que eles ignorar a caixa de diálogo. Uma folha é anexada à janela do qual ele emerge e apenas uma planilha é possível abrir uma janela para a qualquer momento.

[![](dialog-images/sheet08.png "Uma planilha de exemplo modal")](dialog-images/sheet08.png#lightbox)

### <a name="preferences-windows"></a>Preferências Windows

Uma janela de preferências é uma caixa de diálogo sem janela restrita que contém as configurações do aplicativo que o usuário raramente são alterados. Preferências do Windows geralmente incluem uma barra de ferramentas que permite ao usuário alternar entre os diferentes grupos de configurações:

[![](dialog-images/dialog02.png "Uma janela de preferência de exemplo")](dialog-images/dialog02.png#lightbox)

### <a name="open-dialog"></a>Caixa de diálogo Abrir

A caixa de diálogo Abrir fornece aos usuários uma maneira consistente para localizar e abrir um item em um aplicativo:

[![](dialog-images/dialog03.png "Uma caixa de diálogo Abrir")](dialog-images/dialog03.png#lightbox)


### <a name="print-and-page-setup-dialogs"></a>Impressão e caixas de diálogo Configurar página

macOS fornece impressão padrão e experiência de página Instalação caixas de diálogo que seu aplicativo pode exibir para que os usuários podem ter uma impressão consistente em todos os aplicativos que eles usam.

A caixa de diálogo de impressão podem ser exibida como os dois uma caixa de diálogo flutuante livre:

[![](dialog-images/print01.png "Uma caixa de diálogo de impressão")](dialog-images/print01.png#lightbox)

Ou pode ser exibido como uma folha:

[![](dialog-images/print02.png "Uma folha de impressão")](dialog-images/print02.png#lightbox)

A caixa de diálogo de configuração de página podem ser exibida como os dois uma caixa de diálogo flutuante livre:

[![](dialog-images/print03.png "Uma caixa de diálogo de configuração de página")](dialog-images/print03.png#lightbox)

Ou pode ser exibido como uma folha:

[![](dialog-images/print04.png "Uma folha de configuração de página")](dialog-images/print04.png#lightbox)

### <a name="save-dialogs"></a>Caixas de diálogo de salvar

A caixa de diálogo Salvar fornece aos usuários uma maneira consistente para salvar um item em um aplicativo. A caixa de diálogo Salvar tem dois estados: **mínimo** (também conhecido como recolhida):

[![](dialog-images/save01.png "Um diálogo Salvar")](dialog-images/save01.png#lightbox)

E o **expandido** estado:

[![](dialog-images/save02.png "Caixa de diálogo Salvar um expandido")](dialog-images/save02.png#lightbox)

O **mínimo** também pode ser exibida a caixa de diálogo Salvar como uma folha:

[![](dialog-images/save03.png "Mínimo salvar folha")](dialog-images/save03.png#lightbox)

Como o **expandido** caixa de diálogo Salvar:

[![](dialog-images/save04.png "Salvar um expandido folha")](dialog-images/save04.png#lightbox)

Para obter mais informações, consulte o [caixas de diálogo](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/WindowDialogs.html#//apple_ref/doc/uid/20000957-CH43-SW1) seção da Apple [diretrizes de Interface Humana OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)

<a name="Adding_a_Modal_Window_to_a_Project" />

## <a name="adding-a-modal-window-to-a-project"></a>Adicionando uma janela restrita a um projeto

Além da janela do documento principal, talvez seja necessário um aplicativo Xamarin.Mac exibir outros tipos de windows para o usuário, como preferências ou painéis do Inspetor.

Para adicionar uma nova janela, faça o seguinte:

1. No **Solution Explorer**, abra o `Main.storyboard` arquivo para edição no construtor de Interface do Xcode.
2. Arraste um novo **View Controller** na superfície de Design:

    [![](dialog-images/new01.png "Selecionar um controlador de exibição da biblioteca")](dialog-images/new01.png#lightbox)
3. No **Inspetor de identidade**, digite `CustomDialogController` para o **nome da classe**: 

    [![](dialog-images/new02.png "Configurar o nome de classe")](dialog-images/new02.png#lightbox)
4. Alterne para o Visual Studio para Mac, permitir a sincronização com o Xcode e crie o `CustomDialogController.h` arquivo.
5. Volte para Xcode e sua interface de design: 

    [![](dialog-images/new03.png "Criando a interface do usuário no Xcode")](dialog-images/new03.png#lightbox)
6. Criar um **atender Modal** da janela principal do seu aplicativo para o novo controlador de exibição, arrastando o controle do elemento de interface do usuário que será aberta a caixa de diálogo janela da caixa de diálogo. Atribuir o **identificador** `ModalSegue`: 

    [![](dialog-images/new06.png "Segue uma janela restrita")](dialog-images/new06.png#lightbox)
6. A transmissão qualquer **ações** e **tomadas**: 

    [![](dialog-images/new04.png "Configurar uma ação")](dialog-images/new04.png#lightbox)
6. Salve suas alterações e retornar ao Visual Studio para Mac sincronizar com o Xcode.

Verifique o `CustomDialogController.cs` arquivo aparência semelhante ao seguinte:

```csharp
using System;
using Foundation;
using AppKit;

namespace MacDialog
{
    public partial class CustomDialogController : NSViewController
    {
        #region Private Variables
        private string _dialogTitle = "Title";
        private string _dialogDescription = "Description";
        private NSViewController _presentor;
        #endregion

        #region Computed Properties
        public string DialogTitle {
            get { return _dialogTitle; }
            set { _dialogTitle = value; }
        }

        public string DialogDescription {
            get { return _dialogDescription; }
            set { _dialogDescription = value; }
        }

        public NSViewController Presentor {
            get { return _presentor; }
            set { _presentor = value; }
        }
        #endregion

        #region Constructors
        public CustomDialogController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void ViewWillAppear ()
        {
            base.ViewWillAppear ();

            // Set initial title and description
            Title.StringValue = DialogTitle;
            Description.StringValue = DialogDescription;
        }
        #endregion

        #region Private Methods
        private void CloseDialog() {
            Presentor.DismissViewController (this);
        }
        #endregion

        #region Custom Actions
        partial void AcceptDialog (Foundation.NSObject sender) {
            RaiseDialogAccepted();
            CloseDialog();
        }

        partial void CancelDialog (Foundation.NSObject sender) {
            RaiseDialogCanceled();
            CloseDialog();
        }
        #endregion

        #region Events
        public EventHandler DialogAccepted;

        internal void RaiseDialogAccepted() {
            if (this.DialogAccepted != null)
                this.DialogAccepted (this, EventArgs.Empty);
        }

        public EventHandler DialogCanceled;

        internal void RaiseDialogCanceled() {
            if (this.DialogCanceled != null)
                this.DialogCanceled (this, EventArgs.Empty);
        }
        #endregion
    }
}
```

Este código expõe algumas propriedades para definir o título e a descrição da caixa de diálogo e alguns eventos de reagir a caixa de diálogo que está sendo cancelada ou aceita.

Em seguida, edite o `ViewController.cs` de arquivo, substitua o `PrepareForSegue` método e torná-lo a aparência a seguir:

```csharp
public override void PrepareForSegue (NSStoryboardSegue segue, NSObject sender)
{
    base.PrepareForSegue (segue, sender);

    // Take action based on the segue name
    switch (segue.Identifier) {
    case "ModalSegue":
        var dialog = segue.DestinationController as CustomDialogController;
        dialog.DialogTitle = "MacDialog";
        dialog.DialogDescription = "This is a sample dialog.";
        dialog.DialogAccepted += (s, e) => {
            Console.WriteLine ("Dialog accepted");
            DismissViewController (dialog);
        };
        dialog.Presentor = this;
        break;
    }
}
```

Esse código inicializa o segue que definimos no construtor de Interface do Xcode à nossa caixa de diálogo e define o título e descrição. Ele também lida com a opção que faz com que o usuário na caixa de diálogo.

Podemos executar o nosso aplicativo e exibir a caixa de diálogo personalizada:

[![](dialog-images/new05.png "Uma caixa de diálogo de exemplo")](dialog-images/new05.png#lightbox)

Para obter mais informações sobre como usar o windows em um aplicativo de Xamarin.Mac, consulte nosso [trabalhando com o Windows](~/mac/user-interface/window.md) documentação.

<a name="Creating_a_Custom_Sheet" />

## <a name="creating-a-custom-sheet"></a>Criando uma folha personalizada

Um _folha_ é uma caixa de diálogo modal está anexada a uma janela de documento específico, impedindo que os usuários interajam com a janela até que eles ignorar a caixa de diálogo. Uma folha é anexada à janela do qual ele emerge e apenas uma planilha é possível abrir uma janela para a qualquer momento. 

Para criar uma planilha personalizada no Xamarin.Mac, vamos fazer o seguinte:

1. No **Solution Explorer**, abra o `Main.storyboard` arquivo para edição no construtor de Interface do Xcode.
2. Arraste um novo **View Controller** na superfície de Design:

    [![](dialog-images/new01.png "Selecionar um controlador de exibição da biblioteca")](dialog-images/new01.png#lightbox)
2. Crie sua interface do usuário:

    [![](dialog-images/sheet01.png "O design de interface do usuário")](dialog-images/sheet01.png#lightbox)
3. Criar um **folha atender** de sua janela principal para o novo controlador de exibição: 

    [![](dialog-images/sheet02.png "Selecionar o tipo de segue folha")](dialog-images/sheet02.png#lightbox)
4. No **Inspetor de identidade**, nome do controlador de exibição **classe** `SheetViewController`: 

    [![](dialog-images/sheet03.png "Configurar o nome de classe")](dialog-images/sheet03.png#lightbox)
5. Definir qualquer necessário **tomadas** e **ações**: 

    [![](dialog-images/sheet04.png "Definindo as saídas e as ações necessárias")](dialog-images/sheet04.png#lightbox)
6. Salve suas alterações e retornar ao Visual Studio para Mac sincronizar.

Em seguida, edite o `SheetViewController.cs` de arquivo e torná-lo a aparência a seguir:

```csharp
using System;
using Foundation;
using AppKit;

namespace MacDialog
{
    public partial class SheetViewController : NSViewController
    {
        #region Private Variables
        private string _userName = "";
        private string _password = "";
        private NSViewController _presentor;
        #endregion

        #region Computed Properties
        public string UserName {
            get { return _userName; }
            set { _userName = value; }
        }

        public string Password {
            get { return _password;}
            set { _password = value;}
        }

        public NSViewController Presentor {
            get { return _presentor; }
            set { _presentor = value; }
        }
        #endregion

        #region Constructors
        public SheetViewController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void ViewWillAppear ()
        {
            base.ViewWillAppear ();

            // Set initial values
            NameField.StringValue = UserName;
            PasswordField.StringValue = Password;

            // Wireup events
            NameField.Changed += (sender, e) => {
                UserName = NameField.StringValue;
            };
            PasswordField.Changed += (sender, e) => {
                Password = PasswordField.StringValue;
            };
        }
        #endregion

        #region Private Methods
        private void CloseSheet() {
            Presentor.DismissViewController (this);
        }
        #endregion

        #region Custom Actions
        partial void AcceptSheet (Foundation.NSObject sender) {
            RaiseSheetAccepted();
            CloseSheet();
        }

        partial void CancelSheet (Foundation.NSObject sender) {
            RaiseSheetCanceled();
            CloseSheet();
        }
        #endregion

        #region Events
        public EventHandler SheetAccepted;

        internal void RaiseSheetAccepted() {
            if (this.SheetAccepted != null)
                this.SheetAccepted (this, EventArgs.Empty);
        }

        public EventHandler SheetCanceled;

        internal void RaiseSheetCanceled() {
            if (this.SheetCanceled != null)
                this.SheetCanceled (this, EventArgs.Empty);
        }
        #endregion
    }
}
```

Em seguida, edite o `ViewController.cs` de arquivo, edite o `PrepareForSegue` método e torná-lo a aparência a seguir:

```csharp
public override void PrepareForSegue (NSStoryboardSegue segue, NSObject sender)
{
    base.PrepareForSegue (segue, sender);

    // Take action based on the segue name
    switch (segue.Identifier) {
    case "ModalSegue":
        var dialog = segue.DestinationController as CustomDialogController;
        dialog.DialogTitle = "MacDialog";
        dialog.DialogDescription = "This is a sample dialog.";
        dialog.DialogAccepted += (s, e) => {
            Console.WriteLine ("Dialog accepted");
            DismissViewController (dialog);
        };
        dialog.Presentor = this;
        break;
    case "SheetSegue":
        var sheet = segue.DestinationController as SheetViewController;
        sheet.SheetAccepted += (s, e) => {
            Console.WriteLine ("User Name: {0} Password: {1}", sheet.UserName, sheet.Password);
        };
        sheet.Presentor = this;
        break;
    }
}
```

Se executar o aplicativo e abra a folha, ele será anexado à janela:

[![](dialog-images/sheet08.png "Uma planilha de exemplo")](dialog-images/sheet08.png#lightbox)

<a name="Creating_a_Preferences_Dialog" />

## <a name="creating-a-preferences-dialog"></a>Criar uma caixa de diálogo de preferências

Antes, formatar o modo de exibição de preferência no construtor de Interface, precisaremos adicionar um tipo personalizado segue para lidar com desativando as preferências. Adicionar uma nova classe ao seu projeto e chamá-la `ReplaceViewSeque `. Edite a classe e torná-lo a seguinte aparência:

```csharp
using System;
using AppKit;
using Foundation;

namespace MacWindows
{
    [Register("ReplaceViewSeque")]
    public class ReplaceViewSeque : NSStoryboardSegue
    {
        #region Constructors
        public ReplaceViewSeque() {

        }

        public ReplaceViewSeque (string identifier, NSObject sourceController, NSObject destinationController) : base(identifier,sourceController,destinationController) {

        }

        public ReplaceViewSeque (IntPtr handle) : base(handle) {
        }

        public ReplaceViewSeque (NSObjectFlag x) : base(x) {
        }
        #endregion

        #region Override Methods
        public override void Perform ()
        {
            // Cast the source and destination controllers
            var source = SourceController as NSViewController;
            var destination = DestinationController as NSViewController;

            // Is there a source?
            if (source == null) {
                // No, get the current key window
                var window = NSApplication.SharedApplication.KeyWindow;

                // Swap the controllers
                window.ContentViewController = destination;

                // Release memory
                window.ContentViewController?.RemoveFromParentViewController ();
            } else {
                // Swap the controllers
                source.View.Window.ContentViewController = destination;

                // Release memory
                source.RemoveFromParentViewController ();
            }
        
        }
        #endregion

    }

}
```

Com o segue personalizadas criadas, podemos adicionar uma nova janela no construtor de Interface do Xcode para lidar com nosso preferências.

Para adicionar uma nova janela, faça o seguinte:

1. No **Solution Explorer**, abra o `Main.storyboard` arquivo para edição no construtor de Interface do Xcode.
2. Arraste um novo **janela controlador** na superfície de Design:

    [![](dialog-images/pref01.png "Selecione um controlador de janela da biblioteca")](dialog-images/pref01.png#lightbox)
3. Organizar a janela de perto o **barra de menus** designer:

    [![](dialog-images/pref02.png "Adicionar nova janela")](dialog-images/pref02.png#lightbox)
4. Crie cópias do controlador de exibição conectado como haverá guias no modo de exibição de preferência:

    [![](dialog-images/pref03.png "Adicionando os controladores de exibição necessários")](dialog-images/pref03.png#lightbox)
5. Arraste um novo **controlador da barra de ferramentas** do **biblioteca**:

    [![](dialog-images/pref04.png "Selecione um controlador de barra de ferramentas da biblioteca")](dialog-images/pref04.png#lightbox)
6. E solte-a na janela na superfície de Design:

    [![](dialog-images/pref05.png "Adicionar um novo controlador de barra de ferramentas")](dialog-images/pref05.png#lightbox)
7. O design da sua barra de ferramentas de layout:

    [![](dialog-images/pref06.png "A barra de ferramentas de layout")](dialog-images/pref06.png#lightbox)
8. CTRL + clique e arraste de cada **botão da barra de ferramentas** aos modos de exibição criado acima. Selecione um **personalizado** atender tipo:

    [![](dialog-images/pref07.png "Definição do tipo segue")](dialog-images/pref07.png#lightbox)
9. Selecione o novo Segue e defina o **classe** para `ReplaceViewSegue`:

    [![](dialog-images/pref08.png "Definindo a classe segue")](dialog-images/pref08.png#lightbox)
10. No **Menubar Designer** na superfície de Design, no Menu aplicativo, selecione **preferências...** , control, clique e arraste para a janela de preferências para criar um **Mostrar** atender:

    [![](dialog-images/pref09.png "Definição do tipo segue")](dialog-images/pref09.png#lightbox)
11. Salve suas alterações e retornar ao Visual Studio para Mac sincronizar.

Se executar o código e selecione o **preferências...**  do **aplicativo Menu**, a janela será exibida:

[![](dialog-images/pref10.png "Uma janela de preferências de exemplo")](dialog-images/pref10.png#lightbox)

Para obter mais informações sobre como trabalhar com o Windows e barras de ferramentas, consulte nosso [Windows](~/mac/user-interface/window.md) e [barras de ferramentas](~/mac/user-interface/toolbar.md) documentação.

<a name="Saving-and-Loading-Preferences" />

### <a name="saving-and-loading-preferences"></a>Salvando e Carregando preferências

Em um típico macOS aplicativo, quando o usuário faz alterações em qualquer uma das preferências do usuário do aplicativo, essas alterações serão salvas automaticamente. É a maneira mais fácil de lidar com isso em um aplicativo de Xamarin.Mac criar uma única classe para gerenciar todas as preferências do usuário e compartilhá-lo em todo o sistema.

Primeiro, adicione um novo `AppPreferences` classe ao projeto e herdam `NSObject`. As preferências serão criadas ao usar [associação de dados e a codificação de chave-valor](~/mac/app-fundamentals/databinding.md) tornando o processo de criação e manutenção a preferência de forma muito mais simples. Como as preferências consistem em uma pequena quantidade de tipos de dados simples, use o `NSUserDefaults` para armazenar e recuperar valores.

Editar o `AppPreferences.cs` de arquivo e torná-lo a aparência a seguir:

```csharp
using System;
using Foundation;
using AppKit;

namespace SourceWriter
{
    [Register("AppPreferences")]
    public class AppPreferences : NSObject
    {
        #region Computed Properties
        [Export("DefaultLangauge")]
        public int DefaultLanguage {
            get { 
                var value = LoadInt ("DefaultLangauge", 0);
                return value; 
            }
            set {
                WillChangeValue ("DefaultLangauge");
                SaveInt ("DefaultLangauge", value, true);
                DidChangeValue ("DefaultLangauge");
            }
        }

        [Export("SmartLinks")]
        public bool SmartLinks {
            get { return LoadBool ("SmartLinks", true); }
            set {
                WillChangeValue ("SmartLinks");
                SaveBool ("SmartLinks", value, true);
                DidChangeValue ("SmartLinks");
            }
        }

        // Define any other required user preferences in the same fashion
        ...

        [Export("EditorBackgroundColor")]
        public NSColor EditorBackgroundColor {
            get { return LoadColor("EditorBackgroundColor", NSColor.White); }
            set {
                WillChangeValue ("EditorBackgroundColor");
                SaveColor ("EditorBackgroundColor", value, true);
                DidChangeValue ("EditorBackgroundColor");
            }
        }
        #endregion

        #region Constructors
        public AppPreferences ()
        {
        }
        #endregion

        #region Public Methods
        public int LoadInt(string key, int defaultValue) {
            // Attempt to read int
            var number = NSUserDefaults.StandardUserDefaults.IntForKey(key);

            // Take action based on value
            if (number == null) {
                return defaultValue;
            } else {
                return (int)number;
            }
        }
            
        public void SaveInt(string key, int value, bool sync) {
            NSUserDefaults.StandardUserDefaults.SetInt(value, key);
            if (sync) NSUserDefaults.StandardUserDefaults.Synchronize ();
        }

        public bool LoadBool(string key, bool defaultValue) {
            // Attempt to read int
            var value = NSUserDefaults.StandardUserDefaults.BoolForKey(key);

            // Take action based on value
            if (value == null) {
                return defaultValue;
            } else {
                return value;
            }
        }

        public void SaveBool(string key, bool value, bool sync) {
            NSUserDefaults.StandardUserDefaults.SetBool(value, key);
            if (sync) NSUserDefaults.StandardUserDefaults.Synchronize ();
        }

        public string NSColorToHexString(NSColor color, bool withAlpha) {
            //Break color into pieces
            nfloat red=0, green=0, blue=0, alpha=0;
            color.GetRgba (out red, out green, out blue, out alpha);

            // Adjust to byte
            alpha *= 255;
            red *= 255;
            green *= 255;
            blue *= 255;

            //With the alpha value?
            if (withAlpha) {
                return String.Format ("#{0:X2}{1:X2}{2:X2}{3:X2}", (int)alpha, (int)red, (int)green, (int)blue);
            } else {
                return String.Format ("#{0:X2}{1:X2}{2:X2}", (int)red, (int)green, (int)blue);
            }
        }

        public NSColor NSColorFromHexString (string hexValue)
        {
            var colorString = hexValue.Replace ("#", "");
            float red, green, blue, alpha;

            // Convert color based on length
            switch (colorString.Length) {
            case 3 : // #RGB
                red = Convert.ToInt32(string.Format("{0}{0}", colorString.Substring(0, 1)), 16) / 255f;
                green = Convert.ToInt32(string.Format("{0}{0}", colorString.Substring(1, 1)), 16) / 255f;
                blue = Convert.ToInt32(string.Format("{0}{0}", colorString.Substring(2, 1)), 16) / 255f;
                return NSColor.FromRgba(red, green, blue, 1.0f);
            case 6 : // #RRGGBB
                red = Convert.ToInt32(colorString.Substring(0, 2), 16) / 255f;
                green = Convert.ToInt32(colorString.Substring(2, 2), 16) / 255f;
                blue = Convert.ToInt32(colorString.Substring(4, 2), 16) / 255f;
                return NSColor.FromRgba(red, green, blue, 1.0f);
            case 8 : // #AARRGGBB
                alpha = Convert.ToInt32(colorString.Substring(0, 2), 16) / 255f;
                red = Convert.ToInt32(colorString.Substring(2, 2), 16) / 255f;
                green = Convert.ToInt32(colorString.Substring(4, 2), 16) / 255f;
                blue = Convert.ToInt32(colorString.Substring(6, 2), 16) / 255f;
                return NSColor.FromRgba(red, green, blue, alpha);
            default :
                throw new ArgumentOutOfRangeException(string.Format("Invalid color value '{0}'. It should be a hex value of the form #RBG, #RRGGBB or #AARRGGBB", hexValue));
            }
        }

        public NSColor LoadColor(string key, NSColor defaultValue) {

            // Attempt to read color
            var hex = NSUserDefaults.StandardUserDefaults.StringForKey(key);

            // Take action based on value
            if (hex == null) {
                return defaultValue;
            } else {
                return NSColorFromHexString (hex);
            }
        }

        public void SaveColor(string key, NSColor color, bool sync) {
            // Save to default
            NSUserDefaults.StandardUserDefaults.SetString(NSColorToHexString(color,true), key);
            if (sync) NSUserDefaults.StandardUserDefaults.Synchronize ();
        }
        #endregion
    }
}
```

Essa classe contém rotinas de alguns auxiliares, como `SaveInt`, `LoadInt`, `SaveColor`, `LoadColor`, etc. para tornar o trabalho com `NSUserDefaults` mais fácil. Além disso, como `NSUserDefaults` não tem um modo interno para tratar `NSColors`, o `NSColorToHexString` e `NSColorFromHexString` métodos são usados para converter cores baseado na web cadeias de caracteres hexadecimais (`#RRGGBBAA` onde `AA` é a transparência alfa) que pode ser facilmente armazenadas e recuperadas.

No `AppDelegate.cs` de arquivo, crie uma instância do **AppPreferences** objeto que será usado por todos os aplicativos:

```csharp
using AppKit;
using Foundation;
using System.IO;
using System;

namespace SourceWriter
{
    [Register ("AppDelegate")]
    public class AppDelegate : NSApplicationDelegate
    {
        #region Computed Properties
        public int NewWindowNumber { get; set;} = -1;

        public AppPreferences Preferences { get; set; } = new AppPreferences();
        #endregion

        #region Constructors
        public AppDelegate ()
        {
        }
        #endregion
        
        ...
```

<a name="Wiring-Preferences-to-Preference-Views" />

### <a name="wiring-preferences-to-preference-views"></a>Preferências de fiação a preferência de modos de exibição

Em seguida, conecte-se a classe de preferência em elementos de interface do usuário na janela de preferência e modos de exibição criados acima. No construtor de Interface, selecione um controlador de exibição de preferência e alterne para o **Inspetor de identidade**, crie uma classe personalizada para o controlador: 

[![](dialog-images/prefs12.png "O Inspetor de identidade")](dialog-images/prefs12.png#lightbox)

Alterne para o Visual Studio para Mac para sincronizar suas alterações e abra a classe recém-criada para edição. Tornar a classe a seguinte aparência:

```csharp
using System;
using Foundation;
using AppKit;

namespace SourceWriter
{
    public partial class EditorPrefsController : NSViewController
    {
        #region Application Access
        public static AppDelegate App {
            get { return (AppDelegate)NSApplication.SharedApplication.Delegate; }
        }
        #endregion

        #region Computed Properties
        [Export("Preferences")]
        public AppPreferences Preferences {
            get { return App.Preferences; }
        }
        #endregion

        #region Constructors
        public EditorPrefsController (IntPtr handle) : base (handle)
        {
        }
        #endregion
    }
}
```

Observe que essa classe tem feito aqui duas coisas: primeiro, há um auxiliar `App` preferipv6addresses para ter acesso a **AppDelegate** mais fácil. Segundo, o `Preferences` propriedade expõe global **AppPreferences** classe para associação de dados com os controles de interface do usuário colocados nessa exibição.

Em seguida, clique duas vezes no arquivo de Storyboard para abri-la novamente no construtor de Interface (e ver as alterações feitas acima). Arraste os controles de interface do usuário necessários para criar a interface de preferências para a exibição. Para cada controle, alterne para o **Inspetor associação** e associar as propriedades individuais do **AppPreference** classe:

[![](dialog-images/prefs13.png "O Inspetor de associação")](dialog-images/prefs13.png#lightbox)

Repita as etapas acima para todos os painéis (controladores de exibição) e propriedades de preferência necessário.

<a name="Applying-Preference-Changes-to-All-Open-Windows" />

### <a name="applying-preference-changes-to-all-open-windows"></a>Preferência de aplicar alterações para todas as janelas abertas

Como mencionado acima, em um macOS típico do aplicativo, quando o usuário faz alterações em qualquer uma das preferências do usuário do aplicativo, essas alterações são salvas automaticamente e aplicadas a todas as janelas o usuário tenha aberto no aplicativo.

Um planejamento cuidadoso e design de preferências do aplicativo e o windows permitirá que esse processo ocorra sem problemas e transparente para o usuário final, com uma quantidade mínima de codificação de trabalho.

Para qualquer janela que consumirão preferências de aplicativo, adicione a seguinte propriedade de auxiliar para seu controlador de exibição de conteúdo para tornar a acessar nosso **AppDelegate** mais fácil:

```csharp
#region Application Access
public static AppDelegate App {
    get { return (AppDelegate)NSApplication.SharedApplication.Delegate; }
}
#endregion
```

Em seguida, adicione uma classe para configurar o conteúdo ou o comportamento com base nas preferências do usuário:

```csharp
public void ConfigureEditor() {

    // General Preferences
    TextEditor.AutomaticLinkDetectionEnabled = App.Preferences.SmartLinks;
    TextEditor.AutomaticQuoteSubstitutionEnabled = App.Preferences.SmartQuotes;
    ...

}
``` 

Você precisa chamar o método de configuração quando a janela é aberto pela primeira vez para assegurar-se de que está de acordo com as preferências do usuário:

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // Configure editor from user preferences
    ConfigureEditor ();
    ...
}
```

Em seguida, edite o `AppDelegate.cs` de arquivo e adicione o seguinte método ao aplicar qualquer preferência muda para todas as janelas abertas:

```csharp
public void UpdateWindowPreferences() {

    // Process all open windows
    for(int n=0; n<NSApplication.SharedApplication.Windows.Length; ++n) {
        var content = NSApplication.SharedApplication.Windows[n].ContentViewController as ViewController;
        if (content != null ) {
            // Reformat all text
            content.ConfigureEditor ();
        }
    }

}
```

Em seguida, adicione um `PreferenceWindowDelegate` classe ao projeto e torná-lo a aparência a seguir:

```csharp
using System;
using AppKit;
using System.IO;
using Foundation;

namespace SourceWriter
{
    public class PreferenceWidowDelegate : NSWindowDelegate
    {
        #region Application Access
        public static AppDelegate App {
            get { return (AppDelegate)NSApplication.SharedApplication.Delegate; }
        }
        #endregion

        #region Computed Properties
        public NSWindow Window { get; set;}
        #endregion

        #region constructors
        public PreferenceWidowDelegate (NSWindow window)
        {
            // Initialize
            this.Window = window;

        }
        #endregion

        #region Override Methods
        public override bool WindowShouldClose (Foundation.NSObject sender)
        {
            
            // Apply any changes to open windows
            App.UpdateWindowPreferences();

            return true;
        }
        #endregion
    }
}
```

Isso fará com que as alterações de preferência a ser enviado para todas as janelas abertas quando a preferência de janela será fechada.

Finalmente, edite o controlador de janela de preferência e adicionar o delegado criado acima:

```csharp
using System;
using Foundation;
using AppKit;

namespace SourceWriter
{
    public partial class PreferenceWindowController : NSWindowController
    {
        #region Constructors
        public PreferenceWindowController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void WindowDidLoad ()
        {
            base.WindowDidLoad ();

            // Initialize
            Window.Delegate = new PreferenceWidowDelegate(Window);
            Toolbar.SelectedItemIdentifier = "General";
        }
        #endregion
    }
}
```

Com todas essas alterações no local, se o usuário edita preferências do aplicativo e fecha a janela de preferência, as alterações serão aplicadas a todas as janelas abertas:

[![](dialog-images/prefs14.png "Uma janela de preferências de exemplo")](dialog-images/prefs14.png#lightbox)

<a name="The_Open_Dialog" />

## <a name="the-open-dialog"></a>Caixa de diálogo Abrir

A caixa de diálogo Abrir fornece aos usuários uma maneira consistente para localizar e abrir um item em um aplicativo. Para exibir uma caixa de diálogo aberta em um aplicativo Xamarin.Mac, use o seguinte código:

```csharp
var dlg = NSOpenPanel.OpenPanel;
dlg.CanChooseFiles = true;
dlg.CanChooseDirectories = false;
dlg.AllowedFileTypes = new string[] { "txt", "html", "md", "css" };

if (dlg.RunModal () == 1) {
    // Nab the first file
    var url = dlg.Urls [0];

    if (url != null) {
        var path = url.Path;

        // Create a new window to hold the text
        var newWindowController = new MainWindowController ();
        newWindowController.Window.MakeKeyAndOrderFront (this);

        // Load the text into the window
        var window = newWindowController.Window as MainWindow;
        window.Text = File.ReadAllText(path);
        window.SetTitleWithRepresentedFilename (Path.GetFileName(path));
        window.RepresentedUrl = url;

    }
}
```

No código acima, podemos está abrindo uma nova janela de documento para exibir o conteúdo do arquivo. Você precisará substituir o código com a funcionalidade é necessário pelo seu aplicativo.

As seguintes propriedades estão disponíveis ao trabalhar com um `NSOpenPanel`:

- **CanChooseFiles** - se `true` o usuário pode selecionar arquivos.
- **CanChooseDirectories** - se `true` o usuário pode selecionar diretórios.
- **AllowsMultipleSelection** - se `true` o usuário pode selecionar mais de um arquivo por vez.
- **ResolveAliases** - se `true` selecionando e alias, resolve para o caminho do arquivo original.
- **AllowedFileTypes** -é uma matriz de cadeia de caracteres de tipos de arquivo que o usuário pode selecionar qualquer uma extensão ou _utilitário_. O valor padrão é `null`, que permite que qualquer arquivo a ser aberto.

O `RunModal ()` método exibe a caixa de diálogo aberta e permitir que o usuário selecionar arquivos ou pastas (conforme especificado pelas propriedades) e retorna `1` se o usuário clica o **abrir** botão.

Caixa de diálogo Abrir retorna os arquivos selecionados ou pastas do usuário como uma matriz de URLs no `URL` propriedade.

Se executar o programa e selecione o **abrir...**  item do **arquivo** menu, o seguinte é exibido: 

[![](dialog-images/dialog03.png "Uma caixa de diálogo Abrir")](dialog-images/dialog03.png#lightbox)

<a name="The_Print_and_Page_Setup_Dialogs" />

## <a name="the-print-and-page-setup-dialogs"></a>A impressão e caixas de diálogo Configurar página

macOS fornece impressão padrão e experiência de página Instalação caixas de diálogo que seu aplicativo pode exibir para que os usuários podem ter uma impressão consistente em todos os aplicativos que eles usam.

O código a seguir mostra a caixa de diálogo de impressão padrão:

```csharp
public bool ShowPrintAsSheet { get; set;} = true;
...

[Export ("showPrinter:")]
void ShowDocument (NSObject sender) {
    var dlg = new NSPrintPanel();

    // Display the print dialog as dialog box
    if (ShowPrintAsSheet) {
        dlg.BeginSheet(new NSPrintInfo(),this,this,null,new IntPtr());
    } else {
        if (dlg.RunModalWithPrintInfo(new NSPrintInfo()) == 1) {
            var alert = new NSAlert () {
                AlertStyle = NSAlertStyle.Critical,
                InformativeText = "We need to print the document here...",
                MessageText = "Print Document",
            };
            alert.RunModal ();
        }
    }
}

```

Se definirmos o `ShowPrintAsSheet` propriedade `false`, execute o aplicativo e exibir a caixa de diálogo de impressão, será exibido o seguinte:

[![](dialog-images/print01.png "Uma caixa de diálogo de impressão")](dialog-images/print01.png#lightbox)

Se definir o `ShowPrintAsSheet` propriedade `true`, execute o aplicativo e exibir a caixa de diálogo de impressão, será exibido o seguinte:

[![](dialog-images/print02.png "Uma folha de impressão")](dialog-images/print02.png#lightbox)

O código a seguir será exibida a caixa de diálogo do Layout de página:

```csharp
[Export ("showLayout:")]
void ShowLayout (NSObject sender) {
    var dlg = new NSPageLayout();

    // Display the print dialog as dialog box
    if (ShowPrintAsSheet) {
        dlg.BeginSheet (new NSPrintInfo (), this);
    } else {
        if (dlg.RunModal () == 1) {
            var alert = new NSAlert () {
                AlertStyle = NSAlertStyle.Critical,
                InformativeText = "We need to print the document here...",
                MessageText = "Print Document",
            };
            alert.RunModal ();
        }
    }
}
```

Se definirmos o `ShowPrintAsSheet` propriedade `false`, execute o aplicativo e exibir a caixa de diálogo do layout de impressão, será exibido o seguinte:

[![](dialog-images/print03.png "Uma caixa de diálogo de configuração de página")](dialog-images/print03.png#lightbox)

Se definir o `ShowPrintAsSheet` propriedade `true`, execute o aplicativo e exibir a caixa de diálogo do layout de impressão, será exibido o seguinte:

[![](dialog-images/print04.png "Uma folha de configuração de página")](dialog-images/print04.png#lightbox)

Para obter mais informações sobre como trabalhar com a impressão e caixas de diálogo de configuração de página, consulte da Apple [NSPrintPanel](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSPrintPanel_Class/index.html#//apple_ref/doc/uid/TP40004092), [NSPageLayout](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSPageLayout_Class/index.html#//apple_ref/doc/uid/TP40004080) e [Introdução à impressão](http://sdg.mesonet.org/people/brad/XCode3/Documentation/DocSets/com.apple.adc.documentation.AppleSnowLeopard.CoreReference.docset/Contents/Resources/Documents/#documentation/Cocoa/Conceptual/Printing/Printing.html#//apple_ref/doc/uid/10000083-SW1) documentação.

<a name="The_Save_Dialog" />

## <a name="the-save-dialog"></a>A caixa de diálogo

A caixa de diálogo Salvar fornece aos usuários uma maneira consistente para salvar um item em um aplicativo.

O código a seguir mostra a caixa de diálogo padrão salvar:

```csharp
public bool ShowSaveAsSheet { get; set;} = true;
...

[Export("saveDocumentAs:")]
void ShowSaveAs (NSObject sender)
{
    var dlg = new NSSavePanel ();
    dlg.Title = "Save Text File";
    dlg.AllowedFileTypes = new string[] { "txt", "html", "md", "css" };

    if (ShowSaveAsSheet) {
        dlg.BeginSheet(mainWindowController.Window,(result) => {
            var alert = new NSAlert () {
                AlertStyle = NSAlertStyle.Critical,
                InformativeText = "We need to save the document here...",
                MessageText = "Save Document",
            };
            alert.RunModal ();
        });
    } else {
        if (dlg.RunModal () == 1) {
            var alert = new NSAlert () {
                AlertStyle = NSAlertStyle.Critical,
                InformativeText = "We need to save the document here...",
                MessageText = "Save Document",
            };
            alert.RunModal ();
        }
    }

}
```

O `AllowedFileTypes` propriedade é uma matriz de cadeia de caracteres de tipos de arquivo que o usuário pode selecionar para salvar o arquivo como. O tipo de arquivo pode ser especificado como uma extensão ou _utilitário_. O valor padrão é `null`, que permite que qualquer tipo de arquivo a ser usado.

Se definirmos o `ShowSaveAsSheet` propriedade `false`, execute o aplicativo e selecione **Salvar como...**  do **arquivo** menu será exibido o seguinte:

[![](dialog-images/save01.png "A caixa de diálogo gravação")](dialog-images/save01.png#lightbox)

O usuário pode expandir a caixa de diálogo:

[![](dialog-images/save02.png "Caixa de diálogo Salvar um expandido")](dialog-images/save02.png#lightbox)

Se definirmos o `ShowSaveAsSheet` propriedade `true`, execute o aplicativo e selecione **Salvar como...**  do **arquivo** menu será exibido o seguinte:

[![](dialog-images/save03.png "Salvar folha")](dialog-images/save03.png#lightbox)

O usuário pode expandir a caixa de diálogo:

[![](dialog-images/save04.png "Salvar um expandido folha")](dialog-images/save04.png#lightbox)

Para obter mais informações sobre como trabalhar com a caixa de diálogo Salvar, consulte da Apple [NSSavePanel](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSSavePanel_Class/index.html#//apple_ref/doc/uid/TP40004098) documentação.

<a name="Summary" />

## <a name="summary"></a>Resumo

Este artigo obteve uma visão detalhada de como trabalhar com janelas modais, planilhas e caixas de diálogo padrão do sistema em um aplicativo Xamarin.Mac. Vimos o tipos diferentes e usos de janelas modais, planilhas e caixas de diálogo, como o construtor de Interface e como trabalhar com janelas modais da criar e manter janelas modais e planilhas no Xcode, folhas e caixas de diálogo no código c#.

## <a name="related-links"></a>Links relacionados

- [MacWindows (exemplo)](https://developer.xamarin.com/samples/mac/MacWindows/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Menus](~/mac/user-interface/menu.md)
- [Windows](~/mac/user-interface/window.md)
- [Barras de ferramentas](~/mac/user-interface/toolbar.md)
- [Diretrizes de interface humana do OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [Introdução ao Windows](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)
- [Introdução às planilhas](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/Sheets/Sheets.html#//apple_ref/doc/uid/10000002i)
- [Introdução à impressão](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/Printing/osxp_aboutprinting/osxp_aboutprt.html)
